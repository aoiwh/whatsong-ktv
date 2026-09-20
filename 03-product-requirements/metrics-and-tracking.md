# WhatSong｜我颂——指标与埋点方案

## 一、北极星指标

**每场 KTV 中通过工具选唱并反馈的曲目数。**

选择理由：
- 直接反映产品是否被用在真实 KTV 场景
- 同时覆盖“选歌”和“反馈”两个核心动作
- 不依赖用户频繁打开，只关注每次 KTV 是否真正用上

## 二、关键指标

| 指标 | 定义 | 目标值 | 用途 |
|---|---|---|---|
| 导入成功率 | 成功解析出歌名的导入次数 / 总导入次数 | ≥80% | 验证批量导入是否可用 |
| 激活率 | 首次使用后导入 ≥20 首的用户比例 | ≥50% | 验证首次体验是否低门槛 |
| 打标率 | 至少打 1 个标签的歌曲数 / 导入歌曲数 | ≥60% | 验证标签体系是否被接受 |
| 筛选使用率 | 创建场次前使用过筛选的用户比例 | ≥50% | 验证临场筛选是否被使用 |
| 场次记录率 | 筛选后创建场次的用户比例 | ≥40% | 验证闭环是否跑通 |
| 反馈率 | 场次内被反馈的歌曲数 / 场次歌曲数 | ≥40% | 验证一键反馈是否被接受 |
| 复用率 | 同一首歌被加入 ≥2 个场次的比例 | ≥30% | 验证歌单是否被持续使用 |
| 平均选歌时间 | 从进入筛选到创建场次的平均时长 | 下降 | 验证是否解决临场纠结 |

## 三、事件埋点方案

### 导入相关

| 事件名 | 触发时机 | 关键参数 |
|---|---|---|
| import_start | 进入导入页 | user_id, timestamp |
| import_paste | 粘贴文本 | text_length |
| import_parse_success | 解析成功 | parsed_count, duplicate_count |
| import_parse_fail | 解析失败 | reason |
| import_manual_fix | 手动修正 | fix_count |
| import_confirm | 确认导入 | final_count |

### 标签相关

| 事件名 | 触发时机 | 关键参数 |
|---|---|---|
| tag_create | 新增标签 | tag_name, is_preset |
| tag_apply | 给歌曲打标签 | song_id, tag_id, tag_count_on_song |
| tag_delete | 删除标签 | tag_id, affected_song_count |

### 筛选相关

| 事件名 | 触发时机 | 关键参数 |
|---|---|---|
| filter_start | 进入筛选页 | user_id |
| filter_select | 勾选标签 | tag_ids, logic_type |
| filter_empty | 筛选无结果 | tag_ids, logic_type |
| filter_result | 查看结果 | result_count |
| filter_add_to_session | 结果加入场次 | song_count |

### 场次相关

| 事件名 | 触发时机 | 关键参数 |
|---|---|---|
| session_create | 创建场次 | session_name, occasion_type, companion_type |
| session_add_song | 场次内添加歌曲 | session_id, song_id |
| session_reorder | 调整顺序 | session_id |
| session_exit | 退出场次 | session_id, saved_draft |

### 反馈相关

| 事件名 | 触发时机 | 关键参数 |
|---|---|---|
| feedback_submit | 提交反馈 | song_id, session_id, result |
| feedback_modify | 修改反馈 | song_id, old_result, new_result |
| tag_sync_manual | 手动同步全局标签 | song_id, tag_id, action |
| tag_sync_undo | 撤销同步 | song_id, tag_id |

### 统计相关

| 事件名 | 触发时机 | 关键参数 |
|---|---|---|
| stats_view | 查看统计页 | user_id |
| stats_song_detail | 查看单首歌统计 | song_id, sing_count |

## 四、指标口径说明

### 导入成功率

```
import_parse_success / (import_parse_success + import_parse_fail)
```

### 激活率

```
首次使用后导入 ≥20 首的用户数 / 首次使用用户数
```

### 打标率

```
至少打 1 个标签的歌曲数 / 导入歌曲总数
```

### 筛选使用率

```
创建场次前使用过筛选的用户数 / 创建场次用户数
```

### 场次记录率

```
筛选后创建场次的用户数 / 使用过筛选的用户数
```

### 反馈率

```
场次内被反馈的歌曲数 / 场次内歌曲总数
```

### 复用率

```
被加入 ≥2 个场次的歌曲数 / 被加入场次的歌曲总数
```

## 五、漏斗分析

### 主漏斗

```
进入产品 → 导入歌单 → 打标签 → 筛选 → 创建场次 → 反馈 → 再次使用
```

### 各环节关注点

| 环节 | 关注指标 | 流失风险 |
|---|---|---|
| 进入产品 | 首次打开率 | 不知道产品能做什么 |
| 导入歌单 | 导入成功率、激活率 | 解析失败、操作复杂 |
| 打标签 | 打标率 | 标签太多、选择疲劳 |
| 筛选 | 筛选使用率、无结果率 | 标签组合过严、无结果 |
| 创建场次 | 场次记录率 | 流程太长、强制填写 |
| 反馈 | 反馈率 | 反馈太重、不想复盘 |
| 再次使用 | 复用率 | 低频场景、没有提醒 |

## 六、验证目标

### MVP 阶段要验证

1. 批量导入是否能成功解析用户真实文本
2. 用户是否愿意打标签
3. 用户是否会在 KTV 前使用筛选
4. 用户是否愿意一键反馈
5. 用户是否会再次使用

### MVP 阶段不验证

- 商业化
- 社交传播
- AI 能力
- 多端同步
- 大规模并发

## 七、数据采集原则

- 不采集用户真实姓名、手机号、位置
- 不采集歌曲播放行为（产品无播放功能）
- 不采集社交关系
- 只采集产品使用行为
- 数据用于验证产品假设，不用于推荐或广告
