# Test Cases for Proxy Group Selection

## 前提条件

- **Selector 类型**: 支持手动选择，可以选择 group 本身或具体服务器
- **URLTest/Fallback/LoadBalance 类型**: 不支持手动选择具体节点，只能查看自动选择的结果

---

## Test Cases

### Test Case 1: 点击父 Select Group 下的子 Select Group

**场景**: 点击 "翻墙机场"（Selector）下的 "US 自动选择"（Selector 类型子 group）

**期望行为**:
1. 选中 "US 自动选择" 这个 group
2. 取消 "翻墙机场" 下其他具体服务器的选择（如果有）
3. 路由走 "US 自动选择" group 当前选中的节点
4. Actual Server Chain 显示: `GLOBAL → 翻墙机场 → US自动选择 → [US自动选择当前选中的节点]`

---

### Test Case 2: 点击父 Select Group 下的具体服务器

**场景**: 点击 "翻墙机场"（Selector）下的 "California SS - B Group"（叶子节点）

**期望行为**:
1. 选中 "California SS - B Group" 这个具体服务器
2. 取消 "翻墙机场" 下其他 group 的选择（如 US自动选择、JP自动选择等）
3. 路由直接走 "California SS - B Group"
4. Actual Server Chain 显示: `GLOBAL → 翻墙机场 → California SS - B Group`

---

### Test Case 3: 先选子 Group，再选父 Group 下的其他服务器

**场景**:
1. 先点击 "US 自动选择"（Selector 子 group，选中）
2. 再点击 "California SS - B Group"（具体服务器）

**期望行为**:
1. 第一步后: 选中 "US 自动选择"
2. 第二步后:
   - 取消 "US 自动选择" 的选中状态
   - 选中 "California SS - B Group"
   - 路由切换到 "California SS - B Group"

---

### Test Case 4: 先选具体服务器，再选子 Group

**场景**:
1. 先点击 "California SS - B Group"（具体服务器，选中）
2. 再点击 "US 自动选择"（Selector 子 group）

**期望行为**:
1. 第一步后: 选中 "California SS - B Group"
2. 第二步后:
   - 取消 "California SS - B Group" 的选中状态
   - 选中 "US 自动选择"
   - 路由切换到 "US 自动选择" 当前选中的节点

---

### Test Case 5: 点击 URLTest Group 本身

**场景**: 点击 "US 自动选择"（URLTest 类型 group 的 header）

**期望行为**:
1. **如果是 Selector 父 group 下的子 group**: 可以选中该 URLTest group
2. **Actual Server Chain 显示**: `GLOBAL → 翻墙机场 → US自动选择 → [US自动选择当前自动选择的节点]`

**注意**: URLTest group 本身可以被选中（作为父 group 的一个选项），但不能手动选择其下的具体节点

---

### Test Case 6: 点击 URLTest Group 下的具体节点

**场景**: 点击 "US 自动选择"（URLTest 类型）下的 "California SS - B Group"

**期望行为**:
1. **显示提示**: "⚠️ Only Selector groups support manual selection. US自动选择 is URLTest type (auto-managed)"
2. **不执行任何选择操作**
3. 路由保持不变

---

### Test Case 7: 点击 Fallback Group 下的具体节点

**场景**: 点击 "故障转移"（Fallback 类型）下的 "Tokyo SS - B Group"

**期望行为**:
1. **显示提示**: "⚠️ Only Selector groups support manual selection. 故障转移 is Fallback type (auto-managed)"
2. **不执行任何选择操作**
3. 路由保持不变

---

### Test Case 8: GLOBAL 模式下的选择

**场景**: 在 GLOBAL 模式下，点击 GLOBAL 下的 "翻墙机场"（Selector）

**期望行为**:
1. 选中 "翻墙机场"
2. 路由走 "翻墙机场" 当前选中的节点
3. Actual Server Chain 显示: `GLOBAL → 翻墙机场 → [翻墙机场当前选中的节点]`

---

### Test Case 9: GLOBAL 模式下点击 URLTest Group

**场景**: 在 GLOBAL 模式下，点击 GLOBAL 下的 "global自动选择"（URLTest）

