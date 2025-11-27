# Cookie 设置说明

## Cookie 过期时间

✅ **已设置为 7 天**

```typescript
setCookie(headers, {
  name: "session",
  value: token,
  maxAge: 60 * 60 * 24 * 7, // 7天 = 604800秒
  path: "/",
  httpOnly: true,
  secure: isDeploy,
  sameSite: "Lax",
});
```

## 详细配置

| 配置项 | 值 | 说明 |
|--------|-----|------|
| **name** | `session` | Cookie 名称 |
| **maxAge** | `604800` 秒 | 7天过期时间 |
| **path** | `/` | 全站可用 |
| **httpOnly** | `true` | 仅 HTTP 访问，防止 XSS |
| **secure** | 动态设置 | HTTPS 时为 true |
| **sameSite** | `Lax` | 防止 CSRF 攻击 |

## 过期时间计算

```
7天 = 7 × 24 × 60 × 60
    = 168 × 60 × 60
    = 10080 × 60
    = 604800 秒
```

## Secure 属性动态设置

为了兼容本地开发和生产环境：

```typescript
const isDeploy = Deno.env.get("DENO_DEPLOYMENT_ID") !== undefined;

setCookie(headers, {
  // ...
  secure: isDeploy, // 生产环境: true, 本地开发: false
  // ...
});
```

### 环境判断

- **Deno Deploy**：`DENO_DEPLOYMENT_ID` 存在 → `secure: true` （HTTPS）
- **本地开发**：`DENO_DEPLOYMENT_ID` 不存在 → `secure: false` （HTTP）

## 会话生命周期

### 登录时
1. 用户输入密码并提交
2. 服务器验证密码正确
3. 生成随机会话令牌（32字节十六进制）
4. 存储会话到内存 Set
5. 设置 Cookie（7天有效期）
6. 重定向到首页

### 访问页面时
1. 浏览器自动发送 Cookie
2. 服务器从 Cookie 中提取令牌
3. 检查令牌是否在内存 Set 中
4. 验证通过 → 允许访问
5. 验证失败 → 重定向到登录页

### 登出时
1. 用户点击"登出"按钮
2. 服务器从内存 Set 中删除令牌
3. 清除浏览器 Cookie（设置 maxAge: 0）
4. 重定向到登录页

### 7天后
1. Cookie 自动过期
2. 浏览器不再发送 Cookie
3. 服务器检测到未认证
4. 自动重定向到登录页

## 重要提醒

### ⚠️ 内存存储限制

**会话令牌存储在内存中**，这意味着：

- ✅ **优点**：快速、简单
- ❌ **限制**：服务器重启后所有会话失效

**重启后的影响：**
```
服务器重启
    ↓
内存清空
    ↓
所有会话令牌丢失
    ↓
用户 Cookie 仍然存在（7天内）
    ↓
但令牌无效
    ↓
用户需要重新登录
```

### 解决方案（可选）

如果需要持久化会话，可以使用 Deno KV 存储：

```typescript
// 创建会话 - 存储到 KV
export async function createSession(kv: Deno.Kv): Promise<string> {
  const token = generateSessionToken();
  await kv.set(["sessions", token], {
    created_at: Date.now(),
    expires_at: Date.now() + 604800000, // 7天
  }, {
    expireIn: 604800000, // 7天后自动删除
  });
  return token;
}

// 验证会话 - 从 KV 读取
export async function isValidSession(kv: Deno.Kv, token: string): Promise<boolean> {
  const entry = await kv.get(["sessions", token]);
  return entry.value !== null;
}
```

## 安全特性

### 1. HttpOnly
```typescript
httpOnly: true
```
- 防止 JavaScript 访问 Cookie
- 防止 XSS 攻击窃取会话令牌

### 2. Secure（生产环境）
```typescript
secure: true  // 仅在 HTTPS 下传输
```
- 防止中间人攻击
- Cookie 只在 HTTPS 连接中发送

### 3. SameSite
```typescript
sameSite: "Lax"
```
- 防止 CSRF 攻击
- 跨站请求不发送 Cookie（除了顶级导航）

### 4. 随机令牌
```typescript
const array = new Uint8Array(32);
crypto.getRandomValues(array);
// 生成 64 位十六进制字符串
```
- 使用加密安全的随机数生成器
- 32 字节 = 256 位熵
- 几乎不可能被猜测

## 测试验证

### 检查 Cookie 设置

1. **登录后检查浏览器 Cookie**：
   - 打开开发者工具 → Application → Cookies
   - 查看 `session` Cookie
   - 确认 `Max-Age` 为 `604800`
   - 确认 `Expires` 显示 7 天后的日期

2. **检查 Cookie 属性**：
   - `HttpOnly`: ✅
   - `Secure`: ✅ (Deno Deploy)
   - `SameSite`: Lax
   - `Path`: /

### 测试会话过期

```bash
# 方法 1: 修改系统时间（不推荐）
# 方法 2: 手动删除 Cookie 测试
# 方法 3: 等待 7 天（不现实）

# 最简单的测试方法：重启服务器
# 1. 登录系统
# 2. 重启 Deno Deploy
# 3. 刷新页面 → 应该跳转到登录页
```

## 配置建议

### 生产环境
- ✅ 7 天过期时间（当前设置）
- ✅ HttpOnly: true
- ✅ Secure: true
- ✅ SameSite: Lax

### 如需更长时间
```typescript
maxAge: 60 * 60 * 24 * 30  // 30天
```

### 如需更短时间
```typescript
maxAge: 60 * 60 * 24       // 1天
maxAge: 60 * 60 * 12       // 12小时
maxAge: 60 * 60 * 2        // 2小时
```

## 文件位置

- **Cookie 设置**：`lib/auth.ts` → `setSessionCookie()` 函数
- **登录处理**：`routes/api/auth/login.ts`
- **登出处理**：`routes/api/auth/logout.ts`

## 总结

✅ **Cookie 已正确设置为 7 天过期时间**

- 用户登录后 7 天内无需重新登录
- 自动刷新页面时保持登录状态
- 安全特性完整（HttpOnly, Secure, SameSite）
- 兼容本地开发和生产环境

**注意**：服务器重启会清空内存中的会话，用户需要重新登录。如果需要持久化会话，建议使用 Deno KV 存储。
