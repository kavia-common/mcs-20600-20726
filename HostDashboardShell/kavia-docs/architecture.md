# Host Dashboard Shell Micro Frontend Architecture

This document presents the architecture of the MCS (Modular Component System) Host Dashboard Shell, illustrating its structure, dynamic micro frontend integration, navigation flow, and key tooling for quality and security. The system is based on a React frontend, leveraging modern module federation and CI/CD best practices.

---

## Architecture Diagram

```mermaid
graph TD
    %% Shell & Remotes Structure
    A["Host Dashboard Shell<br/>(React/TypeScript)"]
    subgraph NavigationLayer["Navigation UI"]
        B1["Top Nav Bar (Module Switch)"]
        B2["Side Menu (Host Features)"]
    end
    C["Content Area (Dynamic Loader)"]
    subgraph Remotes["Federated Micro Frontends"]
        D1["Assets MFE"]
        D2["Explorer MFE"]
        D3["Templates MFE"]
    end

    %% Federated Integration
    A -- "Module Federation<br/>Webpack Dynamic Import" --> C
    C -- "Load Remote [Assets]" --> D1
    C -- "Load Remote [Explorer]" --> D2
    C -- "Load Remote [Templates]" --> D3

    %% UI Routing & Navigation
    A -- "Navigation Controls" --> B1
    A -- "Feature Links" --> B2
    B1 -- "Module Selection + Routing" --> C
    B2 -- "Host-specific Features" --> A

    %% Deep Linking
    C -- "Routing / Deep Linking" ---|"> React Router"|> C

    %% Tooling and CI/CD
    subgraph Tooling["Quality & Security Tooling"]
        T1["ESLint"]
        T2["SonarQube"]
        T3["Cypress"]
        T4["CI/CD Pipeline"]
    end
    A -.-> T1
    A -.-> T2
    A -.-> T3
    A -.-> T4
    Remotes -.-> T1
    Remotes -.-> T2
    Remotes -.-> T4

    %% Security & API boundaries
    A -.-> apiStub["API Stubs (future backend interface)"]
    Remotes -.-> apiStub

    %% Extensibility
    subgraph Extensibility[""]
        E1["New Remote (Plug-in MFE)"]
    end
    A -- "Module Federation" --> E1

    %% Notes
    classDef faded fill:#f9f9f9,color:#999;
    class Extensibility faded;
```

---

## Architecture Explanation

### 1. Host Dashboard Shell
The Host Dashboard Shell serves as the primary SPA (single-page application) entry and layout engine. Implemented in React and TypeScript, it comprises:
- **Top Navigation Bar:** Enables users to switch between different micro frontend modules.
- **Side Menu:** Provides core host-level features, settings, or user controls that persist regardless of the active module.
- **Dynamic Content Area:** Actively loads and displays the currently selected remote micro frontend module. Uses Webpack Module Federation to fetch remote bundles at runtime, supporting independent deployment of MFEs.

### 2. Micro Frontend Remotes
There are currently three planned remote micro frontend modules:
- **Assets**
- **Explorer**
- **Templates**

Each is compiled and deployed independently. The host requests these bundles dynamically:
- Navigation triggers (via top bar or deep links) prompt the host to fetch and mount the corresponding remote module.
- Routing and deep linking (with React Router) ensure direct access to any sub-section in a remote MFE from the app or URL.

### 3. Module Federation & Extensibility
Module federation (Webpack) is central:
- Remotes expose their entrypoints (per federation contract).
- The Host Dashboard Shell provides a stable API and integration hooks (via UI and context).
- New remotes can be integrated in the same way, supporting future extensibility and modularity.

### 4. Development Tooling & Quality Gates
The system integrates robust development and CI/CD tooling:
- **ESLint:** Enforces coding standards and detects issues pre-commit, integrated via separate config files and project scripts.
- **SonarQube:** Used for continuous static analysis and security vulnerability detection, typically run in CI.
- **Cypress:** Supports end-to-end (E2E) and integration testing for UI flows, executed in CI/CD.
- **Code Quality in CI/CD:** All code (host & remotes) passes through automated pipelines that run linting, analysis, and testing before deployment. This enables early bug detection and prevents regressions.

### 5. Security & Interfaces
- All business logic and state are managed in the frontend. There are no backend/data dependencies at present; however, API stubs are present to allow future integration with backend services.
- Module boundaries are isolated: remote MFEs run independently and interact with the host only through well-defined interfaces, minimizing security risks.

### 6. User Experience
- Seamless navigation via the top bar, with deep linking and SPA-like experience.
- Consistent theming (light/dark mode toggle), accessibility, and responsive layout across all modules.
- Loading/error states are managed and reflected in the shell environment.

---

**In summary:**  
This architecture supports fast, modular development and deployment, robust quality/security practices, and a scalable micro frontend design for enterprise-scale applications.

