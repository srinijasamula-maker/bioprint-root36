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

**Accuracy**
- **Similar typists can get through.** In our testing, a friend with a similar typing speed and rhythm was sometimes accepted. Behavioral matching cannot reliably separate two people who genuinely type alike.
- **Strictness is a trade-off.** A stricter setting blocks look-alike typists more often, but it also blocks the real owner more often. We chose **[Strict / Balanced / Relaxed]** after testing.
- **Our test results:** **[e.g. "Owner: 5 attempts, 4 accepted. Friend with the correct password: 5 attempts, 2 accepted."]** These come from a small number of people, so they are not a proper statistical evaluation.
- **Typing changes.** Tiredness, stress, a different keyboard, a laptop versus a desktop, or an injured hand can change someone's rhythm and cause the genuine user to be blocked.
- **Needs enough data.** The system needs a password of at least 8 characters and 15 enrollment samples, typed at a natural pace. A rushed or careless enrollment gives a weak profile.

**Usability**
- **No corrections.** Backspace and Delete are not allowed during an attempt, because they would distort the timing. The user has to retype from scratch.
- **Pasting and auto-fill are treated as bots.** A genuine user who uses a password manager or pastes their password would be blocked.
- **Keyboard only.** We only capture keyboard timing. Touchscreens and other input methods are not supported, and a profile enrolled on one keyboard may not match another.

**Bot detection**
- **Simple rules only.** We detect script-generated events, pasting, superhuman speed, unnaturally even timing, and replayed timings. A more advanced bot that adds random human-like variation could pass these checks, although it would still need to match the owner's specific rhythm.

**Security and design (this is a demo prototype)**
- **All checks run in the browser.** Profiles are stored in the browser's localStorage, and the decision is made by JavaScript on the page. Anyone with access to the browser could read or edit them. A real system would store profiles and make decisions on a server.
- **Basic password handling.** The password is stored only as a plain hash for the demo. A real system would use a salted, slow hash on a server.
- **No lockout or rate limiting.** There is nothing stopping repeated login attempts.
- **The live match bar helps attackers.** It is shown as a demo feature and can be switched off. In a real system it would be hidden, since an attacker could use it to practice until they pass.
- **Accessibility.** People with motor differences may type less consistently, so this method may not be fair to everyone.

## AI tools used

We used **Claude (by Anthropic)** as our only AI tool during this hackathon.

**What Claude helped with:**
- Writing the code for the login page, including the keystroke timing capture, the enrollment flow, the scoring and bot-detection logic, and the page design
- Explaining how behavioral biometrics and keystroke dynamics work
- Guiding us through Git, GitHub and setting up the repository

**What our team did ourselves:**
- Tested the system with real typing: enrolled a profile and tried logins by the genuine user and by a friend who typed the correct password
- Found that a friend with a similar typing speed was accepted, and asked for a stricter check. This led to the Strictness setting, the 80% pass mark, and 15 enrollment samples
- 
We read through the code and can explain how it works. Every part of the project was created during the event window, and the commit history shows our work over time.
