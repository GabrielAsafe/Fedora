#para resetar as configs do gnome 

dconf reset -f /

#depois tem que dar logoff


#para desistalar os pacotes 
sudo dnf autoremove
sudo dnf clean all
sudo dnf distro-sync
