# 学生报名 API

## 在分享链接页面中, 加载可以报名的专业列表

- 用户用手机或电脑打开分享链接之后, 会请求此接口, 获取学校正在招生的专业的列表
- 请求 url: /api/student-register/load-open-majors

| 参数名       | 是否必须          | 参数类型  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| version     | Yes | String | 表示当前呼叫方的版本号 |
| mobile      |  No      |   String | 当前用户的手机号码, 可以为空 |
| id_number | NO     |    String | 当前用户的身份证号码, 可以为空 |

- 说明: 如果提交了身份证或手机号, 表示前端页面识别出了用户, 需根据这些信息酌情加载专业数据.
- 返回数据请参考 /api/student-register/load-open-majors/mock.json

## 在点击某个专业的"详情"按钮之后, 会发出此请求, 获取专业的详情信息

- 请求 url: /api/student-register/load-major-detail

| 参数名       | 是否必须          | 参数类型  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| version     | Yes | String | 表示当前呼叫方的版本号 |
| id      |  yes      |   String | 请求加载的专业的 ID |

- 返回数据请参考 /api/student-register/load-major-detail/mock.json
- 返回数据中的说明:
  - major: 专业的基础数据
  - major.courses: 该专业所包含的课程的列表

## 在用户填写报名表的界面, 在用户填写了身份证号之后, 会调用此接口

- 此接口的目的, 是为了可以自动的帮助用户填充报名表单数据. 同时也用来查询自己的已报名课程
- 请求 url: /api/student-register/query-student-profile

| 参数名       | 是否必须          | 参数类型  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| version     | Yes | String | 表示当前呼叫方的版本号 |
| mobile      |  No      |   String | 当前用户的手机号码, 可以为空 |
| id_number | NO     |    String | 当前用户的身份证号码, 可以为空 |

- 说明: 在调用此接口的时候, 服务器端可以根据任意非空数据进行组合查询, 并返回匹配的记录
- 返回数据:
  - profile: 表示查询到的学生的记录
  - applied: 表示查询到的学生的以报名的专业列表. 无数据则返回空数组
    - status: 表示报名的结果, 分别为 审核中, 成功, 失败等状态的值

## 在用户填写报名表的界面, 输入了完整的身份证之后, 会调用此接口

- 此接口的目的, 是确保填写的身份证号码有效, 并且可以自动解析出其中的用户生日信息
- 请求 url: /api/student-register/verify-id-number

| 参数名       | 是否必须          | 参数类型  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| version     | Yes | String | 表示当前呼叫方的版本号 |
| id_number      |  Yes      |   Object | 用户的身份证号码|

- 说明: 在调用此接口前, 前端程序会验证长度必须为 18 位
- 返回数据 mock:
  - 当验证成功时:
    - message: 包含了解析出的用户生日, 格式为: Y年m月d日
  - 当报名失败时:
    - message: 表示验证失败的原因文字说明

## 在用户填写报名表的界面, 在点击提交之后的接口

- 此接口的目的, 是为让系统保存学生的报名表单
- 请求 url: /api/student-register/submit-form

| 参数名       | 是否必须          | 参数类型  | 说明  |
| ------------- |:-------------:| -----:| -----:|
| version     | Yes | String | 表示当前呼叫方的版本号 |
| form      |  Yes      |   Object | 用户的 profile 数据, 字段名与数据库名完全相同. ID 为空表示新学生, ID 不为空表示该 profile 已经存在 |
| recruitment_plan_id      |  Yes      |   String | 用户报名所关联的招生简章的 ID |

- 说明: 在调用此接口前, 页面会完成全部的数据格式验证工作. 
- 其中的生日字段不包含在提交的表单当中, 需要通过身份证信息去解析即可, 方法为 GradeAndYearUtil::IdNumberToBirthday($idNumber): Carbon
- 返回数据 mock:
  - 当报名成功时: /api/student-register/submit-form/mock_expect_success.json
  - 当报名失败时: /api/student-register/submit-form/mock_expect_fail.json
    - message: 表示无法报名的原因文字说明
