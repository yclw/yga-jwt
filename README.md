# YGA-JWT

[English](README.md) | [中文](README.zh.md)

A JWT (JSON Web Token) processing utility library based on Go and the golang-jwt library, supporting custom user identity information with simple and easy-to-use access token and refresh token functionality examples.

## Features

- **JWT Token Generation & Verification**: Support for creating and verifying JWT tokens
- **Flexible Configuration**: Customizable secret key, expiration time, issuer, and other configurations
- **Custom Claims**: Support for custom user identity information
- **Reflection Mechanism**: Automatic handling of RegisteredClaims field settings

## Installation

```bash
go get github.com/yclw/yga-jwt
```

## Dependencies

- Go 1.24.2+
- github.com/golang-jwt/jwt/v5

## Quick Start

### Import

```go
import (
 "github.com/yclw/yga-jwt/token"
 "github.com/golang-jwt/jwt/v5"
)
```

### Custom Token Claims

```go
type MyIdentity struct {
    Uid      string `json:"uid"`
    RoleId   int64  `json:"roleId"`
    Username string `json:"username"`
}

type MyClaims struct {
    Identity MyIdentity `json:"Identity"`
    jwt.RegisteredClaims
}
```

### Create JWT Handler

```go
conf := &token.TokenConfig{
    SecretKey: "yoursecretkey", // Secret key
    Issuer:    "yourissuer",    // Issuer
    Audience:  "youraudience",  // Audience
    Expires:   3600,            // Expiration duration (seconds)
}
handler := token.NewJwtHandler(conf, jwt.SigningMethodHS256, &MyClaims{})
```

### Generate Token

```go
ctx := context.Background()

identity := MyIdentity{
    Uid:      "1",
    RoleId:   1,
    Username: "admin",
}

claims := &MyClaims{
    Identity: identity,
}

tokenStr, expires, err := handler.CreateToken(ctx, claims)
```

### Verify/Parse Token

```go
ok, claims, err := handler.VerifyToken(ctx, tokenStr)
if err != nil || !ok {
    // Handle error
    return
}

identity := claims.(*MyClaims).Identity
```

For usage examples, please refer to: [example](https://github.com/yclw/yga-jwt/blob/main/example)
Used in the token module of project [ygpay](https://github.com/yclw/ygpay)

## Project Structure

```text
yga-jwt/
├── token/
│   └── jwt.go          # Core JWT processing logic
├── example/
│   ├── access_token.go # Access token example implementation
│   └── refresh_token.go# Refresh token example implementation
├── go.mod
├── go.sum
└── README.md
```

## Core Components

### TokenConfig

Token configuration struct containing the following fields:

- `SecretKey`: Signing secret key
- `Issuer`: Token issuer
- `Audience`: Token audience
- `Expires`: Expiration time (seconds)
- `MaxRefreshTimes`: Maximum refresh times (configuration item)
- `MultiLogin`: Whether to allow multi-device login (configuration item)

### JwtHandler

JWT handler providing the following core methods:

- `CreateToken(ctx, claims)`: Create JWT token
- `VerifyToken(ctx, token)`: Verify JWT token
- `GetConfig()`: Get configuration information

## License

MIT License

## Contributing

Issues and Pull Requests are welcome to improve this project.
