#### API

## User

### `/api/user/login/pw` `POST`

* `application/x-www-form-urlencoded`
* 密码登录

| 请求参数  | 类型 | 说明                |
| --------- | ---- | ------------------- |
| loginName | 必选 | 登录名（手机/邮箱） |
| password  | 必选 | 密码                |

| 返回参数     | 说明         |
| ------------ | ------------ |
| status       | 状态码       |
| data         | 返回消息     |
| token        | 用户token    |
| refreshToken | refreshToken |
| uid        | 用户 ID    |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"请输入注册时用的邮箱或者手机号呀"` | `loginName` 为空 |
| `false` | `"喵，你没输入密码么？"` | `password` 为空 |
| `false` | `"用户名或密码错误"` | `loginName` 不存在 |
| `false` | `"用户名或密码错误"` | `loginName` 与 `password` 不匹配 |
| `true` | `""` | `loginName` 与 `password` 匹配 |

### `/api/user/login/sms` `POST`

* `application/x-www-form-urlencoded`
* 短信登录

| 请求参数  | 类型 | 说明     |
| ------- | ---- | ------- |
| phone  |  必选  |  手机号  |
| verify_code | 必选 | 验证码  |

| 返回参数     | 说明         |
| ----------- | ------------ |
| status       | 状态码       |
| data         | 返回消息     |
| token        | 用户token    |
| refreshToken | refreshToken |
| uid        | 用户 ID    |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"手机号不能为空哦"` | `phone` 为空 |
| `false` | `"短信验证码不能为空"` | `verify_code` 为空 |
| `false` | `"验证码错误"` | `phone` 与 `verify_code` 不匹配 |
| `false` | `"未发送验证码"` | `verify_code` 无对应验证码 |
| `true` | `""` | `phone` 与 `verify_code` 匹配 |

### `/api/user/register` `POST`

* `application/x-www-form-urlencoded`
* 用户注册；先调用接口发送验证码， 并检查用户名及密码的规范性，确认数据符合规范之后再发送表单

| 请求参数    | 类型 | 说明        |
| ----------- | ---- | ----------- |
| username    | 必选 | 用户名/账号 |
| password    | 必选 | 密码        |
| phone       | 必选 | 手机号      |
| verify_code | 必选 | 验证码      |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"用户名不能为空"` | `username` 为空 |
| `false` | `"用户名太长了"` | `username` 长度超过 15 个字节 |
| `false` | `"密码不能小于6个字符"` | `password` 长度少于 6 个字节 |
| `false` | `"密码不能大于16个字符"` | `password` 长度超过 16 个字节 |
| `false` | `"手机号不可为空"` | `phone` 为空 |
| `false` | `"该手机号已经被注册"` | `phone` 已被注册 |
| `false` | `"请输入验证码"` | `verify_code` 为空 |
| `false` | `"未发送验证码"` | `verify_code` 无对应验证码 |
| `false` | `"验证码错误"` | `verify_code` 与对应验证码不符 |
| `true` | `"注册成功！"` | 参数合法 |

### `/api/user/info/:uid` `GET`

* 获取 UID 为 `:uid` 的用户的个人信息

| 请求参数 | 类型 | 说明    |
| ------- | ---- | ------ |
| uid     | 必选 | 用户 ID |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"UID 无效"` | `uid`为空或无效 |
| `true` | 参见下述代码 | 参数合法 |

```js
{
    Avatar: String
    Uid: Number,
    Username: String,
    RegDate: String,
    Statement: String,
    Exp: Number,
    Coins: Number,
    BCoins: Number,
    Birthday: String,
    Gender: String,
    Videos: Array, // []Video；投稿视频数组（切片）
    Saves: Array, // []Video；收藏夹视频数组（切片）
    Followers: Number, // 关注数
    Followings: Number, // 粉丝数
    TotalViews: Number, //总浏览数
    TotalLikes: Number, //总喜欢数
}
```

