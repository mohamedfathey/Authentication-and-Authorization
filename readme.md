# 🚀 JWT TOPIC - JWT Authentication with Spring Boot

Welcome to **JWT TOPIC**! 🎉 This project demonstrates a secure authentication and authorization system using **JWT (JSON Web Token)** in a Spring Boot application. It allows users to log in, receive a token, and access protected resources based on their roles (e.g., ADMIN or USER). Let’s dive into the details! 🧠

---

## 🌟 Overview
This project builds a secure system where users can authenticate using **JWT tokens** 🔒 and access resources based on their **roles**. It uses **Spring Security** to manage security rules, ensuring only *`authorized`* users can access protected endpoints. JWT tokens are **`stateless`**, meaning the server doesn’t need to store session data—everything is in the token! 💡

---

## 🔧 Key Components (Detailed Breakdown)

### 1️⃣ **JWTService** 🛠️
- 🟠 **What It Does**: The `heart` of JWT handling! It **`generates`** and **`validates`** JWT tokens for secure authentication.
- 🟠 **Key Functions**:
  - 🔵 **Generate Token (`generateJWTForUser`)**: Creates a JWT token for a user after login. It includes:
    - The user’s `username` (e.g., "john").
    - The user’s `role` (e.g., ADMIN or USER).
    - An expiration time (e.g., 1 hour ⏰).
    - Signed with a secret key using the HMAC256 algorithm 🔐.
  - 🔵 **Extract Username (`getUsername`)**: Decodes the token to get the `username`.
  - 🔵 **Extract Role (`getRole`)**: Decodes the token to get the `role` for authorization.
