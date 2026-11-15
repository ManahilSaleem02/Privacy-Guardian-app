# Privacy Guardian – What Are You Sharing?

A client-side web application that helps users understand what personal information they share with mobile apps. Users pick an app type, review the permissions it asks for, decide to **Allow** or **Deny** each one, and get a **privacy score** with advice from a built-in **Privacy Advisor**.

Built as a student project for **Secure Software Design and Development (SSDD)**.

**Live demo:** https://manahilsaleem02.github.io/Privacy-Guardian-app/

---

## Features

- **Four app categories:** Social Media, Shopping, Gaming and Education.
- **Realistic permission lists:** each category asks for 6–7 permissions, with what the app says it uses each one for.
- **Sensitive permissions flagged:** Location, Camera, Microphone and Contacts are marked in red.
- **Need level for each permission:** *Needed*, *Optional* or *Not needed* for that type of app.
- **Allow / Deny for every permission:** all permissions start on **Deny** (least privilege).
- **Context-aware Privacy Advisor:** the advice changes with the user's choice and the app type. For example, allowing Camera on a social media app gives a mild reminder, while allowing it on a shopping app gives a high-risk warning.
- **Live privacy score:** updates as soon as a choice changes.
- **"What this app can see" panel:** black bars hide data the app can't reach and slide away when a permission is allowed.
- **Privacy report:** shows the score, risk level, permissions allowed and denied, the score calculation, recommendations and good habits.
- **Download report (.txt):** saves the report as a plain text file.
- **Optional app name field:** validated before it is used.

---

## How to use

1. **Choose an app type** (and optionally type the app's name).
2. **Review the permissions.** Sensitive ones are marked in red.
3. **Choose Allow or Deny** for each permission and read the advisor's notes.
4. Click **Generate privacy report** to see your score and recommendations.
5. Click **Download report (.txt)** to save a copy.

---

## Privacy score formula

The score starts at **100** and goes down for every permission you allow.

| Permission allowed | Points |
|---|---|
| Location (sensitive) | −20 |
| Contacts (sensitive) | −20 |
| Camera (sensitive) | −15 |
| Microphone (sensitive) | −15 |
| Any other permission (Storage, Notifications, Nearby devices, Calendar) | −5 |
| **Least privilege penalty:** a sensitive permission this app type doesn't need | −10 extra |

The final score is kept between 0 and 100.

| Score | Risk level |
|---|---|
| 80–100 | Low risk |
| 50–79 | Moderate risk |
| 0–49 | High risk |

**Example:** on a Shopping app, allowing Camera (−15, plus −10 because shopping apps don't need it), Location (−20) and Notifications (−5) gives **100 − 50 = 50 (Moderate risk)**.

---

## Permissions by category

| Permission | Social Media | Shopping | Gaming | Education |
|---|---|---|---|---|
| Location 🔴 | Optional | Optional | Not needed | Not needed |
| Camera 🔴 | Needed | Not needed | Not needed | Optional |
| Microphone 🔴 | Optional | Not needed | Optional | Optional |
| Contacts 🔴 | Optional | Not needed | Not needed | Not needed |
| Storage | Needed | Optional | Needed | Needed |
| Notifications | Optional | Optional | Optional | Optional |
| Nearby devices | – | – | Optional | – |
| Calendar | Not needed | – | – | Optional |

🔴 = sensitive permission &nbsp;&nbsp; – = not requested by this category

---

## SSDD concepts applied

| Concept | How it is applied |
|---|---|
| **Input validation** | App type, permission names and Allow/Deny values are checked against fixed allowlists. Anything else is rejected. The app name is trimmed, limited to 30 characters and allowed only letters, numbers, spaces and `. - & '`. |
| **Least privilege** | Every permission defaults to **Deny** (opt-in). Allowing a sensitive permission the app type doesn't need costs an extra 10 points, and the advisor flags it as high risk. An "Allow only what's needed" button grants the minimum. |
| **User privacy** | Everything runs in memory in the browser tab. There are no cookies, no local storage, no logging and no network requests. A Content Security Policy (`connect-src 'none'`) blocks any attempt to send data. Closing the tab erases all choices. |
| **Secure design principles** | Separation of concerns (data, validation, engine and UI layers), fail-safe defaults (anything unexpected becomes Deny), read-only data (deep-frozen objects), encapsulated state, a start-up integrity check of the built-in data, and safe output (text is inserted with `textContent`, never `innerHTML`). |
| **Security requirements** | SR-1 to SR-6, listed below. |

### Security requirements

| ID | Requirement |
|---|---|
| SR-1 | Every permission starts on Deny (opt-in only). |
| SR-2 | Only the four app types and the values Allow and Deny are accepted. |
| SR-3 | Invalid or unknown input is rejected and fails safe to Deny. |
| SR-4 | No choice is stored, logged or sent off the device. |
| SR-5 | Typed text is shown as plain text and can never run as code. |
| SR-6 | Permission data and scoring rules can't be changed while the app runs. |

In the code, every place a concept is applied has a comment starting with `[SSDD:`. Search for it to find them.

---

## Code structure

The whole app is one file, `index.html`. The script inside it is split into four layers:

| Layer | Responsibility |
|---|---|
| **1. Data** | Read-only catalogue of permissions, app categories, scoring rules and advice. |
| **2. Validation** | Allowlist checks for categories, permissions, Allow/Deny values and the app name. |
| **3. Privacy Engine** | Private state, score calculation and the Privacy Advisor. Contains no page (DOM) code. |
| **4. UI** | Draws the page and handles clicks. It never calculates scores itself; it asks the engine. |

```
privacy-guardian/
├── index.html   # the complete app (HTML, CSS and JavaScript)
└── README.md    # this file
```

---

## Tech stack

- HTML5
- CSS3 (no frameworks)
- Vanilla JavaScript (ES6+)
- No libraries, no build step, no backend
- Hosted on GitHub Pages

---

## Run locally

**Option 1:** double-click `index.html` to open it in any modern browser.

**Option 2 (VS Code):**
1. Open the project folder in VS Code.
2. Install the **Live Server** extension.
3. Right-click `index.html` and choose **Open with Live Server**.
