# BioPrint: behaviour-based login

ROOT 36 (IAC 8.0), IIT Palakkad. Track: BioPrint.

A login page that checks **how** a person types their password, not only what the password is. If someone
knows the correct password but types with a different rhythm, or is a script, the login is blocked.
There is no OTP or other fallback.

Team: Warriors( Srinija, Rishitha, Akanksha)

## Run it

1. Download or clone this folder.
2. Double-click `index.html` (opens in Chrome), or open the folder in VS Code and use the Live Server extension.
3. **Enroll** tab: pick a username and a password (8+ characters), then type that password 15 times. Repeat with a new username to add more profiles.
4. **Log in** tab: pick a profile from the **Saved profiles** list (or type its username) and log in with its password. Then let a teammate try the same username and password.
5. Watch the **live rhythm match** bar while you type your password on the Log in tab.
6. Open **Attacker demo** to try an instant-fill bot and a script that types with perfect timing.

No installs and no server. Profiles are kept in the browser's localStorage, so use "Delete all saved profiles" to start over.

## How it works

Everything is in one file, `index.html`, so it opens anywhere with no setup. Inside it there are three parts:

- the styles (the `<style>` block)
- **engine**: the scoring and bot-detection logic (search for "engine" in the file)
- **page**: keystroke capture, enrollment, login and the result screen

Signals captured for every key press: **hold time** (how long a key stays down) and the **gap** between
consecutive key presses.

1. **Enrollment.** The user types the password 15 times. For every hold time and gap we store the average
   and the spread (standard deviation).
2. **Login score.** For each feature we measure how many spreads the new attempt is away from the user's
   average (capped at 3 so one odd keystroke cannot decide everything), then average across all features.
3. **Personal threshold.** Each enrollment sample is scored against a profile built from the other samples
   ("leave one out"). The limit is the mean of those scores plus 2.5 standard deviations, so steady typists
   get a strict limit and erratic typists a looser one.
4. **Bot detection** is a separate check that runs first: script-generated key events, pasted or auto-filled
   passwords, superhuman speed, unnaturally even rhythm, and replayed timings (near-exact copy of an earlier attempt).
5. **Adaptive profile.** Accepted logins are added to the profile (newest 40 kept) so it follows slow changes.
6. **Rhythm match %.** The score is shown as a percentage: 80% or more passes. It updates live while the user types, and enrollment shows how much of the profile has been learned.
7. **Explainability.** A blocked attempt shows the three features that differed most and a chart of the gaps.

## Limitations

- TODO: write these from your own testing (for example: accuracy on a different keyboard, when tired, or when the user is nervous).
- Strictness is a trade-off. In our tests, a stricter setting blocks a friend with a similar typing style more often, but also blocks the real owner more often. TODO: add your own test results here.
- The live match bar is a demo feature. In a real system it would be hidden, because it would let an attacker tune their typing.
- Profiles live in the browser for the demo. A real system would store them on a server, with the password properly hashed and salted.

## AI tools used

TODO: fill this in honestly. For example: which AI assistant you used, which parts it helped write, and what
your team changed, tested or tuned yourselves.
