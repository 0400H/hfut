..
    **功能和改进**

    **小的改进**

    **接口改变**

    **行为改变**

    **问题修复**

    **文档**

    **其他杂项**

.. :changelog:

开发日志
---------------

1.0.0 (20160307)
++++++++++++++++

**功能和改进**

- 精简了架构,现在接口区分更清晰,现在支持单独的会话配置,同时不会再因动态绑定接口而无法进行代码提示
- 添加了 :func:`util.cal_term_code` 和 :func:`util.term_str2code` 计算学期代码
- 添加了 :meth:`models.GuestSession.get_selecting_lesson_time` 查询选课时间
- 添加 :func:`get_host_speed_rank`,由于宣城校区校内还有多个镜像站点,现在提供了测试地址速度排行的功能
- 现在能够自动更新会话保持登录状态了
- todo: 现在支持合肥校区的访问了

**小的改进**

- :func:`change_lesson` 现在能够判断当前是否能够选课
- :func:`get_lessons_can_be_selected` 导出的结果现在是格式化后的了
- :meth:`models.StudentSession.get_stu_timetable` 现在返回的上课周数为周数列表便于实际处理
- :class:`get_selected_lessons` 结果中的 ``费用`` 和 ``学分`` 两个字段从字符串分别改为了整型和浮点型
- 调整了 :meth:`models.GuestSession.get_teaching_plan` 的参数使使用更加方便
- 统一了 :meth:`models.StudentSession.get_code` 的结果键值为中文
- 现在登录时能够判断是否是煞笔的防注入系统导致无法登陆并且如果是宣城校区会自动选取可用地址重新登录


**接口改变**

- 去除了 ``const``, ``session``, ``api``, ``api_request_builder``, ``core``
- 将原来的 ``api`` 中所有的接口根据要求的登录权限不同分别迁移到了 :class:`models.GuestSession` 和 :class:`models.StudentSession`
- 将原来的 ``core`` 中的 ``@unstable``, ``@unfinish`` 迁移到了 ``log`` 模块中
- ``const`` 中的配置项迁移到了 :class:`BaseSession` 中, 现在的配置是会话级而不是全局的,这样可以方便的根据需要进行修改
- :func:`util.store_api_result` 迁移到了 :meth:`models.APIResult.store_api_result` 并稍微调整了一下参数
- 重新命名了大量接口使其更易理解, 同时纠正命名的错误, 接口的重命名状态如下
    - ``get_selecting_lesson_time`` -> ``get_system_state``
    - ``search_lessons`` -> ``search_course``
    - ``get_lesson_classes`` -> ``get_course_classes``
    - ``get_stu_info`` -> ``get_my_info``
    - ``get_stu_grades`` -> ``get_my_achievements``
    - ``get_stu_timetable`` -> ``get_my_curriculum``
    - ``get_stu_feeds`` -> ``get_my_fees``
    - ``get_optional_lessons`` -> ``get_optional_courses``
    - ``get_selected_lessons`` -> ``get_selected_courses``
    - ``is_lesson_selected`` -> ``check_courses``
    - ``get_lessons_can_be_selected`` -> ``get_selectable_courses``

**行为改变**

- 现在登录也看作是一个接口,进行了重构
- 现在所有的接口返回的都是 :class:`models.APIResult` 对象

**问题修复**

- 修复发送登录权限不一致时仍会发送请求的问题
- 修复 :class:`AuthSession` 初始化时参数判断逻辑错误
- 修复 :class:`models.APIRequest` 初始化时继承参数错误
- 修复 :func:`api.get_optional_lessons` 由于疏忽缺少一个参数
- 修复 :meth:`models.StudentSession.get_stu_timetable` 上课周数匹配情况的遗漏
- 修复 :meth:`models.GuestSession.search_lessons` 由于编码问题无法使用课程名称搜索的问题
- 修复 :func:`parser.parse_tr_strs` 触发异常时字符串格式错误的问题

**文档**

- 在**高级技巧**一章添加了例子

**其他杂项**

- 将默认的测试模块从 ``unitest`` 迁移到了 ``pytest``
- 添加大量测试,Python 版本覆盖 2.6-3.5


0.5.0 (20160225)
++++++++++++++++

- 重构 ``api_request_builder.GetLessonClasses``,
      现在可以返回课程已选人数, 课程容量, 时间地点等信息,
      同时修复了一些问题
- 添加 ``api.get_lessons_can_be_selected``,
      获取可以选上的课程教学班级
- 合并 ``api.select_lesson`` 和 ``api.delete_lesson`` 为
      ``api.change_lesson`` 并重构了逻辑
- 修改 ``api.is_lesson_selected`` 参数类型为 list,
      避免使用中重复调用导致发送大量冗余的请求
- 重构 ``parser.parse_tr_strs`` , 现在支持单个值输入输出
- 添加 ``parser.dict_list_2_tuple_set``
- 提升兼容性

0.4.2 (20160218)
++++++++++++++++

- 修复由于配置遗漏导致无法安装的问题

0.4.1 (20160217)
++++++++++++++++