### `/api/user/avatar` `PUT`

* `multipart/form-data` 
* 修改/添加头像；

| 请求参数         | 类型 | 说明                         |
| ---------------- | ---- | ---------------------------- |
| avatar           | 必选 | 头像（二进制文件）             |
| token            | 必选 | token                        |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"头像无效"` | `avatar` 无效或为空 |
| `false` | `"头像文件过大"` | 头像大于2MB |
| `false` | `"上传失败"` | 上传失败 |
| `true` | `"上传成功"` | 上传成功 |

### `/api/user/password` `PUT`

* `application/x-www-form-urlencoded` 
* 修改密码；

| 请求参数         | 类型 | 说明                         |
| ---------------- | ---- | ---------------------------- |
| account | 必选 | 账号 手机号/邮箱地址 |
| code    | 必选 | 验证码             |
| new_password  | 必选 | 新密码                   |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"账号为空"` | `account`为空 |
| `false` | `"账号不存在"` | `account`未注册 |
| `false` | `"账号无效"` | `account`不合法 |
| `false` | `"验证码为空"` | `code`为空 |
| `false` | `"验证码错误"` | `code`错误 |
| `false` | `"未发送验证码"` | `code`无对应验证码 |
| `false` | `"密码不能小于6个字符"` | `new_password` 长度少于 6 个字节 |
| `false` | `"密码不能大于16个字符"` | `new_password` 长度超过 16 个字节 |
| `true` | `""` | 修改成功 |

### `/api/user/email` `PUT`

* `application/x-www-form-urlencoded` 
* 修改/添加email

| 请求参数    | 类型 | 说明                         |
| ----------- | ---- | ---------------------------- |
| old_account | 必选 | 原有设备账号 手机号/邮箱地址 |
| old_code    | 必选 | 原有设备验证码               |
| new_email   | 必选 | 新email                      |
| new_code    | 必选 | 新email验证码                |
| token       | 必选 | token                |

| **status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"原账号为空"` | `old_account`为空 |
| `false` | `"原账号不存在"` | `old_account`未注册 |
| `false` | `"原账号无效"` | `old_account`不合法 |
| `false` | `"原账号验证码为空"` | `old_code`为空 |
| `false` | `"原账号验证码错误"` | `old_code`错误 |
| `false` | `"未发送验证码"` | `old_code` 无对应验证码 |
| `false` | `"新邮箱为空"` | `new_email`为空 |
| `false` | `"新邮箱已被使用"` | `new_email`已注册 |
| `false` | `"新邮箱无效"` | `new_email`不合法 |
| `false` | `"新邮箱验证码为空"` | `new_code`为空 |
| `false` | `"新邮箱验证码错误"` | `new_code`错误 |
| `false` | `"未发送验证码"` | `new_code` 无对应验证码 |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `true` | `""` | 修改成功 |
"视频 ID 
### `/api/user/phone` `PUT`

* `application/x-www-form-urlencoded`
* 修改phone

| 请求参数    | 类型 | 说明                         |
| ----------- | ---- | ---------------------------- |
| old_account | 必选 | 原有设备账号 手机号/邮箱地址 |
| old_code    | 必选 | 原有设备验证码               |
| new_phone   | 必选 | 新手机号                     |
| new_code    | 必选 | 新手机验证码                 |
| token       | 必选 | token                |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"原账号为空"` | `old_account`为空 |
| `false` | `"原账号不存在"` | `old_account`未注册 |
| `false` | `"原账号无效"` | `old_account`不合法 |
| `false` | `"原账号验证码为空"` | `old_code`为空 |
| `false` | `"原账号验证码错误"` | `old_code`错误 |
| `false` | `"未发送验证码"` | `old_code` 无对应验证码 |
| `false` | `"新手机号为空"` | `new_phone`为空 |
| `false` | `"新手机号已被使用"` | `new_phone`已注册 |
| `false` | `"新手机号无效"` | `new_phone`不合法 |
| `false` | `"新手机号验证码为空"` | `new_code`为空 |
| `false` | `"新手机号验证码错误"` | `new_code`错误 |
| `false` | `"未发送验证码"` | `new_code` 无对应验证码 |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `true` | `""` | 修改成功 |

