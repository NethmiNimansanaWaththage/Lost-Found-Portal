# 🔍 UniFound — University Lost and Found Portal

> A full-stack web application that helps university students and staff report lost items, post found items, and use AI to match them together.

**Course:** Object Oriented Programming (CSC 1062) &nbsp;|&nbsp; **Academic Year:** 2025 / 2026

| Team Member | Student ID | Primary Responsibility |
|---|---|---|
| W. Nethmi Nimansana | AS20240512 | OOP Models, Security Layer, Database |
| U.R. Manusha Vishmika | AS20240528 | Services, Controllers, AI Integration |

---

## 📌 What is UniFound?

Lost a student ID on campus? Found someone's laptop in the library? UniFound is one place to handle both.

Students and staff can:
- Report a **lost item** with a photo and location
- Post a **found item** so the owner can claim it
- Use **AI-powered smart search** to find likely matches
- Submit a **claim** — an admin reviews and approves it

No more notice boards. No more WhatsApp groups. Just one clean platform.

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Backend | Java 17 + Spring Boot 3.2 |
| Database | MongoDB Atlas (cloud) |
| Authentication | JWT (JSON Web Tokens) |
| AI Features | Google Gemini API 1.5 Flash |
| Image Storage | Cloudinary CDN |
| Frontend | React / HTML + CSS + JavaScript |
| Version Control | Git + GitHub |
| Hosting | Render (backend) · Vercel (frontend) |

---

## 🎓 OOP Concepts Implemented

This project was built to demonstrate all five core OOP principles from CSC 1062.

### 1. Abstraction — `BaseItem`
`BaseItem` is an **abstract class** that defines the common structure every item in the system must have (title, description, category, location, status, etc.). It cannot be instantiated on its own — only its subclasses can. It declares one abstract method `getItemType()` which every subclass is forced to implement.

```
BaseItem (abstract)
├── getItemType()  ← abstract, no implementation here
├── title, description, category, imageUrl, status, userId ...
```

### 2. Inheritance — `LostItem` and `FoundItem`
Both `LostItem` and `FoundItem` **extend** `BaseItem`. They automatically inherit all common fields and only add what is unique to them. Neither class has to redeclare `title`, `description`, or `status`.

```
BaseItem
├── LostItem   → adds lostDate, lostLocation  → getItemType() returns "LOST"
└── FoundItem  → adds foundDate, foundLocation → getItemType() returns "FOUND"
```

### 3. Encapsulation — Private Fields with Getters/Setters
Every field in every model class (`User`, `BaseItem`, `Claim`) is declared **private**. Access is only through getter and setter methods. Lombok's `@Data` annotation generates these automatically, keeping the code clean while protecting the data.

### 4. Polymorphism — `ItemService<T>` Interface
`ItemService<T extends BaseItem>` is a **generic interface** with methods: `save`, `findById`, `findAll`, `delete`. Both `LostItemService` and `FoundItemService` implement this same interface — but each provides its own version. A controller can call `.save()` without knowing which type it is working with at compile time.

### 5. Interfaces — Service Layer
Every piece of business logic is hidden behind an interface:

| Interface | Implemented By |
|---|---|
| `ItemService<T>` | `LostItemService`, `FoundItemService` |
| `UserService` | `UserServiceImpl` |
| `ClaimService` | `ClaimServiceImpl` |
| `GeminiService` | `GeminiServiceImpl` |
| `CloudinaryService` | `CloudinaryServiceImpl` |

Controllers depend on interfaces, not concrete classes — keeping the system loosely coupled.

---

## 📁 Project Structure

```
campus-lost-found/
│
├── backend/                          ← Spring Boot application
│   └── src/main/java/com/university/lostfound/
│       ├── LostFoundApplication.java
│       ├── model/
│       │   ├── BaseItem.java         ← abstract class (ABSTRACTION)
│       │   ├── LostItem.java         ← extends BaseItem (INHERITANCE)
│       │   ├── FoundItem.java        ← extends BaseItem (INHERITANCE)
│       │   ├── User.java             ← encapsulated with @Data
│       │   ├── Claim.java
│       │   └── enums/
│       │       ├── Role.java         ← USER, ADMIN
│       │       ├── ItemStatus.java   ← ACTIVE, CLAIMED, RESOLVED
│       │       └── ClaimStatus.java  ← PENDING, APPROVED, REJECTED
│       ├── service/
│       │   ├── ItemService.java      ← generic interface (POLYMORPHISM)
│       │   ├── UserService.java      ← interface (INTERFACE)
│       │   ├── ClaimService.java
│       │   ├── GeminiService.java
│       │   ├── CloudinaryService.java
│       │   └── impl/
│       │       ├── LostItemService.java
│       │       ├── FoundItemService.java
│       │       ├── UserServiceImpl.java
│       │       ├── ClaimServiceImpl.java
│       │       ├── GeminiServiceImpl.java
│       │       └── CloudinaryServiceImpl.java
│       ├── controller/
│       │   ├── AuthController.java   ← /api/auth
│       │   ├── LostItemController.java
│       │   ├── FoundItemController.java
│       │   ├── ClaimController.java
│       │   ├── AIController.java     ← /api/ai
│       │   └── UserController.java
│       ├── security/
│       │   ├── JwtUtil.java          ← generates & validates tokens
│       │   └── JwtFilter.java        ← checks every incoming request
│       ├── config/
│       │   └── SecurityConfig.java
│       ├── repository/
│       │   ├── UserRepository.java
│       │   ├── LostItemRepository.java
│       │   ├── FoundItemRepository.java
│       │   └── ClaimRepository.java
│       ├── dto/                      ← request/response data objects
│       └── exception/                ← global error handling
│
└── frontend/                         ← React / HTML frontend
```