- 修复一些潜在问题
- 更新文档

0.4.0 (20160216)
++++++++++++++++

- 删除缓存模块及相关接口
- 分离一般接口与请求接口, 去除了 ``g`` 对象, 只使用列表 ``all_api``
      保存注册的一般接口
- 将 ``AuthSession.catch_response`` 删除, 改用
      ``AuthSession.api_request``
- 新增了 ``model`` 模块, 包含 ``model.APIRequestBuilder``,
      ``model.APIRequest``, ``model.APIResult`` 三个类
- api 模块合并为单个文件, 添加了请求生成与响应处理的
      ``api_request_builder`` 模块
- 新的架构避免了 ``api`` 注册冗余以及 ``api`` 与 ``session``
      的交叉调用, 简化模型, 增加了灵活性, 并且不改变之前使用 session
      调用接口的方式
- 修改了 ``api.get_stu_info`` 中照片地址的生成方式

0.3.5 (20160208)
++++++++++++++++

- 修复 ``session.AuthSession`` 初始化时的逻辑错误
- 修改缓存 md5 计算方式
- 兼容 Python 3.X

0.3.4 (20151030)
++++++++++++++++

- 添加 MANIFEST.in
- 提交到了官方仓库

0.3.3 (20151030)
++++++++++++++++

- 修复 setup.py 配置中的一处错误
- 提交到了官方仓库

0.3.2 (20151030)
++++++++++++++++

- 修改持续集成通知
- 修复 anydbm 在不同环境下触发的 AttributeError: get

0.3.1 (20151030)
++++++++++++++++

- 修复接口注册前后的参数差异导致 ``cal_cache_md5``
      计算结果不正确的问题
- 添加了更多的测试用例

0.3.0 (20151029)
++++++++++++++++

- 修改 ``regist_api`` 为 ``register_api``
- 默认在安装uniout的情况下使用其输出unicode内容方便使用
- 改用元类来绑定接口, 提升声明对象时的效率
- 预定义了用户类型, ``user_type`` 参数使用预定义变量
- ``cal_gpa`` 精度改为5位小数, 与学校一致
- 添加缓存功能, 你可以通过一个全局的缓存管理对象管理缓存了,
      模块内置了 ``MemoryCache`` 和 ``FileCache``, 当然你也可以继承
      ``BaseCache`` 编写新的缓存管理对象, 模块会自动帮你注册

0.2.0 (20151025)
++++++++++++++++

- 调整了模块结构
- 分离了 ``session`` 与 接口, 通过一个统一的 ``AuthSession``
      自动绑定接口, 参数原来 ``StuLib`` 接口参数相同
- 区分了用户类型, AuthSession 即使没有登录也能访问公共接口了
- 添加了 ``regist_api`` ,
      现在你可以在不修改模块代码的情况下添加自己的接口了

0.1.3 (20150912)
++++++++++++++++

- 修复因 ``StuLib`` 初始化时未对 ``stu_id`` 进行类型转换而导致
      ``StuLib.get_stu_info`` 出错的问题

0.1.2 (20150912)
++++++++++++++++

- 修复安装时 README.md 缺失的问题

0.1.1 (20150912)
++++++++++++++++

- 添加了一些单元测试

0.1.0 (20150911)
++++++++++++++++

- 解决 ``requests`` 不能对 GBK 转 UTF8 无损转换的问题
- 添加 ``StuLib.catch_response`` , 抽象了响应的获取,
      提升了代码的可维护性

0.0.4 (20150910)
++++++++++++++++

- 修复了 ``StuLib.get_class_student``
      中由于教务网页代码严重的错误导致页面无法解析而不可用的问题
- 添加了 ``StuLib.get_class_student`` 的测试用例
- 由于 ``requests`` 返回的的网页无法做到无损转码, 将传递
      ``BeautifulSoup`` 的文档改为原始编码文档,将转码工作交给
      ``BeautifulSoup`` 处理, 但用到正则匹配的方法还存在此问题

0.0.3 (20150909)
++++++++++++++++

- 统一将返回的课程代码进行大写转换,
      避免因学校课程代码大小写的不统一产生不可预料的问题
- 重构了 ``StuLib.select_lesson`` , 现在支持更好地批量选课以及更好地结果处理过程
- 重构了 ``StuLib.delete_lesson`` , 现在支持批量删课以及更好地结果处理过程

0.0.2 (20150903)
++++++++++++++++

- 重构了 ``StuLib.select_lesson`` 的参数处理过程,
      由于第二次选课结束暂时没有完成对提交结果的处理
- 添加 Travis IC 持续集成工具

0.0.1 (20150902)
++++++++++++++++

- 修复 ``StuLib.get_class_info`` 出错
- 添加 教师信息查询 ``StuLib.get_teacher_info`` 功能
- 将 ``StuLib.get_url`` 的 ``code`` 修改为对应的方法名称
- 修复 ``StuLib.change_password`` 正则匹配不完整的问题
- 修复 ``StuLib.set_telephone`` 正则匹配不完整的问题
- 添加部分单元测试
- 调整了包的结构