- 🟠 **How It Works**:
  - Uses the `jjwt` library to create and decode tokens.
  - The token is signed with a secret key (loaded from `application.properties`) to prevent tampering.
  - Example token: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...` (don’t worry, it’s just a secure string! 😄)
- 🟠 **Why It Matters**: The JWT token acts like a digital ID card 🪪—it proves who the user is and what they’re allowed to do.

### 2️⃣ **JWTAuthenticationFilter** 🕵️‍♂️
- 🟠 **What It Does**: A gatekeeper for every HTTP request! It checks if the request has a valid JWT token.
- 🟠**Key Functions**:
  - 🔵 **Extract Token**: Looks for the "Authorization" header (e.g., `Authorization: Bearer <token>`).
  - 🔵 **Validate Token**: Uses `JWTService` to decode the token and extract `username` and `role`. If the token is `invalid` (e.g., expired ⏳ or tampered), it returns a 401 **`(Unauthorized)`** response.
  - 🔵 **Authenticate User**: Creates a `UsernamePasswordAuthenticationToken` with the user’s details and sets it in Spring Security’s context.
  - 🔵 **Add Request Details**: Adds metadata like the user’s IP address to the authentication.
- 🟠 **How It Works**:
  - Extends `OncePerRequestFilter`, so it runs once per request.
  - If no token is found, it skips authentication (Spring Security will block the request if needed).
  - After authentication, it passes the request to the next filter or controller.
- 🟠 **Why It Matters**: Ensures every request is secure by validating the JWT token, keeping unauthorized users out! 🚫



### 3️⃣ **WebSecurityConfig** ⚙️
- 🟠 **What It Does**: Configures Spring Security to define the app’s security rules.
- 🟠 **Key Configurations**:
  - 🔵 **Disable CSRF**: `http.csrf(c -> c.disable())`—CSRF isn’t needed since we’re using JWT (stateless authentication).
  - 🔵 **Disable CORS**: `http.cors(c -> c.disable())`—Disabled for simplicity (in production, you’d configure allowed origins 🌐).
  - 🔵 **Endpoint Rules**:
    - `/auth/**` and `/public/**` are public (e.g., `/auth/login` can be accessed by anyone ✅).
    - All other endpoints require authentication (e.g., `/api/users` needs a valid token 🔑).
  - 🔵 **Add JWT Filter**: Adds `JWTAuthenticationFilter` to the security chain to process tokens.
- 🟠 **How It Works**:
  - Defines rules for which endpoints are public or protected.
  - Integrates the JWT filter to validate tokens before Spring Security processes the request.
- 🟠 **Why It Matters**: Sets the boundaries of what’s public and what’s protected, ensuring security across the app! 🛡️

### 4️⃣ **User Entity** 👤
- **What It Does**: Represents a user in the database with all their details.
- **Key Fields**:
  - `id`: Unique ID (auto-generated).
  - `username`: User’s login name (unique).
  - `password`: Hashed password (for security 🔒).
  - `email`: User’s email (unique).
  - `firstName`, `lastName`: Personal details.
  - `role`: Enum (e.g., ADMIN, USER) for permissions.
  - `verified`: Boolean to track email verification.
  - `otp`, `otpExpiration`: For email verification.
  - `resetOtp`, `resetOtpExpiration`: For password reset.
- 🟠 **How It Works**:
  - Maps to a `user` table in the database using JPA annotations.
  - Sensitive fields like `password` are hidden from API responses with `@JsonIgnore`.
- 🟠 **Why It Matters**: Stores user data and roles, which are used for authentication and authorization.

### 5️⃣ **CustomUserDetailsService** 📋
- 🟠 **What It Does**: Loads user data from the database for Spring Security to authenticate users.
- 🟠 **Key Functions**:
  - 🔵 **Load User (`loadUserByUsername`)**: Fetches the user by `username` from the database.
  - 🔵 **Build UserDetails**: Creates a `UserDetails` object with the user’s `username`, `password`, and `role`.
- 🟠 **How It Works**:
  - 🔵 Uses `UserRepo` to query the database.
  - 🔵 Throws `UsernameNotFoundException` if the user isn’t found.
- 🟠 **Why It Matters**: Connects Spring Security to the database, allowing user authentication during login.

---

<!-- 🖌 1️⃣ 2️⃣ 3️⃣ 4️⃣ 5️⃣ ➡ ⬅⬇↗☑🔴🟠🔵🟣🟣🟪🟦🟩 
💫💥🚀 
6️⃣7️⃣8️⃣9️⃣
❌💯❎✅⏩➖
-->


## 🛠️ How It Works (Step-by-Step Flow)

- 1️⃣ **User Logs In**:
   - 🟣 User sends `POST /auth/login` with `username` and `password` (e.g., `{"username": "john", "password": "pass123"}`).
   - 🟣 Server verifies credentials using `CustomUserDetailsService` and a password encoder (e.g., BCrypt).

- 2️⃣ **Token Generation**:
   - 🟣 If credentials are valid, `JWTService` generates a JWT token with the user’s `username` and `role`.
   - 🟣 Token example: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...` (a secure, encoded string).

- 3️⃣ **Token Sent to Client**:
   - 🟣 Server responds with the token, and the client stores it (e.g., in local storage or a cookie).

- 4️⃣ **Access Protected Endpoint**:
   - 🟣 Client sends a request to a protected endpoint (e.g., `GET /api/users`) with the token in the header: `Authorization: Bearer <token>`.

- 5️⃣ **Token Validation**:
   - 🟣 `JWTAuthenticationFilter` extracts and validates the token using `JWTService`.
   - 🟣 If valid, it sets the user in the `SecurityContextHolder` with their `role`.

- 6️⃣ **Authorization Check**:
   - 🟣 Spring Security checks if the user’s role allows access to the endpoint (e.g., "ROLE_ADMIN" for `/api/admin`).
   - 🟣 If allowed, the request proceeds; if not, a 403 (Forbidden) response is returned.

- 7️⃣ **Token Expiration**:
   - 🟣 If the token expires (e.g., after 1 hour ⏰), the user must log in again to get a new token.

---

## ⚡ Setup Instructions

1. **Add Dependencies** to `pom.xml` 📦:
   ```xml
   <dependencies>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-security</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-data-jpa</artifactId>
       </dependency>
       <!-- https://mvnrepository.com/artifact/io.jsonwebtoken/jjwt-api -->
		<dependency>
			<groupId>io.jsonwebtoken</groupId>
			<artifactId>jjwt-api</artifactId>
			<version>0.12.3</version>
		</dependency>
		<dependency>
			<groupId>com.auth0</groupId>
			<artifactId>java-jwt</artifactId>
			<version>4.2.1</version>
		</dependency>

		<!-- https://mvnrepository.com/artifact/io.jsonwebtoken/jjwt-impl -->
		<dependency>
			<groupId>io.jsonwebtoken</groupId>
			<artifactId>jjwt-impl</artifactId>
			<version>0.12.3</version>
			<scope>runtime</scope>
		</dependency>

		<!-- https://mvnrepository.com/artifact/io.jsonwebtoken/jjwt-jackson -->
		<dependency>
			<groupId>io.jsonwebtoken</groupId>
			<artifactId>jjwt-jackson</artifactId>
			<version>0.12.3</version>
			<scope>runtime</scope>
		</dependency>


       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
           <version>8.0.33</version>
       </dependency>
   </dependencies>
   ```

2. **Configure Application Properties** (`application.properties`) ⚙️:
   - JWT settings:
     ```
     jwt.algorithm.key=your-very-secure-secret-key
     jwt.issuer=ProjectGraduation
     jwt.expiryInSeconds=3600
     ```
   - Database settings (e.g., MySQL):
     ```
     spring.datasource.url=jdbc:mysql://localhost:3306/yourdb
     spring.datasource.username=root
     spring.datasource.password=yourpassword
     spring.jpa.hibernate.ddl-auto=update
     ```

3. **Database Setup** 🗄️:
   - Create a database (e.g., `yourdb` in MySQL).
   - JPA will auto-create the `user` table based on the `User` entity.

4. **Run the Application** 🚀:
   - Start the Spring Boot app.
   - Test `/auth/login` to get a token.
   - Use the token to access protected endpoints like `/api/users`.

---

## 🌐 Example Flow
1. **Login**:
   - Request: `POST /auth/login` with `{"username": "john", "password": "pass123"}`.
   - Response: `{"token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."}`.

2. **Access Resource**:
   - Request: `GET /api/users` with header `Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...`.
   - Server validates the token and responds with the resource (or a 401/403 if invalid).

---

## 🎯 Why Use JWT?
- **Stateless**: No server-side session storage—everything is in the token! 🗿
- **Scalable**: Perfect for distributed systems like microservices 🌍.
- **Secure**: Tokens are signed to prevent tampering, and expiration ensures they don’t last forever 🔐.

---
---
---
---
---

# 🚀  JWT Authentication with Spring Boot With Email Verification




## 🔧 Key Components

### 1. **AuthService** 📧
- **What It Does**: Manages user verification and password reset using **`OTP`** (One-Time Password) sent via **email**. It ensures users verify their email before logging in and provides a secure way to reset passwords.

#### 🟠 **Key Functions** (Detailed Explanation)
Here’s a breakdown of the key functions in `AuthService` with examples to make everything super clear! 🌟

- **🔵 Generate and Send OTP (`generateAndSendOtp`)**
  - **Purpose**: Generates a 6-digit OTP and sends it to the user’s email for email verification after registration.
  - **How It Works**:
    - Finds the user by email in the database using `UserRepo`. If the user isn’t found, throws a `UserNotFoundException` 🚫.
    - Generates a random 6-digit OTP (e.g., "123456") using `generateOtp`.
    - Sets the OTP and its expiration (10 minutes ⏰) on the user entity.
    - Saves the updated user to the database.
    - Sends an email to the user with the OTP using `JavaMailSender`.
  - **Email Details**:
    - Subject: "Your OTP Code"
    - Body: "Your OTP code is: 123456\nIt expires in 10 minutes."
  - **Example**:
    - A user registers with email `user@example.com`.
    - `generateAndSendOtp("user@example.com")` is called.
    - The user receives an email: "Your OTP code is: 123456\nIt expires in 10 minutes."
    - The user has 10 minutes to verify their email using this OTP.
  - **Why It Matters**: Ensures users verify their email, preventing fake accounts and securing the registration process 📬.

- **🔵 Verify OTP (`verifyOtp`)**
  - **Purpose**: Verifies the OTP entered by the user to confirm their email.
  - **How It Works**:
    - Finds the user by email. If not found, throws a `UserNotFoundException` 🚫.
    - Checks if the OTP exists and hasn’t expired (within 10 minutes). If expired or missing, returns `false`.
    - Compares the provided OTP with the stored OTP. If they don’t match, returns `false`.
    - If the OTP is valid, marks the user as verified (`setVerified(true)`), clears the OTP, and saves the user.
  - **Example**:
    - User received OTP "123456" and enters it within 10 minutes.
    - `verifyOtp("user@example.com", "123456")` returns `true`, and the user is marked as verified ✅.
    - If the user enters "123457" or the OTP expires, `verifyOtp` returns `false` ❌.
  - **Why It Matters**: Confirms the user owns the email address, adding a layer of security to the account setup 🔒.

- **🔵 Send Password Reset OTP (`sendPasswordResetOtp`)**
  - **Purpose**: Generates an OTP for password reset and sends it to the user’s email.
  - **How It Works**:
    - Finds the user by email. If not found, throws a `UserNotFoundException` 🚫.
    - Generates a new 6-digit OTP (e.g., "654321").
    - Sets the reset OTP and its expiration (10 minutes ⏰) on the user entity.
    - Saves the user to the database.
    - Sends an email with the OTP for password reset.
  - **Email Details**:
    - Subject: "Password Reset OTP"
    - Body: "Your OTP for password reset is: 654321\nExpires in 10 minutes."
  - **Example**:
    - User requests a password reset for `user@example.com`.
    - `sendPasswordResetOtp("user@example.com")` sends an email: "Your OTP for password reset is: 654321\nExpires in 10 minutes."
  - **Why It Matters**: Provides a secure way for users to reset their password without compromising security 📩.

- **🔵 Verify Reset OTP (`verifyResetOtp`)**
  - **Purpose**: Verifies the OTP for password reset.
  - **How It Works**:
    - Finds the user by email. If not found, throws a `UserNotFoundException` 🚫.
    - Checks if the reset OTP exists and hasn’t expired. If expired or missing, returns `false`.
    - Compares the provided OTP with the stored reset OTP. Returns `true` if they match, `false` otherwise.
  - **Example**:
    - User received reset OTP "654321" and enters it within 10 minutes.
    - `verifyResetOtp("user@example.com", "654321")` returns `true` ✅.
    - If the OTP is wrong or expired, it returns `false` ❌.
  - **Why It Matters**: Ensures only the user who received the OTP can proceed with resetting their password 🔐.

- **🔵 Update Password with OTP (`updatePasswordWithOtp`)**
  - **Purpose**: Updates the user’s password after verifying the reset OTP.
  - **How It Works**:
    - Finds the user by email. If not found, throws a `UserNotFoundException` 🚫.
    - Verifies the reset OTP (checks if it exists, hasn’t expired, and matches the provided OTP). If invalid, returns `false`.
    - If valid, encrypts the new password using `EncryptionService` and updates the user’s password.
    - Clears the reset OTP and its expiration, then saves the user.
  - **Example**:
    - User enters OTP "654321" and new password "newPass123".
    - `updatePasswordWithOtp("user@example.com", "654321", "newPass123")` encrypts "newPass123", updates the user’s password, and returns `true` ✅.
    - If the OTP is invalid, it returns `false` ❌.
  - **Why It Matters**: Allows users to securely reset their password after verifying their identity 🔑.

- **🔵 Regenerate OTP (`regenerateOtp`)**
  - **Purpose**: Generates a new OTP if the current one has expired or the user needs a new one.
  - **How It Works**:
    - Finds the user by email. If not found, throws a `UserNotFoundException` 🚫.
    - Checks if the current OTP is still valid (not expired). If valid, throws an `OtpStillValidException` ⏳.
    - If expired, generates a new 6-digit OTP, sets a new expiration (10 minutes), and saves the user.
    - Sends the new OTP via email.
  - **Example**:
    - User’s OTP expired, so they request a new one.
    - `regenerateOtp("user@example.com")` generates a new OTP "789123" and emails it: "Your new OTP code is: 789123\nIt expires in 10 minutes."
    - If the current OTP is still valid, it throws an exception: "Current OTP is still valid until [expiration time]."
  - **Why It Matters**: Gives users a chance to get a new OTP if they miss the 10-minute window, improving user experience 📨.

---

### 2. **UserService** 👤
- **What It Does**: Handles user registration, login, and retrieval, integrating with `AuthService`, `JWTService`, and `EncryptionService` to manage the user lifecycle.

#### 🟠 **Key Functions** (Detailed Explanation)
Let’s break down the key functions in `UserService` with examples! 🌟

- **🔵 Register User (`registerUser`)**
  - **Purpose**: Registers a new user, saves them to the database, and triggers email verification.
  - **How It Works**:
    - Checks if the email or username already exists in the database. If either exists, throws a `UserAlreadyExistsException` 🚫.
    - Creates a new `User` entity with the provided details (first name, last name, username, email, role).
    - Encrypts the password using `EncryptionService` (e.g., with BCrypt).
    - Saves the user to the database.
    - Calls `AuthService.generateAndSendOtp` to send an OTP for email verification.
  - **Example**:
    - Registration request: `{"firstName": "John", "lastName": "Doe", "username": "johndoe", "email": "john@example.com", "password": "pass123", "role": "USER"}`.
    - Checks: No user with email "john@example.com" or username "johndoe" exists ✅.
    - Creates user, encrypts "pass123", saves to database, and sends an OTP email.
    - Returns the created user object.
  - **Why It Matters**: Ensures new users are registered securely and initiates the email verification process 📝.

- **🔵 Login User (`loginUser`)**
  - **Purpose**: Authenticates a user and returns a JWT token if credentials are valid.
  - **How It Works**:
    - Finds the user by username or email (case-insensitive). If not found, throws an `InvalidCredentialsException` 🚫.
    - Verifies the provided password against the stored (encrypted) password using `EncryptionService`. If incorrect, throws an `InvalidCredentialsException`.
    - Checks if the user’s email is verified. If not, throws a `UserNotVerifiedException` 🚫.
    - If all checks pass, generates a JWT token using `JWTService` and returns it.
  - **Example**:
    - Login request: `{"username": "johndoe", "password": "pass123"}`.
    - Finds user "johndoe", verifies password, and checks email verification ✅.
    - Returns a JWT token: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...`.
    - If the password is wrong or email isn’t verified, throws an exception ❌.
  - **Why It Matters**: Securely authenticates users and provides a JWT token for accessing protected endpoints 🔑.

- **🔵 Get User by Username (`getUserByUsername`)**
  - **Purpose**: Retrieves a user by their username.
  - **How It Works**:
    - Queries the database for a user with the given username (case-insensitive).
    - If found, returns the user. If not, throws a `UserNotFoundException` 🚫.
  - **Example**:
    - `getUserByUsername("johndoe")` returns the user with username "johndoe" ✅.
    - `getUserByUsername("unknown")` throws a `UserNotFoundException` ❌.
  - **Why It Matters**: Allows other parts of the app to fetch user details for operations like profile viewing or updates 🧑.

---

## ⚡ Setup Instructions
1. **Add Dependencies** to `pom.xml` 📦:
   ```xml
   <dependencies>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-security</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-data-jpa</artifactId>
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-mail</artifactId>
       </dependency>
       <dependency>
           <groupId>io.jsonwebtoken</groupId>
           <artifactId>jjwt</artifactId>
           <version>0.9.1</version>
       </dependency>
       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
           <version>8.0.33</version>
       </dependency>
   </dependencies>
   ```

2. **Configure Application Properties** (`application.properties`) ⚙️:
   - Database settings:
     ```
     spring.datasource.url=jdbc:mysql://localhost:3306/yourdb
     spring.datasource.username=yourusername
     spring.datasource.password=yourpassword
     spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
     spring.jpa.hibernate.ddl-auto=update
     spring.jpa.show-sql=true
     spring.jpa.properties.hibernate.format_sql=true
     spring.jpa.database=mysql
     spring.jpa.database-platform=org.hibernate.dialect.MySQL8Dialect
     ```
   - Email settings:
     ```
     spring.mail.host=smtp.gmail.com
     spring.mail.port=587
     spring.mail.username=your-email@gmail.com
     spring.mail.password=your-app-password
     spring.mail.properties.mail.smtp.auth=true
     spring.mail.properties.mail.smtp.starttls.enable=true
     spring.mail.properties.mail.smtp.starttls.required=true
     spring.mail.properties.mail.smtp.connectiontimeout=5000
     spring.mail.properties.mail.smtp.timeout=5000
     spring.mail.properties.mail.smtp.writetimeout=5000
     ```
   - JWT settings:
     ```
     application.security.jwt.secret-key=your-jwt-secret-key
     application.security.jwt.access-token-expiration=86400000
     application.security.jwt.refresh-token-expiration=604800000
     jwt.algorithm.key=your-jwt-algorithm-key
     jwt.issuer=your-issuer
     jwt.expiryInSeconds=604800
     ```
   - Encryption settings:
     ```
     encryption.salt.rounds=10
     ```
   - Server port:
     ```
     server.port=9999
     ```

3. **Database Setup** 🗄️:
   - Create a database (e.g., `yourdb` in MySQL).
   - JPA will auto-create the `user` table.

4. **Run the Application** 🚀:
   - Start the app.
   - Test `/auth/register` to register a user and receive an OTP email.
   - Verify the OTP, then log in to get a JWT token.

---

## 🌐 Example Flow
- **Register**: `POST /auth/register` with `{"username": "johndoe", "email": "john@example.com", "password": "pass123", "firstName": "John", "lastName": "Doe", "role": "USER"}`.
- **Receive OTP**: Check email for OTP (e.g., "123456").
- **Verify OTP**: `POST /auth/verify-otp` with `{"email": "john@example.com", "otp": "123456"}`.
- **Login**: `POST /auth/login` with `{"username": "johndoe", "password": "pass123"}`.
- **Response**: `{"token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."}`.

---

## 🎯 Why Use This Setup?
- **Secure Registration**: Email verification with OTP ensures real users 📧.
- **Safe Password Reset**: OTP-based reset prevents unauthorized changes 🔒.
- **Stateless Auth**: JWT tokens make the app scalable 🌍.

---

**Happy coding! 💻**