### `/api/user/username` `PUT`

* `application/x-www-form-urlencoded`
* 修改用户名，消耗6个硬币

| 请求参数      | 类型 | 说明       |
| ------------- | ---- | ---------- |
| token         | 必选 | token      |
| new_username | 必选 | 新用户名 |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"昵称不可为空"` | `new_username` 为空 |
| `false` | `"昵称太长了"` | `new_username` 大于 15 个字节 |
| `false` | `"硬币不足"` | 硬币不足 |
| `true` | `""` | 修改成功 |

### `/api/user/statement` `PUT`

* `application/x-www-form-urlencoded`
* 修改签名；如果无new_statement则更改为默认签名

| 请求参数      | 类型 | 说明       |
| ------------- | ---- | ---------- |
| token         | 必选 | token      |
| new_statement | 可选 | 新个性签名 |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"签名太长了"` | `new_username` 大于 15 个字节 |
| `true` | `""` | 修改成功 |

### `/api/user/gender` `PUT`

* `application/x-www-form-urlencoded`
* 修改性别

| 请求参数   | 类型 | 说明                                    |
| ---------- | ---- | --------------------------------------- |
| token      | 必选 | token                                   |
| new_gender | 必选 | 新性别（感觉好怪）（`F`,`M`, `O`, `N`） |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"无效的性别"` | `new_gender` 不合法 |
| `true` | `""` | 修改成功 |

### `/api/user/birth` `PUT`

* `application/x-www-form-urlencoded`
* 修改出生日期

| 请求参数      | 类型 | 说明       |
| ------------- | ---- | ---------- |
| token         | 必选 | token      |
| new_birth    | 必选 | 新的出生日期，格式为`2006-01-02` |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"日期格式错误"` | `new_birth` 格式错误 |
| `false` | `"出生日期无效"` | `new_birth` 晚于现在 |
| `true` | `""` | 修改成功 |

### `/api/user/check-in` `PUT`

* `application/x-www-form-urlencoded`
* 日常签到，硬币+1，经验+5

| 请求参数      | 类型 | 说明       |
| ------------- | ---- | ---------- |
| token         | 必选 | token      |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token`失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"ALREADY_DONE"` | 已签到 |
| `true` | `"SUCCESS"` | 签到成功 |

### `/api/user/daily` `GET`

* 获取日常任务情况

| 请求参数      | 类型 | 说明       |
| ------------- | ---- | ---------- |
| token         | 必选 | token      |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token`失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `true` | 参见以下代码 | 获取成功 |

```js
{
	"check-in": true | false,
	"view": true | false,
	"coin": Number >= 0 && Number <= 50
}
```

### `/api/user/follow` `POST`

* `application/x-www-form-urlencoded`
* 关注或取消关注某用户

| 请求参数      | 类型 | 说明       |
| ------------- | ---- | ---------- |
| uid         | 必选 | 被关注者 UID      |
| token         | 必选 | 关注者 token      |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token`失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"UID 无效"` | `uid`为空或无效 |
| `false` | `"你时刻都在关注自己"` | 自己关注自己 |
| `true` | `true` | 关注成功 |
| `true` | `false` | 取消关注成功 |

### `/api/user/follow` `GET`

* 获取用户之间的关注情况

| 请求参数      | 类型 | 说明       |
| ------------- | ---- | ---------- |
| a        | 必选 | A 用户 UID      |
| b         | 必选 | B 用户 UID      |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"参数无效"` | `a` 或 `b` 为空或无效 |
| `true` | `-1` | B 关注 A |
| `true` | `0` | AB 互不关注 |
| `true` | `1` | A 关注 B |
| `true` | `2` | AB 互相关注 |

