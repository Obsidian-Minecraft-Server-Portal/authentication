# Obsidian Authentication Library

The **Obsidian Authentication Library** is a Rust library designed to handle authentication tasks for the [Obsidian Minecraft Server Portal](https://github.com/Obsidian-Minecraft-Server-Portal/). This library provides functionality such as user management, token validation, and secure password handling.

> **Note:** This library is not published on `crates.io` and is only available as a Git dependency. You can include it in your project by specifying its Git repository.

## Features

- User registration and management with role-based permissions.
- User authentication via login credentials or tokens.
- Secure password hashing using the `bcrypt` crate.
- Access token support for registration and other functionalities.
- SQLite database integration using `obsidian-sqlite` and `sqlite`.
- Token-based authentication for security and session validation.

---

## Installation

Add the library as a Git dependency in your `Cargo.toml`:

```toml
[dependencies]
obsidian-authentication = { git = "https://github.com/Obsidian-Minecraft-Server-Portal/authentication.git" }
```

---

## Usage

### 1. Initialize the Authentication Database

Use the library's initialization function to set up the database and create the required tables. Additionally, a default admin user will be generated (username: `admin`, password: `admin`).

```rust
use obsidian_authentication::initialize;

fn main() {
    match initialize() {
        Ok(_) => println!("Authentication database initialized successfully."),
        Err(e) => eprintln!("Error initializing authentication database: {}", e),
    }
}
```

---

### 2. Register a New User

```rust
use obsidian_authentication::data::UserRegistration;
use obsidian_authentication::management::create_user;

fn main() {
    let user = UserRegistration {
        username: "player1".to_string(),
        password: "secure_password".to_string(),
        access_token: "some_unique_token".to_string(),
    };

    match create_user(user, false) {
        Ok(_) => println!("User successfully created."),
        Err(e) => eprintln!("Failed to create user: {}", e),
    }
}
```

---

### 3. Login a User

```rust
use obsidian_authentication::data::UserLogin;
use obsidian_authentication::management::login;

fn main() {
    let user = UserLogin {
        username: "player1".to_string(),
        password: "secure_password".to_string(),
    };

    match login(user, "your_client_secret") {
        Ok(response) => println!("Login successful: {:?}", response),
        Err(e) => eprintln!("Login failed: {}", e),
    }
}
```

---

### 4. Token-Based Authentication

Generate and validate tokens for secure, session-based authentication.

#### Generate Token:

```rust
use obsidian_authentication::management::login;

fn main() {
    // Example during login
    let token = match login(user, "your_client_secret") {
        Ok(response) => response.token,
        Err(e) => panic!("Login failed: {}", e),
    };
    println!("Generated token: {}", token);
}
```

#### Validate Token:

```rust
use obsidian_authentication::management::login_with_token;

fn main() {
    let token = "user_token_here";
    match login_with_token(token, "your_client_secret") {
        Ok(user_info) => println!("Token successfully validated: {:?}", user_info),
        Err(e) => eprintln!("Token validation failed: {}", e),
    }
}
```

---

## Database Structure

### Users Table

The library creates a `users` table to store user information:

| Field      | Type        | Description                  |
|------------|-------------|------------------------------|
| `id`       | INTEGER     | Primary key.                |
| `username` | TEXT        | Unique username.            |
| `password` | TEXT        | Securely hashed password.   |
| `admin`    | BOOLEAN     | Admin status (true/false).  |
| `created_at` | TIMESTAMP  | Account creation timestamp. |
| `updated_at` | TIMESTAMP  | Last update timestamp.      |
| `last_login` | TIMESTAMP  | Last login timestamp.       |

### Access Tokens Table

The library also provides an `access-tokens` table for managing tokens:

| Field        | Type        | Description                     |
|--------------|-------------|---------------------------------|
| `id`         | INTEGER     | Primary key.                   |
| `token`      | TEXT        | Unique access token.           |
| `created_at` | TIMESTAMP   | Token creation timestamp.      |

---

## Dependencies

The following are the key dependencies used in the library:

- **[bcrypt](https://docs.rs/bcrypt)**: For secure password hashing.
- **[serde](https://serde.rs/)**: For structured data serialization and deserialization.
- **[sqlite](https://docs.rs/sqlite)**: For database queries and connections.
- **[obsidian-sqlite](https://github.com/Obsidian-Minecraft-Server-Portal/sqlite)**: SQLite integration specifically for the Obsidian ecosystem.
- **[base64](https://docs.rs/base64)**: For encoding and decoding data in Base64.
- **[rand](https://docs.rs/rand)**: For generating unique access tokens.

---

## Contributing

If you encounter any issues or would like to contribute to this library, please submit a pull request or open an issue on the [GitHub repository](https://github.com/Obsidian-Minecraft-Server-Portal/authentication).

---

## License

This project is licensed under the MIT License. See the LICENSE file for details.