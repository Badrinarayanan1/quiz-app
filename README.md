# QuizRoom

QuizRoom is a full-stack, real-time multiplayer quiz application built with a modern technology stack. It allows an administrator to create quiz rooms, add questions, and control the flow of the quiz, while participants can join rooms and compete in real-time. The project is fully containerized with Docker and ready for deployment on Kubernetes.

The application features a React frontend built with Vite and styled with Tailwind CSS and shadcn/ui, and a Node.js backend using TypeScript and Socket.IO for real-time communication. A Jenkins pipeline is included for continuous integration and deployment to an Amazon EKS cluster.

## ✨ Features

- **Real-Time Multiplayer:** Engage users with live quizzes powered by WebSockets.
- **Admin & User Roles:** Separate interfaces for quiz creation/management and participation.
- **Dynamic Quiz Control:** Admins can create rooms, add problems, start the quiz, and advance to the next question.
- **Live Question Display:** Participants see questions with a countdown timer and progress bar.
- **Real-Time Leaderboard:** Scores are updated instantly based on correctness and speed, fostering competition.
- **Modern UI/UX:** A clean, responsive interface built with React, Vite, and shadcn/ui.
- **Containerized & Cloud-Native:** Dockerized services with Kubernetes manifests for easy orchestration.
- **Automated CI/CD:** Includes a `Jenkinsfile` for automated builds, testing, and deployment to Amazon EKS.

## 🏛️ Architecture

The application is a monorepo containing three main components: `frontend`, `backend`, and `k8s`.

-   **`frontend`**: A React single-page application that serves as the user interface for both participants and admins. It communicates with the backend via Socket.IO.
-   **`backend`**: A Node.js server built with TypeScript and Socket.IO. It manages quiz state, user connections, and real-time event broadcasting.
-   **`k8s`**: Contains Kubernetes manifest files (`Deployment` and `Service`) for deploying the frontend and backend to a cluster.
-   **`Jenkinsfile`**: Defines the CI/CD pipeline to automate building Docker images, pushing them to a registry, and deploying to a Kubernetes cluster.

Communication Flow:
`User/Admin (Browser) <==> Socket.IO <==> Backend (Node.js)`

Deployment Flow:
`GitHub -> Jenkins -> Docker Build -> DockerHub Push -> Deploy to EKS`

## 🛠️ Tech Stack

| Category      | Technology                                                              |
| ------------- | ----------------------------------------------------------------------- |
| **Frontend**  | React, TypeScript, Vite, Tailwind CSS, shadcn/ui, Socket.IO Client      |
| **Backend**   | Node.js, TypeScript, Socket.IO                                          |
| **CI/CD**     | Jenkins                                                                 |
| **DevOps**    | Docker, Kubernetes (EKS)                                                |

## 📁 Project Structure

```
.
├── Jenkinsfile         # CI/CD pipeline for building and deploying to EKS
├── backend/            # Node.js, TypeScript, Socket.IO server
│   ├── Dockerfile
│   └── src/
├── frontend/           # React, TypeScript, Vite, TailwindCSS client
│   ├── Dockerfile
│   └── src/
└── k8s/                # Kubernetes deployment and service manifests
    ├── backend-deployment.yaml
    ├── backend-service.yaml
    ├── frontend-deployment.yaml
    └── frontend-service.yaml
```

## 🚀 Getting Started

### Prerequisites

-   Node.js (v18 or later)
-   npm
-   Docker

### Running Locally

1.  **Clone the repository:**
    ```sh
    git clone https://github.com/Badrinarayanan1/quiz-app.git
    cd quiz-app
    ```

2.  **Run the Backend Server:**
    ```sh
    cd backend
    npm install
    npm run dev
    ```
    The backend server will start on `http://localhost:3000`.

3.  **Run the Frontend Application:**
    Open a new terminal window.
    ```sh
    cd frontend
    npm install
    ```
    Create a `.env` file in the `frontend` directory and add the backend URL:
    ```
    VITE_BACKEND_URL=http://localhost:3000
    ```
    Start the frontend development server:
    ```sh
    npm run dev
    ```
    The application will be accessible at `http://localhost:5173`.

### Using Docker

You can also run the entire application using Docker.

1.  **Build the images:**
    ```sh
    docker build -t quizroom-backend ./backend
    docker build -t quizroom-frontend ./frontend
    ```

2.  **Run the containers:**
    *Note: You will need to manage networking and environment variables for the containers to communicate.*

## 🎮 Usage Guide

### As a Participant

1.  Navigate to the homepage.
2.  In the "Join Quiz" tab, enter your name and the Room ID provided by the admin.
3.  Click "Join Quiz".
4.  Wait for the admin to start the quiz.
5.  Answer questions as they appear on the screen.
6.  View your rank and score on the leaderboard between questions.

### As an Admin

1.  Navigate to the homepage and click the **"Admin Mode"** button in the top-right corner.
2.  Enter the admin password. The default password is `ADMIN_PASSWORD`.
3.  **Quiz Management:** Create a new quiz room by providing a unique Room ID. Select a room to manage.
4.  **Create Problems:** Add questions, descriptions, multiple-choice options, and mark the correct answer for the selected room.
5.  **Quiz Control:** Start the quiz and use the "Next" button to move to the next question or show the leaderboard.
6.  **Live Monitor:** View the current state of the quiz, including the active question and live leaderboard.

## ☁️ Deployment

This project is configured for automated deployment to an Amazon EKS cluster using Jenkins.

### Jenkins Pipeline

The `Jenkinsfile` outlines a declarative pipeline with the following stages:

1.  **Checkout Code**: Clones the repository from GitHub.
2.  **Build Docker Images**: Builds Docker images for both `frontend` and `backend`.
3.  **Push to DockerHub**: Logs into DockerHub and pushes the newly built images.
4.  **Deploy to EKS**:
    -   Configures `kubectl` to connect to the specified EKS cluster.
    -   Applies the Kubernetes manifests from the `k8s/` directory.
    -   Updates the running deployments with the latest Docker images using `kubectl set image`.

### Kubernetes Configuration

The `k8s/` directory contains the necessary Kubernetes manifests:

-   **`backend-deployment.yaml` & `backend-service.yaml`**: Deploys the backend application and exposes it internally within the cluster using a `ClusterIP` service on port 3000.
-   **`frontend-deployment.yaml` & `frontend-service.yaml`**: Deploys the frontend application and exposes it to the internet using a `LoadBalancer` service on port 80. The frontend is configured to communicate with the backend's internal service.