## Verify

###  `/api/verify/token` `GET`

* 使用refreshToken获取新token

| 请求参数     | 说明         |
| ------------ | ------------ |
| refreshToken | refreshToken |


| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"refreshToken失效"` | refreshToken失效 |
| `false` | "refreshToken不正确或系统错误" | refreshToken不正确或系统错误 |
| `true` | 新的token | 成功 |

### `/api/verify/sms/general` `POST`

* `application/x-www-form-urlencoded`
* 向 `phone` 发送短信验证码

| 请求参数 | 类型 | 说明   |
| -------- | ---- | ------ |
| phone    | 必选 | 手机号 |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"手机号不可为空"` | `phone` 为空 |
| `false` | `"手机号不合法"` | `phone` 不合法 |
| `true` | `""` | 发送验证码成功 |

### `/api/verify/sms/register` `POST`

* `application/x-www-form-urlencoded`
* 注册时向 `phone` 发送短信验证码

| 请求参数 | 类型 | 说明   |
| -------- | ---- | ------ |
| phone    | 必选 | 手机号 |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"手机号不可为空"` | `phone` 为空 |
| `false` | `"手机号已被使用"` | `phone` 已被使用 |
| `false` | `"手机号不合法"` | `phone` 不合法 |
| `true` | `""` | 发送验证码成功 |

### `/api/verify/sms/login` `POST`

* `application/x-www-form-urlencoded`
* 登录时向 `phone` 发送短信验证码

| 请求参数 | 类型 | 说明   |
| -------- | ---- | ------ |
| phone    | 必选 | 手机号 |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"手机号不可为空"` | `phone` 为空 |
| `false` | `"手机号未被注册"` | `phone` 未注册 |
| `false` | `"手机号不合法"` | `phone` 不合法 |
| `true` | `""` | 发送验证码成功 |

### `/api/verify/email` `POST`

* `application/x-www-form-urlencoded`
* 发送邮箱验证码

| 请求参数 | 类型 | 说明  |
| -------- | ---- | ----- |
| email    | 必选 | email |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"邮箱不可为空"` | `email` 为空 |
| `false` | `"邮箱格式不合法"` | `email` 不合法 |
| `true` | `""` | 发送验证码成功 |

## Check

### `/api/check/username` `GET`

* 检验用户名是否合法

| 请求参数 | 类型 | 说明   |
| -------- | ---- | ------ |
| username | 必选 | 用户名 |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"请告诉我你的昵称吧"` | `username` 为空 |
| `false` | `"昵称过长"` | `username` 长度超过 14 个字节 |
| `false` | `"昵称已存在"` | `username` 已被使用 |
| `true` | `""` | `username` 合法 |

### `/api/check/phone` `GET`

* 检验手机号是否合法

| 请求参数 | 类型 | 说明   |
| -------- | ---- | ------ |
| phone | 必选 | 手机号 |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"请告诉我你的手机号吧"` | `phone` 参数为空 |
| `false` | `"手机号已被使用"` | 手机号已被使用 |
| `true` | `""` | 手机号合法 |

## Video

### `/api/video/video` `GET`

* 请求视频；

| 请求参数      | 类型 | 说明         |
| ------------ | ---- | ----------- |
| video_id     | 必选 | 视频 ID      |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"视频 ID 不可为空"` | `video_id`为空 |
| `false` | `"视频 ID 无效"` | `video_id` 无效 |
| `true` | 参见下述代码 | 参数合法 |

