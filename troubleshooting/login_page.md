# solving the problem of login into arch linux

# problem statement
```
You use the maldives theme for the login screen (SDDM). After two months without updates, the full upgrade moved SDDM to a newer Qt version,
and maldives is a third-party theme that doesn’t work with it. When the login screen tried to load it, it crashed after about 3 seconds,
so you got a black screen with a dash. Your system, files, apps and desktop were never broken.
```
# solution
```
- grep -rn "Current" /etc/sddm.conf /etc/sddm.conf.d/ 2>/dev/null
cat /etc/sddm.conf.d/kde_settings.conf
#if it was like maldives -> so you need to set it to the defualt session(breeze) that is came with sddm(simple disktop display manager) when updating the entire system
sudo tee /etc/sddm.conf.d/theme.conf <<'EOF'
[Theme]
Current=breeze
EOF
#now reboot the system
sudo reboot  # then login with your stuff by breeze and you are now done

```
# some commands to debug
```
#in case you can't open the login session (kde-plasme) after startup
ctrl+Alt+f3 #login to your system through the command lines (this is important)
systemctl --failed       #check failure then disable it or try to see it's packages again and reinstall it again
startplasma-wayland  #now open the login session manually if your system running correctly it will open the whole session but don't stop here you need to make it open automatically once you start the laptop not you
#in order to check your sddm runs and your login shell
grep Exec /usr/share/wayland-sessions/plasma.desktop
getent passwd abdo | cut -d: -f7
#check logs of your current session
journalctl -b _UID=$(id -u) --no-pager | tail -60
journalctl -b --no-pager | grep -iE "sddm-helper|pam_|logind" | tail -25
#check the status of sddm then restart it 
  sudo systemctl status sddm
#
sudo systemctl restart sddm
```
