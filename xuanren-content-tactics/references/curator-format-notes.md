# Curator 归档记录与格式规范

## 2026-05-02 归档事件

**问题**：`xuanren-content-tactics` 被 Curator 误归档。

**原因**：`.usage.json` 显示该 skill 只有 1 次使用记录，Curator 在7天周期运行时将其判定为 stale 并归档。

**恢复**：从 `.archive/` 恢复后，转换为目录格式（`SKILL.md` 在子目录中）以符合 hermes v0.12.0 的 skill 格式规范。

**防御措施**：
- Skill 格式必须是目录结构（`skill-name/SKILL.md`），不接受单 `.md` 文件
- Skill 需要被至少一个其他 skill 或 cron job 引用，才能保持 active 状态
- 高价值 skill 应在 `jinghong-workflow` 的 `skills:` 列表中保持引用

---

## Skill 格式规范（v0.12.0+）

### 正确格式：目录 + SKILL.md

```
~/.hermes/skills/xuanren-content-tactics/
├── SKILL.md          ← 必须，name 字段必须与目录名一致
├── references/        ← 可选，支持文件
├── templates/        ← 可选，支持文件
└── scripts/          ← 可选，支持文件
```

### 错误格式：单 .md 文件

```
~/.hermes/skills/xuanren-content-tactics.md   ← 无效，v0.12.0 不加载
~/.hermes/skills/xuanren-content-tactics/SKILL.md  ← 有效
```

### 两处存放策略

Skill 可同时存在于两个位置，实现冗余保护：

| 位置 | 用途 |
|------|------|
| `~/.hermes/skills/<name>/` | 全局加载，所有 profile 可见 |
| `~/.hermes/profiles/jinghong/skills/<category>/<name>/` | profile 专用，jinghong 调用时优先 |

---

## Curator 行为备忘

- **运行周期**：7天（168小时），后台 gateway cron ticker 触发
- **归档阈值**：30天无使用记录（`use_count` 持续为 1 或 0）
- **引用保护**：在 `skills:` 列表中被引用 ≠ 使用记录，需实际被调用过
- **PIN 保护**：`hermes skills config <name> pin true` 可免疫 Curator 修改/归档
- ** Curator 日志**：`~/.hermes/logs/curator/<timestamp>/REPORT.md`

---

## 命名冲突警告

**不要创建名为 `viral-content-creation` 的 skill**——这是 Curator 自动生成的 umbrella skill 名，与内容战术库无关。
