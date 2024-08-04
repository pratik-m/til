# Citrix not grabbing keystokes to alt tab/win key 

link: https://askubuntu.com/questions/1385296/ubuntu-21-10-gnome-40-citrix-keyboard-combinations


I had the same issue in Citrix Workspace as well when accessing my work environment. However, your solution is switching back to Xorg and that's actually a workaround. I used this as well but kept on looking for a real solution and I am glad I found it. In https://bugs.launchpad.net/ubuntu/+source/remmina/+bug/1704870 I read that other applications had this same issue on Wayland and for these there was a solution in Gnome Settings which I couldn't find for Citrix. Next I came across: https://lists.freedesktop.org/archives/wayland-devel/2017-March/033526.html and finally https://lists.freedesktop.org/archives/wayland-devel/2017-July/034459.html where this was fixed already in july 2017 by adding two wayland protocols specifically for this.

I then found https://wiki.archlinux.org/title/citrix#Troubleshooting where 2 Gnome settings are given to set those 2 wayland protocols so keyboard shortcuts within a Citrix session work perfectly alright in Wayland. Besides setting the values through the command line, you could also install the graphical frontend called 'dconf-editor'.

```
gsettings set org.gnome.mutter.wayland xwayland-grab-access-rules "['Wfica']"
gsettings set org.gnome.mutter.wayland xwayland-allow-grabs true
```

Don't forget to write Wfica in the setting for xwayland-grab-access-rules with a capital 'W' because otherwise it doesn't work (i.e. for me at least it did not).

So no reason to fall back to Xorg anymore for Citrix as far as keyboard shortcuts are concerned.