**期望行为**:
1. 选中 "global自动选择"
2. 路由走 "global自动选择" 当前自动选择的节点
3. Actual Server Chain 显示: `GLOBAL → global自动选择 → [global自动选择当前自动选择的节点]`

---

### Test Case 10: 多层嵌套 Group 的选择

**场景**:
- 结构: GLOBAL（Selector）→ 翻墙机场（Selector）→ US自动选择（URLTest）→ California SS - B Group
- 点击 "翻墙机场"

**期望行为**:
1. 选中 "翻墙机场"
2. 路由走 "翻墙机场" 当前选中的节点（可能是 US自动选择 当前自动选择的某个节点）
3. Actual Server Chain 显示: `GLOBAL → 翻墙机场 → US自动选择 → [US自动选择当前自动选择的节点]`

---

### Test Case 11: 快速切换选择

**场景**: 快速点击不同的服务器

**期望行为**:
1. 每次点击都正确更新选中状态
2. 路由正确切换
3. UI 状态与 Mihomo 实际配置一致

---

### Test Case 12: 初始状态 - 同名 Group 的独立选择状态 ⭐ CRITICAL

**场景**:
```
GLOBAL (Selector) - now: US自动选择
├── 翻墙机场 (Selector) - now: ???
│   ├── US自动选择 (URLTest)  ← 应该未选中
│   └── ...
└── US自动选择 (URLTest)      ← 应该选中
```

**初始状态**: GLOBAL 选择了 "US自动选择"（作为独立 group）

**操作**: 页面加载后，展开 "翻墙机场"

**期望行为**:
1. ✅ GLOBAL 下的 "US自动选择" 显示为选中（绿色标记）
2. ✅ "翻墙机场" 下的 "US自动选择" **不显示**为选中状态

**实际行为 (Bug)**:
- ❌ "翻墙机场" 下的 "US自动选择" 也显示为选中状态

**根因分析**:
- 可能是 `翻墙机场.now` 默认也是 "US自动选择"
- 或者 `isActive` 判断逻辑错误

**调试方法**:
```javascript
// 在浏览器控制台执行
debugGroupState('US自动选择');
```

---

### Test Case 13: 选择独立 Group 不影响同名 Sub Group

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

### Test Case 14: 选择 Sub Group 不影响同名独立 Group

**场景**: 同上

**操作**: 点击 "翻墙机场" 下的 "US自动选择"

**期望行为**:
1. ✅ "翻墙机场" 下的 "US自动选择" 显示为选中状态
2. ✅ GLOBAL 下的独立 "US自动选择" **不显示**为选中状态
3. ✅ "翻墙机场" 的 header 显示选中 "US自动选择"

---

## 关键逻辑总结

| 点击目标 | 父 Group 类型 | 目标类型 | 行为 |
|---------|-------------|---------|------|
| 具体服务器 | Selector | 叶子节点 | ✅ 选择该服务器 |
| Select Group | Selector | Selector Group | ✅ 选择该 Group |
| URLTest Group | Selector | URLTest Group | ✅ 选择该 Group（作为整体） |
| Fallback Group | Selector | Fallback Group | ✅ 选择该 Group（作为整体） |
| URLTest 下的节点 | URLTest | 叶子节点 | ❌ 提示不支持手动选择 |
| Fallback 下的节点 | Fallback | 叶子节点 | ❌ 提示不支持手动选择 |

---

## 实现状态

- [x] Test Case 1: 已实现
- [x] Test Case 2: 已实现
- [x] Test Case 3: 已实现
- [x] Test Case 4: 已实现
- [x] Test Case 5: 已实现
- [x] Test Case 6: 已实现（显示提示信息）
- [x] Test Case 7: 已实现（显示提示信息）
- [x] Test Case 8: 已实现
- [x] Test Case 9: 已实现
- [x] Test Case 10: 已实现
- [x] Test Case 11: 已实现
- [ ] Test Case 12: **需要修复** - 同名 Group 状态混淆问题
- [ ] Test Case 13: **需要修复** - 依赖 TC12 修复
- [ ] Test Case 14: **需要修复** - 依赖 TC12 修复
