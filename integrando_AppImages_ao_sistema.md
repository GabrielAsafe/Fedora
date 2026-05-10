mkdir -p ~/Applications

mv programa.AppImage ~/Applications/

chmod +x ~/Applications/programa.AppImage


# baixa um ícone png ou svf e mete na pasta de icones 

mkdir -p ~/.local/share/icons


# cria o arquivo de desktop  - mude para o nome do app

nano ~/.local/share/applications/programa.desktop


[Desktop Entry]
Name=ESCREVA O NOME DA PORRA DO PROGRAMA AQUI !!!!!
Exec=/home/SEU_USUARIO/Applications/programa.AppImage
Icon=/home/SEU_USUARIO/.local/share/icons/programa.png
Type=Application
Categories=Utility;
Terminal=false



# de permissões
chmod +x ~/.local/share/applications/programa.desktop



# atualize o menu

update-desktop-database ~/.local/share/applications
