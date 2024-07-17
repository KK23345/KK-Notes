# Ruo-Yi框架分析

## 一、工程结构

- 后端

  ```
  com.ruoyi     
  ├── common            // 工具类
  │       └── annotation                    // 自定义注解
  │       └── config                        // 全局配置
  │       └── constant                      // 通用常量
  │       └── core                          // 核心控制
  │       └── enums                         // 通用枚举
  │       └── exception                     // 通用异常
  │       └── filter                        // 过滤器处理
  │       └── utils                         // 通用类处理
  ├── framework         // 框架核心
  │       └── aspectj                       // 注解实现
  │       └── config                        // 系统配置
  │       └── datasource                    // 数据权限
  │       └── interceptor                   // 拦截器
  │       └── manager                       // 异步处理
  │       └── security                      // 权限控制
  │       └── web                           // 前端控制
  ├── ruoyi-generator   // 代码生成（可移除）
  ├── ruoyi-quartz      // 定时任务（可移除）
  ├── ruoyi-system      // 系统代码
  ├── ruoyi-admin       // 后台服务
  ├── ruoyi-xxxxxx      // 其他模块
  ```

- 前端

  ```
  ├── build                      // 构建相关  
  ├── bin                        // 执行脚本
  ├── public                     // 公共文件
  │   ├── favicon.ico            // favicon图标
  │   └── index.html             // html模板
  │   └── robots.txt             // 反爬虫
  ├── src                        // 源代码
  │   ├── api                    // 所有请求
  │   ├── assets                 // 主题 字体等静态资源
  │   ├── components             // 全局公用组件
  │   ├── directive              // 全局指令
  │   ├── layout                 // 布局
  │   ├── plugins                // 通用方法
  │   ├── router                 // 路由
  │   ├── store                  // 全局 store管理
  │   ├── utils                  // 全局公用方法
  │   ├── views                  // view
  │   ├── App.vue                // 入口页面
  │   ├── main.js                // 入口 加载组件 初始化等
  │   ├── permission.js          // 权限管理
  │   └── settings.js            // 系统配置
  ```

## 二、用户认证&权限控制模块

