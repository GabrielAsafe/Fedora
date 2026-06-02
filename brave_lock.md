ps aux | grep brave

pkill -9 brave

rm -f ~/.config/BraveSoftware/Brave-Browser/SingletonLock
rm -f ~/.config/BraveSoftware/Brave-Browser/SingletonCookie
rm -f ~/.config/BraveSoftware/Brave-Browser/SingletonSock

ls -la ~/.config/BraveSoftware/Brave-Browser/Singleton*