```js
let data = {
    Id: Number, //int64, 视频 ID
    Video: String, // string, 视频地址
    Cover: String, // string, 封面地址
    Title: String, // string, 视频标题
    Length: String, // string, 视频时长
    Channel: String, // string, 分区，字符串编号，参见`channel.md`
    Label: Array, // []string, 标签，字符串切片
    Description: String, // string, 简介
    Author: Number, // int64, 作者 UID
    Time: String, // Time, 上传时间
    Views: Number, // int64, 播放次数
    Likes: Number, // int64, 点赞数量
    Coins: Number, // int64, 投币数量
    Saves: Number, // int64, 收藏数量
    Shares: Number, // int64, 分享数量
    Danmakus: Array, // []Danmaku, 弹幕，弹幕切片
}
```

### `/api/video/video` `POST`

* `multipart/form-data` 
* 视频投稿；

| 请求参数       | 类型 | 说明                        |
| ------------ | ---- | --------------------------- |
| video        | 必选 | 视频（视频格式，二进制文件）      |
| cover        | 必选 | 封面（图片格式，二进制文件）       |
| title        | 必选 | 标题，至多 80 字              |
| length       | 必选 | 视频时长，格式：`"114:51"`     |
| channel      | 必选 | 分区，字符串编号，参见`channel.md` |
| label        | 必选 | 标签，至多 10 个，数组转 json 字符串 |
| description  | 可选 | 简介，至多 250 字             |
| token        | 必选 | token                       |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"视频不可为空"` | `video`为空 |
| `false` | `"视频体积不可大于 2GB"` | `video`体积大于 2GB |
| `false` | `"视频上传失败"` | `video`上传失败 |
| `false` | `视频格式无效` | `video`格式无效 |
| `false` | `"封面不可为空"` | `cover`为空 |
| `false` | `"封面体积不可大于 2MB"` | `cover`体积大于 2MB |
| `false` | `"封面上传失败"` | `cover`上传失败 |
| `false` | `"封面格式无效"` | `cover`格式无效 |
| `false` | `"标题不可为空"` | `title`为空 |
| `false` | `"标题过长"` | `title`长度大于 80 |
| `false` | `"时长无效"` | `length`为空或无效 |
| `false` | `"分区无效"` | `channel`为空或无效 |
| `false` | `"标签无效"` | `label`为空、无效、重复或过多 |
| `false` | `"简介过长"` | `description`长度大于 250 |
| `true` | 视频 ID | 上传成功 |

### `/api/video/danmaku` `GET`

* 获取弹幕

| 请求参数     | 类型 | 说明                        |
| ----------- | ---- | --------------------------- |
| video_id    | 必选 | 视频ID                      |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"视频 ID 不可为空"` | `video_id`为空 |
| `false` | `"视频 ID 无效"` | `video_id` 无效 |
| `true` | 参见以下代码 | 获取成功 |

```js
[{
    // Integer; 弹幕 ID;
    "Id": 1,
    // Integer; 视频 ID;
    "VideoId": 1, 
    // Integer; 发送弹幕的用户 ID;
    "UserId": 1,
    // String; 弹幕内容;
    "Value": "2333",
    // String[6]; 弹幕颜色; 
    "Color": "FFFFFF",
    // String; 弹幕类型;
    "Type": "scroll" || "top" || "bottom",
    // Time; 弹幕发送时间;
    "Time": "1970/1/1 00:00:00", 
    // Integer; 弹幕弹出位置，单位为秒; 例如此处弹幕发送于 1min 54s 处
    "Location": 114,
}, {
    // 如上...
}]
```

### `/api/video/danmaku` `POST`

* `application/json` 
* 发送弹幕；

