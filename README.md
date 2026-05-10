# Fedora

## aplicativos 

AppImage e Flatpak (ignora o snap) tentam resolver o mesmo problema: distribuir programas Linux sem depender tanto da distro.

Mas eles funcionam de formas bem diferentes.

# AppImage

O AppImage é o mais simples.

Você baixa um único arquivo:

```bash id="82nrf0"
programa.AppImage
```

dá permissão:

```bash id="8h4mr0"
chmod +x programa.AppImage
```

e executa.

## Características

* portátil,
* não instala no sistema,
* não precisa root,
* funciona em quase qualquer distro,
* cada app leva suas próprias dependências.

## Vantagens

* extremamente simples,
* ótimo para testar programas,
* não “suja” o sistema,
* fácil de remover (apaga o arquivo).

## Desvantagens

* atualizações menos integradas,
* integra pior com menus/temas,
* pode duplicar bibliotecas e gastar mais espaço,
* sandbox/security limitada.

## Melhor uso

* apps isolados,
* software experimental,
* versões portáteis,
* programas que você usa raramente.

---

# Flatpak

O Flatpak é quase um “mini-container” para desktop Linux mas alguns programas que precisam de acesso ao terminal podem quebrar, tipo o vscode.

Muito usado no:

* Fedora
* GNOME
* Steam Deck

## Características

* instala apps em ambiente isolado,
* usa runtimes compartilhados,
* tem permissões controladas,
* integra com loja gráfica.

## Vantagens

* mais seguro,
* atualizações automáticas,
* integração boa com desktop,
* dependências compartilhadas,
* funciona igual em várias distros.

## Desvantagens

* ocupa bastante espaço inicialmente,
* apps podem abrir mais devagar,
* sandbox às vezes atrapalha acesso a arquivos/dispositivos.

## Melhor uso

* apps desktop modernos,
* programas gráficos,
* software multiplataforma.

---


# Outras possibilidades

## RPM / DEB nativos

Os pacotes tradicionais da distro.

Fedora:

```bash id="v4zv9w"
sudo dnf install pacote
```

### Melhor integração

* mais leves,
* mais rápidos,
* melhor integração com sistema.

### Problema

* dependem da distro e versão.

---

# Compilar do código-fonte

O clássico:

```bash id="k8xygk"
./configure
make
sudo make install
```


## Vantagem

* controle total,
* máxima performance possível,
* última versão.

## Desvantagem

* manutenção manual,
* dependências,
* pode quebrar fácil.

---

# Containers

Para apps mais complexos:

* Docker
* Podman

Muito usados para backend/desenvolvimento.

No Fedora, o Podman é muito forte porque já vem integrado ao ecossistema Red Hat.

---
