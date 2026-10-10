Title
[macOS] Antigravity OAuth succeeds in browser but desktop app remains stuck on "Welcome to Antigravity" — Fixed by resetting local session data
Environment
- OS: macOS (Apple Silicon)
- App: Google Antigravity 2.22.0
- Proxy: Clash Verge 2.4.2
- Proxy mode: TUN + System Proxy
- Browser: Google Chrome
Issue
When signing in to Google Antigravity:
1. Click Continue with Google.
2. Complete Google authentication in Chrome.
3. The browser redirects to:
   https://antigravity.google/auth-success?app=antigravity
4. The browser displays an authentication success message.
5. However, the Antigravity desktop application remains stuck on the welcome screen.
6. Clicking the manual redirect link does not resolve the issue.
The browser authentication succeeds, but the desktop application never completes the sign-in process.
Troubleshooting
I verified the following:
- Google authentication worked in Chrome.
- Google API endpoints were reachable through Clash Verge.
- macOS recognized the antigravity:// URL scheme.
- Running open 'antigravity://' activated the application.
- Antigravity launched successfully with proxy environment variables.
- The desktop application still failed to complete authentication.
I also checked the application logs:
~/Library/Logs/Antigravity/main.log
~/Library/Logs/Antigravity/language_server.log

No conclusive OAuth failure was identified from the available logs.
Solution
Backing up and resetting the local session data resolved the issue.
First, completely quit Antigravity using Cmd + Q.
Then run:
APP="$HOME/Library/Application Support/Antigravity"
BACKUP="$HOME/Desktop/Antigravity-Auth-Backup-$(date +%Y%m%d-%H%M%S)"

mkdir -p "$BACKUP"

for item in "Cookies" "Cookies-journal" "Local Storage" "Session Storage"; do
  if [ -e "$APP/$item" ]; then
    mv "$APP/$item" "$BACKUP/"
  fi
done

echo "Backup location: $BACKUP"

This moves the relevant session data into a backup folder on the Desktop instead of permanently deleting it.
After restarting Antigravity and signing in again with Google, the application successfully entered the main interface.
Result
Resolved in my environment.
The issue appears to have been related to local session data.
However, because multiple storage locations were reset simultaneously, I cannot determine which specific file caused the problem.
This workaround may help other macOS users experiencing the same browser-authentication-success / desktop-login-loop issue.
Notes
- Back up your data before modifying application storage.
- Do not delete the entire Antigravity configuration directory unnecessarily.
- Resetting local session data may remove existing sessions or local application state.
- Keep the backup until you confirm that the application works normally after restarting.
- This is a confirmed workaround for one installation, not a verified universal fix.# antigravity-macos-login-fix
