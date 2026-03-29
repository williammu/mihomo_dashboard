# Group Identity Test Cases

## 问题描述

在 Mihomo 配置中，同一个 group 名称可能出现在不同位置：
1. **独立 Group**: 直接在 GLOBAL 下的 group
2. **Sub Group**: 作为另一个 group 的 child 的 group

这两个虽然名字相同，但身份不同，选中状态需要分别处理。

---

## 核心 Bug

### Bug 描述
初始状态选择了 GLOBAL 下的 "US自动选择"，展开 "翻墙机场" 时，其子 group "US自动选择" 也显示为选中状态。

### 期望行为
- GLOBAL 下的 "US自动选择" 显示为选中
- "翻墙机场" 下的 "US自动选择" **不显示**为选中

### 实际行为
- 两者都显示为选中状态 ❌

---

## Test Cases

### Test Case A: 初始状态 - 选择独立 Group，Sub Group 不应选中

**初始状态**:
```
GLOBAL (Selector) - now: US自动选择
├── 翻墙机场 (Selector) - now: ???
│   ├── US自动选择 (URLTest)  ← 应该未选中
│   └── ...
└── US自动选择 (URLTest)      ← 应该选中
```

**操作**: 页面加载后，展开 "翻墙机场"

**期望行为**:
1. ✅ GLOBAL 下的 "US自动选择" 显示为选中
2. ✅ "翻墙机场" 下的 "US自动选择" **不显示**为选中状态

**实际行为 (Bug)**:
- ❌ "翻墙机场" 下的 "US自动选择" 也显示为选中状态

**可能原因**:
1. `翻墙机场.now` 默认值也是 "US自动选择"
2. `isActive` 判断逻辑有误

---

### Test Case B: 选择独立 Group 不影响同名 Sub Group

**场景**:
```
GLOBAL
├── 翻墙机场
│   └── US自动选择 (sub group)
└── US自动选择 (独立 group)
```

**操作**: 点击 GLOBAL 下的 "US自动选择"

**期望行为**:
1. ✅ GLOBAL 下的 "US自动选择" 显示为选中
2. ✅ "翻墙机场" 下的 "US自动选择" **不显示**为选中状态

---

### Test Case C: 选择 Sub Group 不影响同名独立 Group

**场景**: 同上

**操作**: 点击 "翻墙机场" 下的 "US自动选择"

**期望行为**:
1. ✅ "翻墙机场" 下的 "US自动选择" 显示为选中状态
2. ✅ GLOBAL 下的独立 "US自动选择" **不显示**为选中状态
3. ✅ "翻墙机场" 的 header 显示选中 "US自动选择"

---

### Test Case D: 切换选择时的状态更新

**场景**:
```
GLOBAL
└── 翻墙机场 (Selector)
    ├── US自动选择 (URLTest)
    └── California SS
```

**操作 1**: 点击 "翻墙机场" 下的 "US自动选择"

**期望 1**:
- ✅ "US自动选择" 显示为选中
- ✅ "California SS" 未选中

**操作 2**: 点击 "翻墙机场" 下的 "California SS"

**期望 2**:
- ✅ "US自动选择" 取消选中
- ✅ "California SS" 显示为选中

---

## 调试信息

请在浏览器控制台执行以下调试：

```javascript
// 1. 检查初始状态
console.log('=== 初始状态 ===');
console.log('globalProxy.now:', globalProxy?.now);
console.log('翻墙机场.now:', allProxies['翻墙机场']?.now);

// 2. 检查 allProxies 中的数据
console.log('=== allProxies ===');
console.log('翻墙机场:', allProxies['翻墙机场']);
console.log('US自动选择:', allProxies['US自动选择']);

// 3. 展开翻墙机场后，检查子 group 的 isActive 计算
// 应该看到 renderSubGroup 的调试日志
```

---

## 问题分析

### 可能原因 1: 翻墙机场.now 默认值为 US自动选择

Mihomo 配置可能默认设置 `翻墙机场.now = 'US自动选择'`，导致 sub group 显示为选中。

**验证方法**:
```javascript
console.log('翻墙机场.now:', allProxies['翻墙机场']?.now);
```

### 可能原因 2: isActive 判断逻辑错误

即使 `翻墙机场.now !== 'US自动选择'`，`isActive` 仍被计算为 true。

**验证方法**: 查看 `renderSubGroup` 的调试日志

### 可能原因 3: 数据同步问题

`allProxies` 中的数据不是最新的，或者 `refreshAll` 没有正确更新。

---

## 修复方案

### 方案 1: 确认数据正确性
确保 `allProxies` 包含最新的 `now` 值。

### 方案 2: 添加更多调试
在关键位置添加日志，追踪 `isActive` 的计算过程。

### 方案 3: 使用路径作为唯一标识
如果上述方案不奏效，考虑使用完整路径来区分同名 group：
```javascript
const isActive = parentGroupName 
    ? allProxies[parentGroupName]?.now === groupName
    : globalProxy.now === groupName;
```
