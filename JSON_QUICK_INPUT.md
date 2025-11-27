# JSON 快捷输入功能说明

## 功能概述

在"添加账号"弹窗中新增了 JSON 快捷输入功能，可以直接粘贴 JSON 格式的账号信息，系统会自动解析并填充到表单中。

## 使用方法

### 1. 打开添加账号弹窗

点击管理控制台的"添加账号"按钮

### 2. 粘贴 JSON 数据

在蓝色背景的"快捷输入"区域，粘贴如下格式的 JSON：

```json
{
  "team_id": "2350088b-ed16-46d2-b512-4876391c5886",
  "secure_c_ses": "CSE.ARsLs02lrEPsMLFfgM6LGSgZ75MPCjlXfgWq_dx0QOr73R3dc...",
  "host_c_oses": "COS.AQH81rgLFxqtXO-PfBi9ob5vye7DQ4ZvaB-sPuhUqBqVKIvl...",
  "csesidx": "1772320590",
  "user_agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36...",
  "available": true
}
```

### 3. 自动填充

- ✅ 粘贴后，JSON 会自动解析
- ✅ 表单字段会自动填充
- ✅ 显示绿色提示："JSON 解析成功，已自动填充表单"

### 4. 保存

点击"保存"按钮提交表单

## 字段说明

| 字段 | 必需 | 说明 |
|------|------|------|
| `team_id` | ✅ | Google Business 团队 ID |
| `secure_c_ses` | ✅ | Cookie 中的 `__Secure-C_SES` 值 |
| `host_c_oses` | ❌ | Cookie 中的 `__Host-C_OSES` 值（可选） |
| `csesidx` | ✅ | 会话索引 |
| `user_agent` | ❌ | 浏览器 User Agent（可选） |
| `available` | ❌ | 账号状态（自动忽略，默认为 true） |

## 特性

### 智能解析

- 自动识别 JSON 格式
- 验证必需字段（team_id, secure_c_ses, csesidx）
- 缺少必需字段时显示错误提示

### 实时反馈

- **成功**：显示绿色提示，表单自动填充
- **错误**：显示红色错误信息
  - "JSON 格式错误，请检查" - JSON 语法错误
  - "JSON 缺少必需字段" - 缺少必需的字段

### 灵活使用

1. **纯 JSON 输入**：粘贴 JSON 后直接保存
2. **手动编辑**：JSON 填充后，可以手动修改表单字段
3. **纯手动输入**：不使用 JSON，直接手动填写表单

## 示例

### 完整示例（带可选字段）

```json
{
  "team_id": "2350088b-ed16-46d2-b512-4876391c5886",
  "secure_c_ses": "CSE.ARsLs02lrEPsMLFfgM6LGSgZ75MPCjlXfgWq_dx0QOr73R3dc-7LNWHrXiTlv5mzOqdZbryvevxIfLUvIJTQR6mgIKZMVy8VCViAy9kgKHe2jiaJma7E2bC6llHsu_xZ36Z5y9N2JKP5jJ2wsw9f7RAl3f7WBo-FliP-QnbXm9R3Z-bNQ6yy_xFH2KRIDBTiWNrLoTpxkvAp-UDhsnPTQEbiXnmDVSxWdmgo-GbdPT7kcFSzO3N5pAYPoZoFXHiycvLMK2UnkVTVFPf5CNNbQmgi7EhgH9fUXwEUUNDVr6-wxk2sMx37tqPw4HfpHYSuKE0MFvRg9SqFWc2yc9ket3Pk53_BsCHQT_a_7LA7hpvs8tRQCBnWLy6tVDOmXl5Xk8QXMWi37x8UyC5K_5VeEGl1Aifi2JLIjJfVsR44DOJL8I--miO7qXvY9i2--P0BI0uNiZhnHIUqTQ",
  "host_c_oses": "COS.AQH81rgLFxqtXO-PfBi9ob5vye7DQ4ZvaB-sPuhUqBqVKIvl9teVcBIohByw5iRWb0A_WQaUlYahj8xO-AjruEqzaK5jFDQrlfDOVqf7QcmRQOUiDQAERllFKTUJ5nYWHn0vCF8zrS27tPek",
  "csesidx": "1772320590",
  "user_agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/142.0.0.0 Safari/537.36"
}
```

### 最简示例（仅必需字段）

```json
{
  "team_id": "2350088b-ed16-46d2-b512-4876391c5886",
  "secure_c_ses": "CSE.ARsLs02lrEPsMLFfgM...",
  "csesidx": "1772320590"
}
```

## 界面预览

```
┌─────────────────────────────────────────────────────────┐
│  🚀 快捷输入 (JSON 格式)                                  │
│  ┌───────────────────────────────────────────────────┐  │
│  │ {                                                 │  │
│  │   "team_id": "...",                              │  │
│  │   "secure_c_ses": "...",                         │  │
│  │   "csesidx": "..."                               │  │
│  │ }                                                │  │
│  └───────────────────────────────────────────────────┘  │
│  ✓ JSON 解析成功，已自动填充表单                          │
│  提示：粘贴 JSON 后，下方表单会自动填充。也可以手动填写。    │
└─────────────────────────────────────────────────────────┘

────────────────────────────────────────────────────────────

或手动填写

Team ID *
┌───────────────────────────────────────────────────────┐
│ 2350088b-ed16-46d2-b512-4876391c5886                │
└───────────────────────────────────────────────────────┘

Secure C SES *
┌───────────────────────────────────────────────────────┐
│ CSE.ARsLs02lrEPsMLFfgM...                           │
└───────────────────────────────────────────────────────┘

...
```

## 技术实现

- 使用 Preact Signals 实现响应式表单
- 实时 JSON 解析和验证
- 自动填充表单字段
- 完整的错误处理

## 文件修改

- `islands/AccountManager.tsx` - 添加 JSON 快捷输入功能

## 后续优化建议

- [ ] 支持批量导入多个账号
- [ ] 提供 JSON 模板下载
- [ ] 支持从文件导入