---

## 🚀 Getting Started

### Prerequisites
- Java 17 or higher
- Maven 3.8+
- A MongoDB Atlas account (free tier works)
- A Cloudinary account (free tier works)
- A Google Gemini API key (free at [aistudio.google.com](https://aistudio.google.com))

### 1. Clone the Repository
```bash
git clone https://github.com/YOUR_USERNAME/campus-lost-found.git
cd campus-lost-found
```

### 2. Configure the Backend
Open `backend/src/main/resources/application.properties` and fill in your credentials:

```properties
server.port=8080

spring.data.mongodb.uri=mongodb+srv://<username>:<password>@<cluster>.mongodb.net/lostfound
spring.data.mongodb.database=lostfound

jwt.secret=<your-256-bit-secret-key>
jwt.expiration=86400000

cloudinary.cloud-name=<your-cloud-name>
cloudinary.api-key=<your-api-key>
cloudinary.api-secret=<your-api-secret>

gemini.api.key=<your-gemini-api-key>
gemini.api.url=https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent
```

> ⚠️ **Never commit real credentials to GitHub.** Use environment variables in production.

### 3. Run the Backend
```bash
cd backend
./mvnw spring-boot:run
```
Backend starts at **http://localhost:8080**

### 4. Run the Frontend
```bash
cd frontend
npm install
npm run dev
```
Frontend starts at **http://localhost:5173**

---

## 🔌 API Endpoints

### Auth — no token required
| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/auth/register` | Register a new account |
| POST | `/api/auth/login` | Login and receive JWT token |

### Lost Items
| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/lost` | Get all lost item reports |
| GET | `/api/lost/{id}` | Get one lost item |
| POST | `/api/lost` | Create a lost item report 🔒 |
| PUT | `/api/lost/{id}` | Update your report 🔒 |
| DELETE | `/api/lost/{id}` | Delete a report 🔒 |

### Found Items
| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/found` | Get all found item reports |
| GET | `/api/found/{id}` | Get one found item |
| POST | `/api/found` | Create a found item report 🔒 |
| PUT | `/api/found/{id}` | Update your report 🔒 |
| DELETE | `/api/found/{id}` | Delete a report 🔒 |

### Claims
| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/claims?itemId=X` | Submit a claim on a found item 🔒 |
| GET | `/api/claims/my` | View your own claims 🔒 |
| PUT | `/api/claims/{id}/approve` | Approve a claim 🔒 Admin only |
| PUT | `/api/claims/{id}/reject` | Reject a claim 🔒 Admin only |

### AI Features
| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/ai/match/{lostItemId}` | Find found items that match a lost item 🔒 |
| GET | `/api/ai/search?q=...` | Natural language smart search 🔒 |
| POST | `/api/ai/enhance` | Improve an item description with AI 🔒 |

🔒 = requires `Authorization: Bearer <token>` header

---

## 🗄️ Database Collections

| Collection | Purpose |
|---|---|
| `users` | Registered user accounts |
| `lost_items` | Lost item reports |
| `found_items` | Found item reports |
| `claims` | Ownership claim requests |

---

## 🌐 Live Demo

| Resource | Link |
|---|---|
| Frontend | `[ INSERT VERCEL URL ]` |
| Backend API | `[ INSERT RENDER URL ]` |
| GitHub Repo | `[ INSERT GITHUB URL ]` |

---

## 👥 Team Contributions

| Area | Nethmi (AS20240512) | Manusha (AS20240528) |
|---|---|---|
| OOP Model Design | ✅ Primary | Review |
| Security / JWT | ✅ Primary | Review |
| Repository Layer | ✅ Primary | Review |
| Service Interfaces & Impls | Review | ✅ Primary |
| REST Controllers | Review | ✅ Primary |
| Gemini AI Integration | Review | ✅ Primary |
| Cloudinary Integration | ✅ Primary | Review |
| Frontend | Equal | Equal |
| UML Diagrams | Review | ✅ Primary |
| Documentation | Equal | Equal |

---

## 📄 License

This project was built for academic purposes as part of CSC 1062 — Object Oriented Programming at the Faculty of Computing, 2025/2026.
