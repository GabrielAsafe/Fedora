# Criar novo utilizador 

useradd -m tempadmin
passwd tempadmin
usermod -aG wheel tempadmin // wheel é o grupo de sudo


sudo userdel -r tempadmin

# Editar nome de user

sudo usermod -l gabriel gabrie
sudo usermod -d /home/gabriel -m gabriel
sudo groupmod -n gabriel gabrie
