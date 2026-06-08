#salve o ficheiro como: chmod +x enviar.sh

#!/bin/bash

# Verifica se foi passado um argumento
if [ $# -ne 1 ]; then
    echo "Uso: $0 <arquivo>"
    exit 1
fi

ARQUIVO="$1"
DESTINO="gabriel@100.79.140.54:~/"

# Verifica se o arquivo existe
if [ ! -f "$ARQUIVO" ]; then
    echo "Erro: arquivo não encontrado: $ARQUIVO"
    exit 1
fi

scp "$ARQUIVO" "$DESTINO"

#modo de usar
./enviar.sh "/home/gabriel/Transferências/Hoppers 2026.mp4"







ou

#!/bin/bash

if [ $# -lt 2 ]; then
    echo "Uso: $0 <arquivo> <usuario@host> [destino]"
    exit 1
fi

ARQUIVO="$1"
HOST="$2"
DESTINO="${3:-~/}"

scp "$ARQUIVO" "$HOST:$DESTINO"


#modo de usar

./enviar.sh "/home/gabriel/Transferências/Hoppers 2026.mp4" gabriel@100.79.140.54
