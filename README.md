# Caro Game – Client

The **desktop game client** of a multiplayer **Caro (Gomoku / Five-in-a-row)** game, written in Java with Swing. Players register, log in, chat, make friends, create or join rooms and play against each other online – or play offline against a built-in **AI**.

> 👉 To play online you also need the **server**: **Caro_Server** (https://github.com/NhuNguyen0101/Caro-Server). The server must be running **before** you start the client. The offline "Play with computer" mode needs the server only to log in (you cannot reach the home screen without logging in).

---

## Table of contents

1. [Features](#1-features)
2. [Game rules](#2-game-rules)
3. [Tech stack](#3-tech-stack)
4. [What you need to install](#4-what-you-need-to-install-prerequisites)
5. [Step-by-step setup](#5-step-by-step-setup)
6. [Running the client](#6-running-the-client)
7. [How to play – quick walkthrough](#7-how-to-play--quick-walkthrough)
8. [Connecting to a server on another computer](#8-connecting-to-a-server-on-another-computer)
9. [Voice chat](#9-voice-chat)
10. [Project structure](#10-project-structure)
11. [Troubleshooting](#11-troubleshooting)
12. [Known limitations](#12-known-limitations)

---

## 1. Features

- **Account system** – register with username, password, nickname and one of 6 avatars; log in; log out.
- **Home page** with your statistics (games played, wins, draws, win ratio, rank) and a **global chat** shared by everyone online (the server admin's announcements also appear here).
- **Rooms**
  - *Create room* – optionally protected by a password.
  - *Room list* – shows up to 8 rooms waiting for an opponent (a lock icon marks password rooms) and refreshes automatically.
  - *Join by room ID* (+ password if needed).
  - *Quick play* – automatically joins any open public room, or creates one and waits.
- **Friends** – see your friends with their status (offline / online / playing), send a friend request from the opponent info window, accept requests, and **challenge a friend to a duel**.
- **Leaderboard** – top 8 players with gold / silver / bronze icons.
- **Play with the computer** – offline game against an AI (minimax search with alpha-beta pruning, depth 3). Starts alternate between you and the computer.
- **In-game extras** – 20×20 board, turn countdown timer, in-room chat, draw offers, score board for the current match, sound effects, and **voice chat** between the two players.
- **Admin interaction** – if the server admin warns or bans you, you see a pop-up (a ban sends you back to the login screen).

## 2. Game rules

(The in-game *Help → Trợ giúp* menu shows a Vietnamese version.)

- The board is **20 × 20**.
- Two players take turns; the first player uses **X**, the second uses **O**. Who starts alternates every game.
- You win by getting **5 in a row** (horizontal, vertical or diagonal). The implementation follows the "international" rule: a line of five that is **blocked at both ends** by the opponent does not count.
- **Turn timer:** the countdown starts at **60 seconds** per move in the code (the help dialog text says 20 seconds). If you run out of time you lose the game.
- You can **offer a draw**; if your opponent accepts, the game is a draw.
- Scoring for the leaderboard: **+1 point per game played, +5 for a draw, +10 for a win** (the server ranks players by `games + draws×5 + wins×10`).

## 3. Tech stack

| Item | Details |
|------|---------|
| Language | Java (project is configured for **Java 15**; JDK 17 / 21 also work) |
| GUI | Java Swing (windows were designed with the NetBeans GUI builder – the `.form` files) |
| Networking | `java.net.Socket` – TCP to the server on port **7777**; UDP port **5555** for voice chat |
| Audio | `javax.sound.sampled` (sound effects + microphone / speaker voice chat) |
| Build | NetBeans / Apache Ant project (`build.xml`, `nbproject/`) – plain `javac` also works |

The client does **not** talk to the database; only the server does. (`lib/mysql-connector-java-8.0.13.jar` is left over in the NetBeans project, but the client code does not use it.)

## 4. What you need to install (prerequisites)

### 4.1 Java Development Kit (JDK) – version 15 or newer
1. Download a JDK: [Adoptium Temurin](https://adoptium.net/) or [Oracle JDK](https://www.oracle.com/java/technologies/downloads/). **JDK 17 or 21 (LTS)** is recommended.
2. Install it.
3. Verify in a terminal (Windows: *Command Prompt* / *PowerShell*):
   ```bash
   java -version
   javac -version
   ```
   Both must print a version number (15 or higher). If `javac` is "not recognized", the JDK's `bin` folder is not on your `PATH` – reinstall and enable the "add to PATH / set JAVA_HOME" option.

### 4.2 The Caro server
Install and start the **Caro_Server** project first (it needs MySQL – see that repository's README). Without a running server, the login screen cannot log you in.

### 4.3 (Optional) An IDE
**Apache NetBeans** (<https://netbeans.apache.org/>) opens this project directly and is the easiest way to run and to edit the windows visually. It is **not** required.

### 4.4 (Optional) Microphone and speakers
Only needed for voice chat. The game works without them.

There are **no other libraries to download** – the client uses only the standard Java library.

## 5. Step-by-step setup

### Step 1 – Get the code
```bash
git clone <your-client-repository-url>
cd Caro_Client-main
```
(or download the ZIP from GitHub and extract it).

### Step 2 – Start the server
In another terminal / IDE window, start Caro_Server (see its README). Wait until it prints `Server is waiting to accept user...`.

### Step 3 – (Only if the server is NOT on your own computer) set the server address
By default the client connects to `127.0.0.1` (your own computer). See [section 8](#8-connecting-to-a-server-on-another-computer) to change it.

## 6. Running the client

> ⚠️ **Always start the client from the project's root folder** (the folder that contains `assets/` and `src/`). All images and sounds are loaded with relative paths like `assets/image/x3.jpg`; if you run from another folder, the board and icons will be blank and sounds will fail.

### Option 1 – NetBeans (easiest)
1. *File → Open Project…* → select the `Caro_Client-main` folder.
2. If NetBeans complains about a missing library `mssql-jdbc-9.2.1.jre15.jar`, ignore it or remove it in *Project Properties → Libraries* – it is not used.
3. Right-click the project → **Run**. (Main class: `controller.Client`; NetBeans runs with the project folder as working directory, so the assets are found.)

### Option 2 – Command line (no IDE)
Run these commands **from inside the `Caro_Client-main` folder**.

**Windows (Command Prompt):**
```bat
mkdir build\classes
dir /s /b src\*.java > sources.txt
javac -encoding UTF-8 -d build\classes @sources.txt
java -cp build\classes controller.Client
```

**Linux / macOS:**
```bash
mkdir -p build/classes
javac -encoding UTF-8 -d build/classes $(find src -name "*.java")
java -cp build/classes controller.Client
```

> ⚠️ Keep `-encoding UTF-8` – the source contains Vietnamese text which would otherwise be garbled. The interface itself is in Vietnamese.

### Option 3 – Build a runnable JAR with Ant (if you have Apache Ant installed)
```bash
ant jar
java -jar dist/CaroClientNhom5.jar
```
Run the JAR from the project root (so `assets/` is next to where you launch it), or copy the `assets` folder next to the JAR's launch directory.

A login window should appear. To play with friends, start the client several times (each on a different computer, or several windows on the same computer for testing).

## 7. How to play – quick walkthrough

1. **Register**: on the login window click the register button, enter username, password, nickname, choose an avatar and submit. You are logged in automatically. (Avoid commas `,` in any field – see [limitations](#12-known-limitations).)
2. **Home page** buttons (Vietnamese label → meaning):
   - **Tạo Phòng** – create a room (you can set an optional password) and wait for an opponent.
   - **Vào phòng** – enter a room ID (and password) to join a specific room.
   - **Tìm phòng** – open the room list / find rooms; **Chơi nhanh** – quick play (auto-match).
   - **Danh sách bạn bè** – friend list; double-click a friend to send a duel invitation.
   - **Bảng xếp hạng** – leaderboard.
   - **Chơi với máy** – play against the computer (AI).
   - **Đăng xuất** – log out; **Thoát Game** – quit.
   - The chat box at the bottom is the **global chat**.
3. **In a game**: click an empty square to place your mark when it is your turn. Use the side panel to chat with your opponent, offer a draw (**Cầu hòa**), mute/unmute the mic and speaker, or leave via the *Menu → Thoát*.
4. After a game ends, a new game starts automatically after a few seconds.

To test the whole thing on one computer: start the server, then open **two** client instances, register two different accounts, create a room in one and join it from the other.

## 8. Connecting to a server on another computer

The server address is hard-coded. Open `src/controller/SocketHandle.java` and change line 67:

```java
socketOfClient = new Socket("127.0.0.1", 7777);
```
to the server computer's IP address, for example:
```java
socketOfClient = new Socket("192.168.1.20", 7777);
```
Then rebuild (re-run the `javac` command or rebuild in NetBeans).

- Find the server's IP with `ipconfig` (Windows) or `ip a` / `ifconfig` (Linux/macOS). Both computers should be on the same network.
- The server's firewall must allow **TCP port 7777**.

## 9. Voice chat

- Voice is streamed **directly between the two players over UDP port 5555** (the server only relays the "mic on/off" notices). Audio format: 44.1 kHz, 16-bit, stereo.
- Both players need a working microphone/speaker and must allow **UDP 5555** through their firewall.
- The client listens on UDP port 5555, so **two client windows on the same computer cannot both use voice chat** at the same time (the second one fails to bind the port). Everything else works fine in that setup.
- If no audio device is available, you will see an error message / console stack trace in voice-related actions, but the game itself keeps working.

## 10. Project structure

```
Caro_Client-main/
├── build.xml / manifest.mf / nbproject/   # NetBeans + Ant project files
├── lib/                                    # NetBeans helper libs (not needed to run the client)
├── assets/                                 # Images and sounds – MUST stay next to where you run the game
│   ├── avatar/   (0.jpg … 5.jpg)           # Player avatars
│   ├── game/     # Game screen pictures and mute / speaker icons
│   ├── icon/     # Buttons, rank medals, loading GIFs, status icons
│   ├── image/    # Board squares, X/O marks, window icon, send button
│   └── sound/    # click.wav, 1click.wav, win.wav
└── src/
    ├── controller/
    │   ├── Client.java        # Entry point (main). Holds every window instance; open/close window helpers
    │   └── SocketHandle.java  # TCP connection to the server: sends nothing by itself, reads and reacts to every server message
    ├── model/
    │   ├── User.java          # Player data (id, nickname, avatar, stats, rank …)
    │   ├── Point.java         # Board coordinate helper (board is 0–19 × 0–19)
    │   └── XOButton.java      # A clickable board square (blank / X / O)
    └── view/                  # One Swing window per screen (.java + .form designed in NetBeans)
        ├── LoginFrm, RegisterFrm
        ├── HomePageFrm        # Main menu + global chat
        ├── RoomNameFrm, RoomListFrm, FindRoomFrm, WaitingRoomFrm
        ├── CreateRoomPasswordFrm, JoinRoomPasswordFrm
        ├── FriendListFrm, FriendRequestFrm, CompetitorInfoFrm
        ├── RankFrm            # Leaderboard
        ├── GameClientFrm      # Online game board, timer, chat, draw, voice chat
        ├── GameAIFrm          # Offline game vs. computer (minimax + alpha-beta)
        └── GameNoticeFrm      # Small pop-up notices
```

The architecture is a simple MVC split: `view` = windows, `controller` = networking and window management, `model` = data classes.

## 11. Troubleshooting

| Problem | Likely cause / fix |
|---------|--------------------|
| `java.net.ConnectException: Connection refused` in the console, login does nothing | The server is not running, or the address/port in `SocketHandle.java` is wrong, or a firewall blocks TCP 7777. Start the server first and then the client. |
| Window opens but the board/icons are blank or you see "Couldn't find file" for sounds | You started the client from the wrong folder. `cd` into `Caro_Client-main` (the folder with `assets/`) and start again. |
| "Tài khoản hoặc mật khẩu không chính xác" | Wrong username or password. |
| "Tài khoản đã đăng nhập ở nơi khác" | That account is already logged in somewhere (or a previous session did not close cleanly – wait a moment or restart the server). |
| "Tài khoản đã bị ban" | The admin banned this account; the admin can unban it from the server's Admin panel. |
| Vietnamese text shows as `?` or strange symbols | Re-compile with `-encoding UTF-8`. |
| `javac` / `java` is not recognized | JDK is not installed or not on `PATH` (see 4.1). |
| `error: invalid source release` / `class file version` errors | Your JDK is older than 15. Install JDK 17 or newer. |
| Voice chat: `BindException: Address already in use` | Another client (or program) on this computer already uses UDP port 5555. |
| Voice chat: `LineUnavailableException` | No microphone/speaker available or another app is using it. |
| The game freezes if the server stops | The client has no auto-reconnect. Close it and start again after the server is back. |

## 12. Known limitations

- The server address and ports (`127.0.0.1:7777`, UDP `5555`) are hard-coded.
- The text protocol is comma-separated, so **do not use commas** in usernames, passwords, nicknames, room passwords or chat messages (the part after the comma will be lost or can cause errors).
- Passwords are sent to the server in plain text (no encryption).
- The user interface language is Vietnamese only.
- The help dialog says 20 seconds per move but the actual timer is 60 seconds.
- Playing with the computer still requires logging in to the server first.
