# Documentação do setup Pi-hole + Tailscale como DNS

## Objetivo

Usar o Pi-hole como DNS em todos os dispositivos com Tailscale:

* dentro da rede de casa, usando o Pi-hole;
* fora da rede de casa, usando o Pi-hole via Tailscale;
* sem depender do DNS do router atual.

---

## Rede identificada

Servidor Pi-hole:

```text
LAN:       192.168.1.100/24
Tailscale: 100.85.237.37
Interface LAN: eth0
Interface VPN: tailscale0
```

Dispositivo remoto testado:

```text
notebook1: 100.93.92.40
```

---

## Estado inicial descoberto

O Pi-hole respondia corretamente pela LAN:

```bash
nslookup google.com 192.168.1.100
```

Funcionava.

Mas não respondia via Tailscale:

```bash
nslookup google.com 100.85.237.37
```

Dava timeout.

---

## Configuração do Pi-hole/dnsmasq

Foi criado/ajustado:

```bash
/etc/dnsmasq.d/02-tailscale.conf
```

Conteúdo corrigido:

```conf
interface=tailscale0
interface=eth0

listen-address=127.0.0.1
listen-address=100.85.237.37
listen-address=192.168.1.100
```

Havia um erro antes:

```conf
listen-address=192.168.1.
```

Isso era inválido. O correto é usar o IP completo:

```conf
listen-address=192.168.1.100
```

---

## Verificação de escuta do Pi-hole

Foi executado:

```bash
sudo ss -tulnp | grep :53
```

Resultado importante:

```text
0.0.0.0:53
[::]:53
```

Conclusão:

O Pi-hole estava a escutar em todas as interfaces, incluindo:

```text
eth0
tailscale0
```

Portanto, o problema não era o Pi-hole não estar a escutar.

---

## Configuração necessária no Pi-hole Admin

No painel do Pi-hole:

```text
Settings → DNS → Interface listening behavior
```

Foi necessário usar:

```text
Listen on all interfaces, permit all origins
```

Motivo:

A interface `tailscale0` aparece como `/32`:

```text
100.85.237.37/32
```

Por isso, outros peers Tailscale podem ser tratados como “não locais” pelo Pi-hole. Com “permit all origins”, o Pi-hole aceita queries vindas da rede Tailscale.

A segurança deve ser feita no firewall, não nessa opção do Pi-hole.

---

## Tailscale ACL

As ACLs estavam permissivas:

```json
{
  "src": ["*"],
  "dst": ["*"],
  "ip": ["*"]
}
```

Conclusão:

O problema não era ACL do Tailscale.

---

## Testes Tailscale

Do servidor Pi-hole:

```bash
tailscale status
```

Mostrou os peers, incluindo:

```text
100.85.237.37    pihole
100.93.92.40     notebook1
```

Do notebook remoto:

```bash
ping 100.85.237.37
```

Funcionou.

Também foi testado TCP/53:

```bash
nc -zv 100.85.237.37 53
```

Funcionou.

Mas DNS continuava a falhar inicialmente porque DNS usa UDP/53 na maior parte dos casos.

---

## Firewall/UFW

Foi descoberto que o UFW estava ativo e com regras que bloqueavam tráfego necessário.

Estado depois de limpar:

```bash
sudo ufw status verbose
```

Resultado:

```text
Status: active
Default: deny (incoming), allow (outgoing), disabled (routed)

53      ALLOW IN    192.168.1.0/24
53      ALLOW IN    100.64.0.0/10
22/tcp  ALLOW IN    Anywhere
```

Isto permitia DNS, mas bloqueava outros serviços que também precisavam funcionar na LAN.

Conclusão:

Permitir só a porta 53 é demasiado restritivo para este servidor se ele também precisa responder a outros serviços internos, como painel web, SSH, etc.

---

## Decisão final sobre firewall

Foi concluído:

* Com firewall desligada, tudo funciona.
* Com firewall restritiva por porta, muita coisa quebra.
* Melhor abordagem para este caso doméstico/homelab: permitir redes confiáveis inteiras, não apenas portas específicas.

Configuração recomendada se quiser usar UFW:

```bash
sudo ufw reset
sudo ufw default deny incoming
sudo ufw default allow outgoing

sudo ufw allow from 192.168.1.0/24
sudo ufw allow from 100.64.0.0/10
sudo ufw allow 22/tcp

sudo ufw enable
```

Isto significa:

```text
LAN inteira pode aceder ao servidor
Tailscale inteiro pode aceder ao servidor
Outras origens são bloqueadas
```

---

## Configuração DNS do sistema no Pi-hole

O ficheiro:

```bash
/etc/resolv.conf
```

Mostrava:

```text
nameserver 100.100.100.100
nameserver fd7a:115c:a1e0::53
```

Isto é o DNS interno do Tailscale. É normal quando MagicDNS/Override DNS está ativo.

---

## Configuração estática local

Foi visto:

```bash
/etc/dhcp.conf
```

Conteúdo:

```conf
interface eth0
static ip_address=192.168.1.100/24
static routers=192.168.1.1
static domain_name_servers=192.168.1.1
```

Interpretação:

* IP fixo do Pi-hole: `192.168.1.100`
* Gateway/router: `192.168.1.1`
* DNS usado pelo próprio servidor: `192.168.1.1`

Isso não impede o Pi-hole de servir DNS para outros dispositivos.

---

## Configuração final esperada no Tailscale Admin

No painel do Tailscale DNS:

```text
Nameserver: 100.85.237.37
Override local DNS: enabled
```

Resultado esperado:

Todos os dispositivos com Tailscale passam a usar o Pi-hole como DNS, estejam dentro ou fora da rede de casa.

---

## Testes finais recomendados

No servidor Pi-hole:

```bash
nslookup google.com 192.168.1.100
```

Deve funcionar.

Num cliente Tailscale fora da LAN:

```bash
nslookup google.com 100.85.237.37
```

Deve funcionar.

Num dispositivo normal da LAN:

```bash
nslookup google.com 192.168.1.100
```

Deve funcionar.

Verificar se o Pi-hole está a escutar:

```bash
sudo ss -tulnp | grep :53
```

Esperado:

```text
0.0.0.0:53
[::]:53
```

---

## Configuração final recomendada

Pi-hole:

```text
Listen on all interfaces, permit all origins
```

Tailscale DNS:

```text
Nameserver: 100.85.237.37
Override local DNS: enabled
```

UFW, se ativado:

```bash
sudo ufw reset
sudo ufw default deny incoming
sudo ufw default allow outgoing

sudo ufw allow from 192.168.1.0/24
sudo ufw allow from 100.64.0.0/10
sudo ufw allow 22/tcp

sudo ufw enable
```

Sem UFW:

```bash
sudo ufw disable
```

Funciona, mas com menos controlo de segurança.

---

## Conclusão

O problema principal não era o Tailscale nem o Pi-hole.

Foram encontrados três pontos importantes:

1. O Pi-hole precisava aceitar queries de todas as origens, porque os peers Tailscale não eram vistos como locais.
2. O Pi-hole estava corretamente a escutar em `0.0.0.0:53`.
3. A firewall UFW estava a bloquear tráfego necessário, especialmente quando configurada de forma demasiado restritiva por porta.

A solução funcional é:

```text
Pi-hole aberto para todas as interfaces
Tailscale força DNS para 100.85.237.37
Firewall, se usada, permite LAN + Tailscale inteiras
```
