# SaveMate – Project Repositories Overview
### Project Repositories and Folder Structure Documentation  
**Repository:** savemate-docs    
**Course:** Agile Project Management – WSB University  
**Last Updated:** November 20, 2025  

---

##  Purpose of This Document

This file provides a clear overview of all GitHub repositories used in the SaveMate project, including their roles, folder structures, and responsibilities.  


Use this file together with:

- `TECHNOLOGY_STACK.md`  
- `DEVELOPER_SETUP.md`  
- `API_DOCUMENTATION.md`  
- `DATABASE_SCHEMA.md`  

---

##  1. Main Repositories

The SaveMate project uses **three separate GitHub repositories**, following industry best practices for MERN stack applications.

---

### **A. savemate-docs – Documentation Repository**
📌 URL: *https://github.com/jenfranx30/savemate-docs*

Contains:  
- All documentation  
- Technology decisions  
- Guides  
- Diagrams  
- Weekly reports  
- Final report  

Recommended for:  
✔ Instructors  
✔ Developers needing context  
✔ Agile documentation  

---

### **B. savemate-frontend – React Application**
📌 URL: *https://github.com/jenfranx30/savemate-frontend*

Contains the full frontend built with React + Tailwind.

Main features:  
- UI components  
- Routing (React Router v6)  
- Interactive map (React Leaflet)  
- Deal browsing & filtering  
- Authentication pages  
- API calls to backend  

#### Structure (simplified):
```
savemate-frontend/
├── src/
│   ├── components/
│   ├── pages/
│   ├── contexts/
│   ├── services/
│   ├── utils/
│   └── assets/
├── public/
├── .env.example
├── package.json
└── README.md
```

---

### **C. savemate-backend – Node.js/Express API**
📌 URL: *https://github.com/jenfranx30/savemate-backend*

Contains backend API logic, database models, authentication, and business logic.

Key features:  
- REST API  
- JWT authentication  
- MongoDB + Mongoose  
- Cloudinary integration  
- Secure middleware  
- User/Business/Deals routes  

#### Structure (simplified):

```
savemate-backend/
├── config/
├── models/
├── controllers/
├── routes/
├── middleware/
├── utils/
├── server.js
├── .env.example
├── package.json
└── README.md
```

---

##  2. Folder Structure Overview

### Top-Level Project Layout

SaveMate Project/
├── savemate-docs/  Documentation repo
├── savemate-frontend/  React frontend
└── savemate-backend/  Backend API (Node.js/Express)


Benefits of this separation:

✔ Independent development  
✔ Easier deployment (Vercel + Render)  
✔ Clear ownership per team member  
✔ Better version control  

---

##  3. Repository Ownership and Responsibilities

| Repository | Primary Owners | Responsibilities |
|-----------|----------------|------------------|
| **savemate-docs** | Entire Team | Documentation, reports, diagrams |
| **savemate-frontend** | JY, MR, RI | UI, React components, routing |
| **savemate-backend** | RI, JY, MR | API, DB schemas, controllers, auth |

---

##  4. How to Clone All Repositories

Developers should clone **all three** locally:

```bash
# Documentation
git clone https://github.com/jenfranx30/savemate-docs.git

# Frontend
git clone https://github.com/jenfranx30/savemate-frontend.git

# Backend
git clone https://github.com/jenfranx30/savemate-backend.git








