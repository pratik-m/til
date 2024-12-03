# Check for conflicting keybindings in Ubuntu 

I have been trying my hands on various extension/tweaks in Ubuntu 24 to meet my workflow requirements. After a while a few keybindings did not work as expected. I suspected there might be conflicting keybidings causing them not to work. By using the below command I was able to find the conflicting keybindings and disabled them 


** Find keybindings **
```bash
gsettings list-recursively | grep "<super>"
```

** Disable keybindings ** 
```bash
gsettings set org.gnome.shell.extensions.dash-to-dock app-shift-hotkey-8 []
```

