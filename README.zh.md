# YGA-JWT

[English](README.md) | [中文](README.zh.md)

一个基于 Go 语言与golang-jwt库的 JWT（JSON Web Token）处理工具库，支持自定义用户身份信息，提供简单易用的访问令牌和刷新令牌功能示例。

## 特性

- **JWT 令牌生成与验证**：支持创建和验证 JWT 令牌
- **灵活配置**：可自定义密钥、过期时间、签发人等配置
- **自定义声明**：支持自定义用户身份信息
- **反射机制**：自动处理 RegisteredClaims 字段设置

## 安装

```bash
go get github.com/yclw/yga-jwt
```

## 依赖

- Go 1.24.2+
- github.com/golang-jwt/jwt/v5

## 快速使用

### 导入

```go
import (
 "github.com/yclw/yga-jwt/token"
 "github.com/golang-jwt/jwt/v5"
)
```

### 自定义令牌声明

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

### 创建Jwt处理器

```go
conf := &token.TokenConfig{
    SecretKey: "yoursecretkey", // 密钥
    Issuer:    "yourissuer",    // 签发人
    Audience:  "youraudience",  // 受众
    Expires:   3600,            // 过期时长(秒)
}
handler := token.NewJwtHandler(conf, jwt.SigningMethodHS256, &MyClaims{})
```

### 生成token

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

### 验证/解析token

```go
ok, claims, err := handler.VerifyToken(ctx, tokenStr)
if err != nil || !ok {
    // 处理错误
    return
}

identity := claims.(*MyClaims).Identity
```

使用请参考：[example](https://github.com/yclw/yga-jwt/blob/main/example)
在项目[ygpay](https://github.com/yclw/ygpay)的token中使用

## 项目结构

```text
yga-jwt/
├── token/
│   └── jwt.go          # 核心 JWT 处理逻辑
├── example/
│   ├── access_token.go # 访问令牌示例实现
│   └── refresh_token.go# 刷新令牌示例实现
├── go.mod
├── go.sum
└── README.md
```

## 核心组件

### TokenConfig

令牌配置结构体，包含以下字段：

- `SecretKey`: 签名密钥
- `Issuer`: 令牌签发人
- `Audience`: 令牌受众
- `Expires`: 过期时间（秒）
- `MaxRefreshTimes`: 最大刷新次数（配置项）
- `MultiLogin`: 是否允许多端登录（配置项）

### JwtHandler

JWT 处理器，提供以下核心方法：

- `CreateToken(ctx, claims)`: 创建 JWT 令牌
- `VerifyToken(ctx, token)`: 验证 JWT 令牌
- `GetConfig()`: 获取配置信息

## 许可证

MIT License

## 贡献

欢迎提交 Issue 和 Pull Request 来改进这个项目。
