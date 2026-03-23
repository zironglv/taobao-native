# 淘宝桌面版MCP工具套件 - 安装指南

## 包含内容

| 组件 | 说明 | 版本 |
|------|------|------|
| taobao-native | 淘宝购物核心工具 | v1.1.0 |
| taobao-mcp-benchmark | 评测框架 | v1.4.1 |

---

## 一、安装步骤

### 1. 解压文件

```bash
# 解压到 CoPaw 的 active_skills 目录
unzip taobao-mcp-skills.zip -d ~/.copaw/active_skills/
```

### 2. 验证安装

```bash
# 检查是否安装成功
ls ~/.copaw/active_skills/taobao-native/SKILL.md
ls ~/.copaw/active_skills/taobao-mcp-benchmark/SKILL.md
```

### 3. 重启 CoPaw（如需要）

如果 CoPaw 正在运行，重启后自动加载新skill。

---

## 二、前置要求

### 必须安装的工具

1. **淘宝桌面客户端**
   - 下载地址：https://desktop.taobao.com/
   - 必须已登录账号

2. **mcporter CLI**
   ```bash
   # 检查是否安装
   mcporter --version
   ```

3. **Node.js**（用于生成评测报告）
   ```bash
   node --version  # 需要 v16+
   ```

### 评测报告依赖（可选）

如需生成 Word 格式评测报告：
```bash
npm install -g docx
```

---

## 三、使用方法

### 3.1 基本购物操作

```bash
# 搜索商品
mcporter call taobao-native.search_products keyword=保温杯 --output json

# 加入购物车（自动选择SKU）
mcporter call taobao-native.add_to_cart --args '{"itemId":"601542707852"}' --output json

# 查看购物车
mcporter call taobao-native.navigate --args '{"page":"cart"}' --output json

# 查看订单
mcporter call taobao-native.navigate --args '{"page":"order"}' --output json
```

### 3.2 运行评测

在 CoPaw 中说：
```
帮我评测一下淘宝MCP工具
```

AI会自动执行5个评测任务并生成报告。

---

## 四、优化策略（已验证有效）

本版本包含以下优化策略，已通过对比评测验证：

| 策略 | 效果 | 使用方法 |
|------|------|----------|
| filter参数精确扫描 | 减少72%调用 | `scan_page_elements filter="关键词"` |
| maxLength限制输出 | 减少token消耗 | `read_page_content maxLength=3000` |
| 复合工具优先 | 减少手动步骤 | 使用 `add_to_cart`、`open_chat_from_*` |

### 评测结果

| 指标 | 优化前 | 优化后 | 变化 |
|------|--------|--------|------|
| 工具调用次数 | 25次 | 7次 | ⬇️ -72% |
| 总耗时 | 261秒 | 254秒 | ⬇️ -3% |
| 任务完成率 | 100% | 100% | 持平 |
| 综合评分 | 9.05 | 9.16 | ⬆️ +1.2% |

---

## 五、工具清单

### taobao-native 提供的工具

| 工具 | 功能 | 说明 |
|------|------|------|
| navigate | 页面导航 | 跳转到首页、购物车、订单等 |
| navigate_to_url | 打开URL | 在淘宝客户端打开任意链接 |
| get_current_tab | 获取当前标签页 | 返回URL和标题 |
| search_products | 搜索商品 | 返回商品列表（标题、价格、店铺） |
| add_to_cart | 加入购物车 | 自动处理SKU选择 |
| read_page_content | 读取页面内容 | 提取可见文本 |
| scan_page_elements | 扫描可交互元素 | 返回带序号的元素列表 |
| click_element | 点击元素 | 按序号或文本点击 |
| input_text | 输入文本 | 向输入框填入文字 |
| scroll_page | 滚动页面 | 上下滚动或滚动到指定元素 |
| get_browse_history | 获取浏览历史 | 商品、搜索词、店铺历史 |
| open_chat_from_cart | 从购物车发消息 | 查找商品→进入旺旺→发消息 |
| open_chat_from_order | 从订单发消息 | 查找订单→进入旺旺→发消息 |
| open_chat_from_search | 搜索后发消息 | 搜索商品→进入旺旺→发消息 |
| send_chat_message | 发送聊天消息 | 在当前旺旺页面发送消息 |
| inspect_page | 诊断页面状态 | 排查操作失败原因 |

### taobao-mcp-benchmark 评测任务

| 任务 | 权重 | 说明 |
|------|------|------|
| 淘金币签到 | 20% | 测试基础导航和识别 |
| 商品搜索+对比+加购 | 30% | 测试搜索和购物流程 |
| 订单管理 | 15% | 测试订单查询和筛选 |
| 购物车降价信息 | 20% | 测试购物车数据读取 |
| 客服咨询对话 | 15% | 测试旺旺聊天功能 |

---

## 六、常见问题

### Q: 工具调用失败怎么办？

1. 确认淘宝桌面客户端已启动并登录
2. 使用 `inspect_page` 诊断页面状态
3. 检查网络连接

### Q: 如何查看调试信息？

```bash
# 查看评测日志
cat ~/.copaw/tasks/benchmark_*/calls.log
cat ~/.copaw/tasks/benchmark_*/timing.log

# 查看截图
open ~/.copaw/tasks/benchmark_*/screenshots/
```

### Q: 如何升级？

直接覆盖安装即可：
```bash
unzip taobao-mcp-skills.zip -d ~/.copaw/active_skills/
```

---

## 七、文件结构

```
~/.copaw/active_skills/
├── taobao-native/
│   └── SKILL.md          # 核心工具文档
└── taobao-mcp-benchmark/
    ├── SKILL.md          # 评测框架文档
    ├── templates/        # 报告模板
    ├── scripts/          # 生成脚本
    └── history/          # 评测历史
```

---

## 八、版本历史

| 版本 | 日期 | 更新内容 |
|------|------|----------|
| v1.1.0 | 2026-03-17 | 添加优化策略，减少72%调用 |
| v1.0.0 | 2026-03-16 | 初始版本 |

---

## 九、联系与反馈

如有问题或建议，请联系 CoPaw 开发团队。

---

*最后更新：2026-03-18*