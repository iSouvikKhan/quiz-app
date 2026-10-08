# Quiz App

A real-time multiplayer quiz application. An admin creates a quiz room and adds multiple-choice questions; players join the room with its code and a name, answer each question within a time limit, and see a leaderboard between questions. Communication between the browser and the server happens over Socket.IO.

## Features

- Admin page to create a quiz room and add questions with four options and a correct answer
- Player page to join a room by code and name
- Questions are broadcast to every player in the room at the same time
- 20-second answer window per question; the leaderboard is sent automatically when it expires
- Time-based scoring: a submission earns between 1000 and 500 points depending on how quickly it is sent
- One submission per player per question
- Leaderboard showing the top 20 players
- All quiz state is kept in memory on the server (no database)

## Tech Stack

- **Backend:** Node.js, TypeScript, Socket.IO
- **Frontend:** React 18, TypeScript, Vite, React Router, Tailwind CSS, socket.io-client, react-icons

## Project Structure

```
quiz-app/
├── backend/
│   ├── src/
│   │   ├── index.ts              # Starts the Socket.IO server on port 3000
│   │   ├── Quiz.ts               # Quiz room logic: problems, users, scoring, leaderboard
│   │   └── managers/
│   │       ├── IoManager.ts      # Shared Socket.IO server instance (CORS open to all origins)
│   │       ├── QuizManager.ts    # Keeps track of quiz rooms
│   │       └── UserManager.ts    # Socket event handlers (join, joinAdmin, submit, ...)
│   ├── package.json
│   └── tsconfig.json             # Compiles src/ to dist/
└── frontend/
    ├── src/
    │   ├── App.tsx               # Routes: /admin and /user
    │   └── components/
    │       ├── Admin.tsx         # Create a room, add problems, move to the next problem
    │       ├── CreateProblem.tsx
    │       ├── QuizControls.tsx
    │       ├── User.tsx          # Join screen and player view
    │       ├── Quiz.tsx          # Question and answer submission
    │       └── leaderboard/      # Leaderboard and player cards
    ├── package.json
    └── vite.config.ts
```

## Prerequisites

- Node.js and npm

## Installation

```bash
git clone https://github.com/iSouvikKhan/quiz-app.git
cd quiz-app

cd backend
npm install

cd ../frontend
npm install
```

## Running the App

### Backend

The backend has no build or start script in `package.json`, and TypeScript is not listed as a dependency. Compile it with the TypeScript compiler and run the output:

```bash
cd backend
npx -p typescript tsc
node dist/index.js
```

The Socket.IO server listens on port `3000`.

### Frontend

```bash
cd frontend
npm run dev
```

Vite prints the local URL (by default `http://localhost:5173`). The frontend connects to the backend at `http://localhost:3000`, which is hard-coded in `Admin.tsx` and `User.tsx`.

Other frontend scripts:

| Command           | Description                            |
| ----------------- | -------------------------------------- |
| `npm run build`   | Type-check and build for production    |
| `npm run preview` | Preview the production build           |
| `npm run lint`    | Run ESLint                             |

## Usage

1. Open `/admin` in the browser. Enter a room ID and click **Create room**.
2. Fill in a question title, description, and four options, select the correct option, and click **Add problem**. Repeat for more questions.
3. Players open `/user`, enter the room ID as the code along with their name, and click **Join**.
4. The admin clicks **Next problem** to broadcast a question. Players pick an option and click **Submit**.
5. After 20 seconds the leaderboard is shown to the players. The admin clicks **Next problem** to continue.

The admin page authenticates with a password that is hard-coded in both `backend/src/managers/UserManager.ts` and `frontend/src/components/Admin.tsx`; change it in both places if needed.

## Notes

This is an early-stage project and some parts are incomplete:

- There is no socket event that calls the quiz's `start()` method, and **Next problem** advances from the current index, so the first problem added to a room is never broadcast.
- The "quiz ended" state and final results are not implemented on the server.
- Rooms, players, and scores are lost when the server restarts.
