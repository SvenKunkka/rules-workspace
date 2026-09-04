# 硬件文档核心规则库

## English Overview

This repository is a human-readable and machine-readable rule library for hardware documentation. `rules.json` is the only runtime entry point. It contains deterministic field, value, unit, compound-constraint, product-identity, and semantic-extraction rules. Review candidates and unresolved conflicts live under `.internal/review`, complete source evidence under `.internal/evidence`, legacy rules under `.internal/legacy`, and generated reports under `.internal/reports`.

Rules are promoted automatically only after source independence, product coverage, semantic/type/unit consistency, counterexample checks, and held-out regression tests all pass. Each rule references an `evidence_id`; full evidence should remain in the evidence store rather than being copied into the runtime file.

The library treats unit ambiguity and product identity as safety boundaries. For example, bare `mA` in a battery-capacity field is never silently rewritten as `mAh`; the raw value is retained and marked unresolved with a suggested correction. Likewise, identical files do not prove identical products. Model, connectivity, layout, core structure, and commercial SKU remain high-weight identity fields, and uncertain relationships stay separate or unresolved.

本目录只放置人工可读、可编辑、可被软件直接读取的核心规则。`rules.json` 是软件唯一读取入口；它同时包含确定性字段/值/单位/复合约束、产品身份边界和运行时语义提取规范。候选、冲突、未解决项与审核队列位于 `.internal/review`。完整原始证据位于 `.internal/evidence`，历史规则位于 `.internal/legacy`，报告位于 `.internal/reports`。

## 使用与编辑

先阅读 `Rules_Core.xlsx` 的 Overview；编辑规则时只更新 `rules.json`。每条规则只引用 `evidence_id`，完整证据不要复制回本目录。自动晋级仅在独立文档、产品数、语义/类型/单位一致性、反例检查和留出样本回归均通过时发生；其余内容会继续留在 `.internal/review`，不会阻塞 AI 在运行时保存带来源的原文提取。

## 单位安全边界（0.4.1）

`mA` 仅在最大电流、充电电流、工作电流、待机电流或睡眠电流等电流字段中按电流处理。电池、电池规格、电池容量字段中的裸 `mA/ma` 绝不自动换算或改写为 `mAh`：保留 `raw_value`，并标记 `suspected_unit_typo: true`、`suggested_unit: mAh`、`status: unresolved`。

## 安全边界与回滚

“文件相同”与“产品相同”不同；型号、连接方式、布局、核心结构和商业 SKU 是高权重身份字段。规则生成只读扫描原始资料，解析中间结果保存在 `<workspace>/work/semantic_ir`，不会修改源文件。无法判断的产品关系保持分离或 `unresolved`；内部历史材料均已收口在 `.internal/`。
