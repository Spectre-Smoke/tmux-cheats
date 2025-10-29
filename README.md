# tmux Cheats

Schnellreferenz für `tmux` – Sessions, Windows (Tabs), Panes (Splits), Copy-Mode, Maus, Plugins & eine kompakte `.tmux.conf`.

**Inhalt:**  
[Quickstart](#quickstart) · [Sessions](#sessions-workspaces) · [Windows](#windows-tabs-innerhalb-einer-session) · [Panes/Splits](#panes-splits-im-window) · [Copy-Mode](#copy-mode-scrollen--kopieren) · [Maus](#maus-support) · [Panes sync](#panes-synchronisieren) · [Buffers](#buffers--clipboard) · [tmux.conf (minimal)](#minimale-tmuxconf) · [TPM/Plugins](#plugins-optional-via-tpm) · [Nuetzliche Einzeiler](#nuetzliche-einzeiler) · [Troubleshooting](#troubleshooting)

---

## 🚀 Quickstart  {#quickstart}

~~~bash
# Installation (Ubuntu/Mint)
sudo apt update && sudo apt install -y tmux

# Neue Session starten
tmux new -s work

# Sessions anzeigen / anhängen
tmux ls
tmux attach -t work
# In tmux: Prefix (Ctrl-b), dann d  -> detach (Session läuft weiter)
~~~

---

## 🧭 Sessions (Workspaces)  {#sessions-workspaces}

~~~bash
# Neue Session
tmux new -s NAME

# Anhängen / Wechseln
tmux attach -t NAME
tmux switch-client -t NAME

# Umbenennen / Beenden
tmux rename-session -t ALTNAME NEUNAME
tmux kill-session -t NAME

# Praktischer Einzeiler: "anhängen oder neu"
tmux attach -t work || tmux new -s work
~~~

---

## 🗂️ Windows (Tabs) innerhalb einer Session  {#windows-tabs-innerhalb-einer-session}

~~~text
# Keys (mit Prefix Ctrl-b):
c           -> neues Window
,           -> Window umbenennen
p / n       -> prev/next Window
0..9        -> direkt zu Window
&           -> Window schließen
~~~

~~~bash
# CLI-Variante
tmux new-window                 # neues Window
tmux list-windows               # alle Windows auflisten
tmux select-window -t 2         # zu Window 2 springen
tmux rename-window -t 2 build   # Window 2 umbenennen
tmux kill-window -t 2
~~~

---

## 🪟 Panes (Splits) im Window  {#panes-splits-im-window}

~~~text
# Keys (mit Prefix Ctrl-b):
"           -> horizontal split
%           -> vertikal split
Pfeile      -> Pane wechseln
x           -> Pane killen
o           -> zum nächsten Pane
Ctrl-b :    -> Befehls-Prompt
~~~

~~~bash
# Resizing (Standard – je nach Terminal):
# Ctrl-b + Alt + Pfeile

# CLI-Variante
tmux split-window -h            # horizontal (links/rechts)
tmux split-window -v            # vertikal (oben/unten)
tmux select-pane -L             # -R | -U | -D
tmux swap-pane -U               # oder -D
tmux resize-pane -L 5           # -R | -U | -D 5
~~~

---

## 📋 Copy-Mode (Scrollen & Kopieren)  {#copy-mode-scrollen--kopieren}

~~~text
# Keys (mit Prefix Ctrl-b):
[           -> Copy-Mode starten (Scrollen mit Pfeilen/PgUp/PgDn)
/           -> Suche vorwärts, ? rückwärts
Space       -> Start Markierung, Enter -> kopieren
]           -> Einfügen aus tmux-Buffer
~~~

~~~bash
# Pane-Inhalt schnell in Datei sichern (ohne Copy-Mode)
tmux capture-pane -S -10000 -p > pane.log
~~~

---

## 🖱️ Maus-Support  {#maus-support}

~~~bash
# Scrollen, Klick-Wechsel, Pane-Größen ziehen:
# In .tmux.conf aktivieren:
set -g mouse on
~~~

---

## 🔁 Panes synchronisieren  {#panes-synchronisieren}

~~~bash
# Alle Tastatureingaben in alle Panes des Windows spiegeln
setw synchronize-panes on
# wieder aus:
setw synchronize-panes off
~~~

---

## 📦 Buffers & Clipboard  {#buffers--clipboard}

~~~bash
# Puffer anzeigen
tmux list-buffers
tmux show-buffer -b 0

# Aktuelle Auswahl in Datei
tmux save-buffer -b 0 /tmp/tmux-buffer.txt

# Datei in tmux-Buffer laden, dann mit ] einfügen
tmux load-buffer /path/file.txt
~~~

---

## ⚙️ Minimale .tmux.conf  {#minimale-tmuxconf}

Diese Datei unter `~/.tmux.conf` anlegen, dann neu laden mit:
`tmux source-file ~/.tmux.conf`

~~~tmux
##### Prefix (optional zu Ctrl-a ändern)
# set -g prefix C-a
# unbind C-b
# bind C-a send-prefix

##### Maus & Historie
set -g mouse on
set -g history-limit 100000

##### Farben/Truecolor (falls nötig)
set -ga terminal-overrides ",xterm-256color:Tc"

##### Statuszeile (einfach)
set -g status-bg colour237
set -g status-fg white
set -g status-left " #S "
set -g status-right " %Y-%m-%d %H:%M "

##### Splits bequem (| und -)
bind '|' split-window -h
bind '-' split-window -v
unbind '"'
unbind %
bind r source-file ~/.tmux.conf \; display-message "Reloaded ~/.tmux.conf"

##### Pane-Navigation mit Vim-Style (h j k l)
bind -r h select-pane -L
bind -r j select-pane -D
bind -r k select-pane -U
bind -r l select-pane -R

##### Resize mit Shift+Pfeil
bind -r S-Left  resize-pane -L 3
bind -r S-Right resize-pane -R 3
bind -r S-Up    resize-pane -U 2
bind -r S-Down  resize-pane -D 2
~~~

---

## 🔌 Plugins (optional, via TPM)  {#plugins-optional-via-tpm}

~~~bash
# TPM installieren
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
~~~

In `~/.tmux.conf` ergänzen (am Ende):

~~~tmux
# TPM
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-resurrect'   # Sessions/Layouts speichern
run -b '~/.tmux/plugins/tpm/tpm'
~~~

---

## 🧰 Nuetzliche Einzeiler  {#nuetzliche-einzeiler}

~~~bash
# Neue oder vorhandene "work"-Session
tmux attach -t work || tmux new -s work

# Alle DETACHED Sessions killen
tmux ls | awk '/(detached)/{print $1}' | sed 's/:$//' | xargs -r -I{} tmux kill-session -t {}

# Aktuelles Window als Layout (ID ausgeben)
tmux display-message "#{window_active}:#{window_layout}"

# Pane-Inhalt (letzte 2000 Zeilen) anzeigen
tmux capture-pane -S -2000 -p
~~~

---

## 🛠️ Troubleshooting  {#troubleshooting}

- **Farben kaputt?** Terminal auf `xterm-256color` stellen und in `.tmux.conf` Truecolor (`terminal-overrides`) setzen.  
- **Alt/Meta geht nicht?** In den Terminal-Einstellungen „Meta als Esc senden“ aktivieren.  
- **Maus reagiert nicht?** `set -g mouse on` in `.tmux.conf` und danach `tmux source-file ~/.tmux.conf`.