| 请求参数    | 类型 | 说明    |
| ---------- | ---- | ------ |
| token      | 必选 | token   |
| video_id   | 必选 | 视频 ID |
| value      | 必选 | 弹幕内容 |
| color      | 必选 | 弹幕颜色 |
| type       | 必选 | 弹幕类型 |
| location   | 必选 | 弹幕位置 |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"弹幕不可为空"` | `value`为空 |
| `false` | `"弹幕过长"` | `value`大于 100 字符 |
| `false` | `"参数无效"` | `video_id`、`color`、`type`、`location`为空或无效 |
| `true` | 参见以下代码 | 发布成功 |

```js
{
    // Integer; 弹幕 ID;
    "Id": 1,
    // Integer; 视频 ID;
    "VideoId": 1, 
    // Integer; 发送弹幕的用户 ID;
    "UserId": 1,
    // String; 弹幕内容;
    "Value": "2333",
    // String[6]; 弹幕颜色; 
    "Color": "FFFFFF",
    // String; 弹幕类型;
    "Type": "scroll" || "top" || "bottom",
    // Time; 弹幕发送时间;
    "Time": "1970/1/1 00:00:00", 
    // Integer; 弹幕弹出位置，单位为秒; 例如此处弹幕发送于 1min 54s 处
    "Location": 114,
}
```

### `/api/video/like` `GET`

* 获取点赞状态；

| 请求参数    | 类型 | 说明    |
| ---------- | ---- | ------ |
| token      | 必选 | token   |
| video_id   | 必选 | 视频 ID |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"视频 ID 无效"` | `video_id`为空或无效 |
| `true` | `true` | 已点赞 |
| `true` | `false` | 未点赞 |

### `/api/video/like` `POST`

* `application/x-www-form-urlencoded` 
* 点赞/取消点赞；

| 请求参数    | 类型 | 说明    |
| ---------- | ---- | ------ |
| token      | 必选 | token   |
| video_id   | 必选 | 视频 ID |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"视频 ID 无效"` | `video_id`为空或无效 |
| `true` | `true` | 点赞成功 |
| `true` | `false` | 取消点赞成功 |

### `/api/video/coin` `GET`

* 获取投币状态；

| 请求参数    | 类型 | 说明    |
| ---------- | ---- | ------ |
| token      | 必选 | token   |
| video_id   | 必选 | 视频 ID |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"视频 ID 无效"` | `video_id`为空或无效 |
| `true` | `true` | 已投币 |
| `true` | `false` | 未投币 |

### `/api/video/coin` `POST`

* `application/x-www-form-urlencoded` 
* 投币；
* 投币成功后投币者硬币数 -1，UP主硬币数 +1；
* 每投币一次，投币者经验+10，每日上限50；
* UP主的视频每被投币一次，经验+1，每日无上限；

| 请求参数    | 类型 | 说明    |
| ---------- | ---- | ------ |
| token      | 必选 | token   |
| video_id   | 必选 | 视频 ID |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"视频 ID 无效"` | `video_id`为空或无效 |
| `false` | `"硬币不足"` | 硬币不足 |
| `false` | `"不能给自己投币哦"` | 不能给自己的视频投币 |
| `true` | `true` | 投币成功 |
| `true` | `false` | 投币失败（已投币） |

### `/api/video/save` `POST`

* `application/x-www-form-urlencoded` 
* 收藏；

| 请求参数    | 类型 | 说明    |
| ---------- | ---- | ------ |
| token      | 必选 | token   |
| video_id   | 必选 | 视频 ID |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"视频 ID 无效"` | `video_id`为空或无效 |
| `true` | `true` | 收藏成功 |
| `true` | `false` | 取消收藏成功 |

### `/api/video/view` `POST`

* `application/x-www-form-urlencoded` 
* 观看数 +1；若提供 `token` ，则浏览用户经验+5，每日上限5

| 请求参数 | 类型 | 说明       |
| ------- | ---- | --------- |
| video_id | 必选 | 视频ID       |
| token | 可选 | token     |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"视频 ID 不可为空"` | `video_id`为空 |
| `false` | `"视频 ID 无效"` | `video_id` 无效 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `true` | `""` | 提交成功 |
| `true` | `"ALREADY_DONE"` | 提供`token`且用户今日已浏览 |
| `true` | `"SUCCESS"` | 提供`token`且为用户今日首次浏览 |

### `/api/video/share` `POST`

* `application/x-www-form-urlencoded` 
* 视频分享数 +1

| 请求参数 | 类型 | 说明       |
| ------- | ---- | --------- |
| video_id | 必选 | 视频ID       |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"视频 ID 不可为空"` | `video_id`为空 |
| `false` | `"视频 ID 无效"` | `video_id` 无效 |
| `true` | `""` | 提交成功 |

