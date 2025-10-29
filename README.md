# tmux Cheats

Schnellreferenz für `tmux` – Sessions, Windows (Tabs), Panes (Splits), Copy-Mode, Maus, Plugins & eine kompakte `.tmux.conf`.

---

## 🚀 Quickstart

```bash
# Installation (Ubuntu/Mint)
sudo apt update && sudo apt install -y tmux

# Neue Session starten
tmux new -s work

# Sessions anzeigen / anhängen / lösen
tmux ls
tmux attach -t work
# In tmux: Prefix (Ctrl-b), dann d  -> detach (Session läuft weiter)


🧭 Sessions (Workspaces)

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

🗂️ Windows (Tabs) innerhalb einer Session

Keys (mit Prefix Ctrl-b):

c → neues Window

, → Window umbenennen

p / n → prev/next Window

Zahl 0..9 → direkt zu Window

& → Window schließen

# CLI-Variante
tmux new-window                 # neues Window
tmux list-windows               # alle Windows auflisten
tmux select-window -t 2         # zu Window 2 springen
tmux rename-window -t 2 build   # Window 2 umbenennen
tmux kill-window -t 2


🪟 Panes (Splits) im Window

Keys (mit Prefix Ctrl-b):

" → horizontal split

% → vertikal split

Pfeile → Pane wechseln

x → Pane killen

o → zum nächsten Pane

Ctrl-b + : → Befehls-Prompt

Resizing (Standard):

Ctrl-b + Alt + Pfeile → Größe ändern (je nach Terminal)
# CLI-Variante
tmux split-window -h     # horizontal (links/rechts)
tmux split-window -v     # vertikal (oben/unten)
tmux select-pane -L|R|U|D
tmux swap-pane -U|-D
tmux resize-pane -L|R|U|D 5

📋 Copy-Mode (Scrollen & Kopieren)

Keys (mit Prefix Ctrl-b):

[ → Copy-Mode starten (Scrollen mit Pfeilen/PgUp/PgDn)

/ → Suche vorwärts, ? rückwärts

Space → Start Markierung, Enter → kopieren

] → Einfügen aus tmux-Buffer

# Pane-Inhalt schnell in Datei sichern (ohne Copy-Mode)
tmux capture-pane -S -10000 -p > pane.log

🖱️ Maus-Support

Scrollen, Klick-Wechsel, Pane-Größen ziehen

In .tmux.conf: set -g mouse on (siehe unten)

🔁 Panes synchronisieren

Alle Tastatureingaben in alle Panes des Windows spiegeln:
# Temporär (Prompt mit Ctrl-b :)
setw synchronize-panes on
# wieder aus:
setw synchronize-panes off

📦 Buffers & Clipboard

# Puffer anzeigen
tmux list-buffers
tmux show-buffer -b 0

# Aktuelle Auswahl in Datei
tmux save-buffer -b 0 /tmp/tmux-buffer.txt

# Datei in tmux-Buffer laden, dann mit ] einfügen
tmux load-buffer /path/file.txt

⚙️ Minimale .tmux.conf

Lege die Datei unter ~/.tmux.conf an, danach: tmux source-file ~/.tmux.conf
##### Prefix (optional zu Ctrl-a ändern)
#set -g prefix C-a
#unbind C-b
#bind C-a send-prefix

##### Maus & Historie
set -g mouse on
set -g history-limit 100000

##### Farben/Truecolor (falls nötig)
set -ga terminal-overrides ',xterm-256color:Tc'

##### Statuszeile (einfach)
set -g status-bg colour237
set -g status-fg white
set -g status-left " #S "
set -g status-right " %Y-%m-%d %H:%M "

##### Splits bequem (| und -)
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %
bind r source-file ~/.tmux.conf \; display "Reloaded ~/.tmux.conf"

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

🔌 Plugins (optional, via TPM)

TPM installieren:
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
In ~/.tmux.conf ergänzen:
# TPM (am Ende der Datei)
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-resurrect'   # Sessions/Layouts speichern
run '~/.tmux/plugins/tpm/tpm'

🧰 Nützliche Einzeiler
# Neue oder vorhandene "work"-Session:
tmux attach -t work || tmux new -s work

# Alle DETACHED Sessions killen
tmux ls | awk '/(detached)/{print $1}' | sed 's/:$//' | xargs -r -I{} tmux kill-session -t {}

# Aktuelles Window als Layout merken (ID ausgeben)
tmux display-message "#{window_active}:#{window_layout}"

# Pane-Inhalt (letzte 2000 Zeilen) anzeigen
tmux capture-pane -S -2000 -p

🛠️ Troubleshooting

Farben kaputt? Achte darauf, dass dein Terminal xterm-256color nutzt und in .tmux.conf Truecolor gesetzt ist (siehe oben).

Alt/Meta funktioniert nicht? In Terminal-Einstellungen „Meta senden als Esc“ aktivieren.

Maus geht nicht? set -g mouse on in .tmux.conf und tmux source-file ~/.tmux.conf.

