# How to install packettracer on Arch linux


- go to Cisco website: install ubuntu version 
	- https://www.netacad.com/resources/lab-downloads?courseLang=en-US
- if you have : yay (AUR package manager)
	- move this file that you downloaded thereon your downloads to yay repo on your system
		- cp ~/Downloads/Packet Tracer 9.0.1 Ubuntu 64bit  ~/.cache/yay
	- now install it using yay on your system
		-" yay -S packettracer "
- to make sure that everything work well
	- which packettracer 
		- answer:/usr/lib/packettracer/packettracer.AppImage
	- now in order to make a command for packettracer to run it in everywhere on your system (use soft link idea)
		- sudo ln -s /usr/lib/packettracer/packettracer.AppImage /usr/local/bin/packettracer
			- "we will link the original file path under the bin directly (once your execuable file under the bin you can run it easily)"
			- test in konsole:  packettracer
	- now if it's opening and you can't login due to no browser opens for you just do the following:
		- We know the exact problem:

```text
Packet Tracer → Cisco Qt 6.8
                  ↓
             kde-open
                  ↓
        tries to use Cisco Qt 6.8
                  ↓
        KDE requires Qt 6.11 ❌
```

. Create the private browser launcher

Run:

```bash
mkdir -p ~/.local/lib/packettracer-bin
```

Then:

```bash
cat > ~/.local/lib/packettracer-bin/kde-open <<'EOF'
#!/bin/bash
unset LD_LIBRARY_PATH
exec /usr/bin/firefox "$@"
EOF

```

Make it executable:

```bash
chmod +x ~/.local/lib/packettracer-bin/kde-open
```

Test it:

```bash
~/.local/lib/packettracer-bin/kde-open https://www.google.com
```

# 2. Create our Packet Tracer launcher

Now:

```bash
mkdir -p ~/.local/bin
```

Create:

```bash
cat > ~/.local/bin/packettracer <<'EOF'
#!/bin/bash

PT_APP="/usr/lib/packettracer/packettracer.AppImage"
PT_BIN="$HOME/.local/lib/packettracer-bin"

export PATH="$PT_BIN:$PATH"

exec "$PT_APP" "$@"
EOF
```

Then:

```bash
chmod +x ~/.local/bin/packettracer
```

Make sure your local bin directory is in PATH:

```bash
echo "$PATH" | tr ':' '\n' | grep -Fx "$HOME/.local/bin"
```

If it prints:

```text
/home/user/.local/bin
```

you're good.

If it prints nothing, run:

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

1. The important files we created

Keep this mental map:

```text
~/.local/
├── bin/
│   └── packettracer
│
└── lib/
    └── packettracer-bin/
        └── kde-open
```

System/AUR-managed application:

```text
/usr/lib/packettracer/
└── packettracer.AppImage
```

So your architecture is:

```text
You
 │
 │ packettracer
 ▼
~/.local/bin/packettracer
 │
 │ sets PATH for browser helper
 ▼
/usr/lib/packettracer/packettracer.AppImage
 │
 │
 └── when login needs browser
          │
          ▼
 ~/.local/lib/packettracer-bin/kde-open
          │
          │ removes Packet Tracer's LD_LIBRARY_PATH
          ▼
     /usr/bin/firefox
```

Our workaround:

```
Packet Tracer
    │
    │ Packet Tracer Qt
    ▼
our kde-open wrapper
    │
    │ unset LD_LIBRARY_PATH(this reclose these libraries that were responsible for all of these error instead we will use our normal default through firefox)
    ▼
Firefox
    │
    │ normal system libraries
    ▼
Arch Linux Qt/system libraries
```