### `/api/video/comments` `GET`

* 获取评论

| 请求参数 | 类型 | 说明       |
| ------- | ---- | --------- |
| video_id | 必选 | 视频ID    |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"视频 ID 不可为空"` | `video_id`为空 |
| `false` | `"视频 ID 无效"` | `video_id` 无效 |
| `true` | 参见以下代码 | 提交成功 |

```js
[{
	Id: Number, // 评论 ID
	VideoId: Number, // 视频 ID
	UserId: Number, // 评论者 ID
	Value: String, // 评论内容
	Time: String, // 评论时间，格式：2021-02-06 19:20
	Likes: Number //赞数
},{
	// 同上
}]
```

### `/api/video/comment` `POST`

* `application/x-www-form-urlencoded` 
* 提交评论

| 请求参数 | 类型 | 说明       |
| ------- | ---- | --------- |
| token | 必选 | token      |
| video_id | 必选 | 视频ID   |
| comment | 必选 | 评论信息    |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"NO_TOKEN_PROVIDED"` | `token`为空 |
| `false` | `"TOKEN_EXPIRED"` | `token` 失效 |
| `false` | `"PRASE_TOKEN_ERROR"` | `token`解析失败 |
| `false` | `"视频 ID 不可为空"` | `video_id`为空 |
| `false` | `"视频 ID 无效"` | `video_id` 无效 |
| `false` | `"评论内容不可为空"` | `comment` 为空 |
| `false` | `"评论内容过长"` | `comment` 长度大于 1024 |
| `true` | 参见以下代码 | 提交成功 |

```js
{
	Id: Number, // 评论 ID
	VideoId: Number, // 视频 ID
	UserId: Number, // 评论者 ID
	Value: String, // 评论内容
	Time: String, // 评论时间，格式：2021-02-06 19:20
	Likes: Number //赞数
}
```


### `/api/video/recommend` `GET`

* 获取推荐视频列表；
* 获取成功时返回 `data` 长度（至多）为 20；

| 请求参数    | 类型 | 说明    |
| ---------- | ---- | ------ |
| video_id | 必选 | 视频 ID |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"视频 ID 无效"` | `video_id`为空或无效 |
| `true` | `[{Video},{...}]` | 获取成功 |

## Home

### `/api/home/sections` `GET`

* 请求首页推荐视频；

| status | data | 说明   |
| -------- | ---- | ------ |
| `true` | 参见下述代码 | 无 |

```js
let data = [19]{
	list: [8]Video, // 视频列表
	rank: [10]Video  // 视频排行
}
// data 长度为 19，依次为：
// 动画，番剧，国创，音乐，舞蹈，游戏，知识，数码，生活，美食，动物圈，鬼畜，时尚，资讯，娱乐，电影，电视剧，影视，纪录片
```

### `/api/home/search` `GET`

* 搜索视频；

| 请求参数    | 类型 | 说明    |
| ---------- | ---- | ------ |
| keywords | 必选 | 搜索关键词 |

| status | data | 说明   |
| -------- | ---- | ------ |
| `false` | `"搜索内容不可为空"` | `keywords`为空 |
| `true` | 内含用户信息的视频数组 | `keywords`不为空 |

* 如果搜索结果为空则返回空数组，而非 null

# 一般规定

如无特殊说明，则返回一个以下格式的 json：

```javascript
{
    status: true, // true：成功， false：失败
    data: "" // 提示信息
}
```