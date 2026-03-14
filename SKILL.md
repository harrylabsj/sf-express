# 顺丰速运助手 (SF Express Skill)

CLI工具，专为顺丰速运用户提供深度集成的快递服务。

## 功能

- **单号查询** - 实时追踪顺丰快递物流状态
- **智能识别** - 自动识别顺丰单号（SF开头或12-15位数字）
- **批量查询** - 同时查询多个顺丰单号
- **时效预估** - 查询顺丰各产品时效（标快、特快、冷运等）
- **运费估算** - 根据重量和距离估算运费
- **网点查询** - 查找附近顺丰网点和丰巢柜
- **寄件下单** - 快速预约上门取件
- **电子面单** - 生成电子面单
- **订阅提醒** - 物流状态变更自动通知
- **历史记录** - 保存查询和寄件历史

## 顺丰产品类型

| 产品 | 代码 | 时效 | 适用场景 |
|------|------|------|---------|
| 顺丰标快 | standard | 1-2天 | 普通文件、包裹 |
| 顺丰特快 | express | 次日达 | 紧急文件、时效要求高 |
| 顺丰即日 | same_day | 当日达 | 同城急件 |
| 顺丰冷链 | cold_chain | 1-2天 | 生鲜、医药 |
| 顺丰重货 | heavy | 2-3天 | 大件物品 |
| 顺丰国际 | international | 3-7天 | 跨境快递 |
| 顺丰特惠 | economy | 2-3天 | 非紧急、经济型 |

## 安装

```bash
cd ~/.openclaw/skills/sf-express
pip install -r requirements.txt
```

## 使用方法

### 1. 单号查询
```bash
# 查询单个单号
python sfexpress.py query SF1234567890123

# 自动识别顺丰单号
python sfexpress.py query 123456789012
```

### 2. 批量查询
```bash
python sfexpress.py batch SF1234567890123 SF9876543210987 123456789012
```

### 3. 时效查询
```bash
# 查询北京到上海的标准时效
python sfexpress.py time 北京 上海 --product standard

# 查询冷链时效
python sfexpress.py time 广州 深圳 --product cold_chain
```

### 4. 运费估算
```bash
# 估算1kg包裹从北京到上海的运费
python sfexpress.py price 北京 上海 --weight 1

# 估算10kg大件运费
python sfexpress.py price 北京 上海 --weight 10 --product heavy
```

### 5. 网点查询
```bash
# 查询附近网点
python sfexpress.py网点 北京市朝阳区

# 查询丰巢柜
python sfexpress.py locker 深圳市南山区
```

### 6. 寄件下单
```bash
# 预约上门取件
python sfexpress.py ship \
  --from "张三,13800138000,北京市朝阳区xxx" \
  --to "李四,13900139000,上海市浦东新区xxx" \
  --weight 2 \
  --product standard
```

### 7. 电子面单
```bash
# 生成电子面单
python sfexpress.py label \
  --from "张三,13800138000,北京市朝阳区xxx" \
  --to "李四,13900139000,上海市浦东新区xxx" \
  --product standard
```

### 8. 订阅提醒
```bash
# 订阅单号状态变更
python sfexpress.py subscribe SF1234567890123

# 取消订阅
python sfexpress.py unsubscribe SF1234567890123

# 查看所有订阅
python sfexpress.py subscriptions
```

### 9. 历史记录
```bash
# 查询历史
python sfexpress.py history --limit 20

# 搜索特定单号
python sfexpress.py history --search SF1234567890123
```

## 数据存储与安全

### 存储架构
- **主目录**: `~/.openclaw/data/sf-express/secure/` (加密存储)
- **查询历史**: `history.db` (SQLite数据库，AES-256加密)
- **订阅数据**: `subscriptions.enc` (加密存储)
- **寄件地址**: `addresses.enc` (加密存储)
- **配置文件**: `config.json` (加密存储)
- **加密密钥**: `.key` (权限 600)

### 隐私保护
1. **加密存储**: 所有敏感数据使用 Fernet 加密 (AES-256)
2. **本地优先**: 数据仅存储在本地，不上传云端
3. **数据控制**: 支持一键清除所有个人数据
4. **透明审计**: 可查看所有存储的文件和权限

### 隐私控制命令
```bash
# 查看隐私信息
python sfexpress.py privacy info

# 清除所有个人数据  
python sfexpress.py privacy clear

# 导出加密数据（备份）
python sfexpress.py privacy export
```

## 技术实现

- Python 3.8+ 异步架构
- SQLite 本地数据存储 (加密)
- 顺丰官方 API 集成
- 智能单号识别算法
- 地址解析和地理编码

## 依赖

- Python 3.8+
- `aiohttp>=3.9.0` (异步HTTP请求)
- `cryptography>=42.0.0` (加密库)
- `qrcode>=7.4.0` (二维码生成)
- `pillow>=10.2.0` (图像处理)
- `python-dateutil>=2.8.2` (日期处理)

## 注意事项

1. 首次使用会自动创建加密存储目录
2. 寄件功能需要实名认证
3. 运费估算为参考价格，实际价格以系统为准
4. 时效查询受天气、交通等因素影响
5. 建议使用前先进行运费和时效查询

## 更新日志

### v1.0.0
- 初始版本发布
- 支持顺丰全产品线查询
- 时效和运费估算功能
- 网点查询和寄件下单
- 本地加密存储
