# LightKid MVP Plan

**Goal**: Deliver a functional MVP for parents to track their child's growth and development.

## 1. Scope Definition

The MVP will focus on the **Core**, **Growth**, and **Milestones** features. Advanced analysis and detailed reporting will be in future phases.

### 🟡 Core Module (Must Have)
- **Authentication**: Register, Login (JWT), Logout.
- **User Profile**: Update parent details.
- **Child Management**:
    - Add Child (Name, DOB, Gender, Photo).
    - Edit/Delete Child.
    - List Children (Switch support).

### 🟢 Growth Module (Must Have)
- **Record Growth**: Input Height, Weight, Head Circumference.
- **History**: List past records.
- **Standards**: Compare against WHO growth standards (z-scores).
- **Charts**: Basic line charts for Weight-for-Age, Height-for-Age.

### 🔵 Milestones Module (Should Have)
- **Milestone List**: View age-appropriate milestones.
- **Checklist**: Mark milestones as achieved/not achieved.
- **Progress**: Simple progress bar for current age group.

### 🟣 Sleep & Activity (Nice to Have)
- **Sleep**: Log Start/End time.
- **Activity**: Simple log (Feeding, Diaper).
- *Excluded for MVP*: Detailed sleep analysis, pattern recognition.

### 📊 Dashboard
- **Summary Card**: Latest Weight/Height.
- **Upcoming Milestones**: Top 3 pending milestones.
- **Recent Activity**: Last 5 logs.

## 2. Technical Roadmap

- [x] **Infrastructure**: Docker, Air, CI/CD, Swagger.
- [x] **Database**: Schema setup for Users, Children, Growth, Milestones.
- [ ] **API Implementation**:
    - [x] **Auth & Child**: Implemented (Register, Login, CRUD Child).
    - [ ] **Growth Service**: Stub only (Service exists but empty).
    - [ ] **Milestone Service**: Partial (Service logic exists, Seed data ready, needs verification).
    - [ ] **Dashboard**: Stub only (returns mock data).
- [ ] **Frontend Implementation** (Flutter):
    - [x] **Screens**: Login, Children, Dashboard, Growth, Milestones, Tracking, Reports.
    - [ ] **Integration**: Currently using mocks or disconnected; needs integration with Backend.

## 3. Success Metrics
- Parent can successfully register and add a child.
- Parent can record growth data and see it validated.
- System returns correct percentile/z-score for growth data.
