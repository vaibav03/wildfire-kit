# Message to send to players

Copy everything in the box below and send it to players. They paste it into Claude Code (any folder). Codex players: see the second box below.

```
Set me up for the Wildfire game. In this folder run: git clone https://github.com/vaibav03/wildfire-kit.git
Then check that Miro is connected here (any Miro tool: the claude.ai Miro connector or the Miro plugin both work). If not, tell me exactly how, either: claude.ai → Settings → Connectors → Miro → Connect, or in Claude Code: /plugin install miro@claude-plugins-official, /reload-plugins, then /mcp → miro → Authenticate. Pick the CIS 591 team.
Also remind me to accept the instructor's Miro board invite so I can edit the board.
Then tell me: slash commands only load when Claude Code starts inside the kit, so quit and run
  cd wildfire-kit && claude
and in there type /join Your Name (first name + last initial), then /play when the instructor says go.
```

## Codex players

Players who use Codex instead paste this into Codex (any folder):

```
Set me up for the Wildfire game. In this folder run: git clone https://github.com/vaibav03/wildfire-kit.git
Then check that Miro is connected in Codex (any Miro tool works). If not, run: codex mcp add miro --url https://mcp.miro.com/
and then tell me to run: codex mcp login miro   (sign in to Miro and pick the CIS 591 team). In the Codex app, Plugins → Miro → Add also works.
Also remind me to accept the instructor's Miro board invite so I can edit the board.
Then tell me: the game's skills load when Codex starts inside the kit, so quit and run
  cd wildfire-kit && codex
say yes when Codex asks to trust the folder, then type $join Your Name (first name + last initial), then $play when the instructor says go.
```
