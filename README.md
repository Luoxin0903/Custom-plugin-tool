自定义的插件工具


# ZZAgent助手 Chrome Extension 使用指南
插件界面：
<img width="429" alt="企业微信截图_1750771719244" src="https://github.com/user-attachments/assets/bd878e20-77d2-45df-b49f-9baaec09bbe3" />
调试页面：
<img width="964" alt="企业微信截图_17507757907619" src="https://github.com/user-attachments/assets/2f5c4207-a573-4282-becb-470c85891b28" />
图片自定义：
<img width="960" alt="企业微信截图_17508191234414" src="https://github.com/user-attachments/assets/54a3cd10-b467-4bcd-909f-143c8d844a19" />
<img width="960" alt="企业微信截图_17508180035601" src="https://github.com/user-attachments/assets/6b332fb4-d6c0-4f32-b838-65eaeaabf999" />
关于版本：
<img width="240" alt="企业微信截图_17508232468400" src="https://github.com/user-attachments/assets/7922f372-77f5-4f33-a7af-bc38c24f1817" />


## 项目结构

```
difyPlugin-main/
├── manifest.json          # 扩展配置文件
├── background.js          # 后台脚本（URL转换逻辑）
├── popup.html            # 弹出界面
├── config.js             # URL转换配置
├── icons/                # 图标文件夹
│   ├── icon16.png
│   ├── icon48.png
│   └── icon128.png
├── build.js              # 打包脚本
├── build.bat             # Windows快速构建脚本
├── package.json          # 项目配置
└── dist/                 # 输出文件夹
    └── dify-extension.zip
```

## 快速开始

### 1. 构建扩展包

**方法一：使用批处理文件（推荐）**
```bash
# 双击运行
build.bat
```

**方法二：使用命令行**
```bash
# 安装依赖
npm install

# 构建扩展包
npm run package
```

### 2. 安装到Chrome

1. 打开Chrome浏览器
2. 访问 `chrome://extensions/`
3. 开启"开发者模式"
4. 点击"加载已解压的扩展程序"
5. 选择项目文件夹（包含manifest.json的文件夹）

## 配置说明

### 修改URL转换规则

编辑 `config.js` 文件来自定义URL转换规则：

```javascript
const config = {
  // 目标协议
  targetProtocol: 'dify://',
  
  // URL匹配模式
  urlPatterns: [
    // 匹配所有HTTP/HTTPS链接
    {
      protocol: ['http:', 'https:'],
      hostname: /.*/, // 任何域名
      pathname: /.*/  // 任何路径
    },
    
    // 示例：只匹配特定域名
    // {
    //   protocol: ['https:'],
    //   hostname: /zzdify\.zhuanspirit\.com/,
    //   pathname: /^\/chat\/.*/
    // }
  ],
  
  // 自定义转换函数（可选）
  customTransform: null,
  
  // 默认转换函数
  defaultTransform: (url) => {
    return `${config.targetProtocol}${url.host}${url.pathname}${url.search}${url.hash}`;
  }
};
```

### 配置示例

**示例1：转换所有HTTP链接**
```javascript
urlPatterns: [
  {
    protocol: ['http:', 'https:'],
    hostname: /.*/,
    pathname: /.*/
  }
]
```

**示例2：只转换特定域名**
```javascript
urlPatterns: [
  {
    protocol: ['https:'],
    hostname: /zzdify\.zhuanspirit\.com/,
    pathname: /^\/chat\/.*/
  }
]
```

**示例3：自定义转换逻辑**
```javascript
customTransform: (url) => {
  if (url.hostname === 'zzdify.zhuanspirit.com') {
    const chatId = url.pathname.split('/').pop();
    return `dify://${chatId}`;
  }
  return null; // 使用默认转换
}
```

## 功能特性

1. **自动检测**：当访问HTTP/HTTPS链接时自动转换
2. **手动触发**：点击扩展图标手动转换当前页面
3. **灵活配置**：支持正则表达式匹配和自定义转换逻辑
4. **协议转换**：将http://或https://转换为dify://

## 转换示例

| 原始URL | 转换后URL |
|---------|-----------|
| `https://zzdify.zhuanspirit.com/chat/UolxvvkNaemD31DV` | `dify://zzdify.zhuanspirit.com/chat/UolxvvkNaemD31DV` |
| `http://example.com/path?param=value#fragment` | `dify://example.com/path?param=value#fragment` |
| `https://api.example.com/v1/data` | `dify://api.example.com/v1/data` |

## 开发说明

### 文件说明

- **manifest.json**: Chrome扩展的配置文件，定义权限、脚本等
- **background.js**: 后台脚本，处理URL转换逻辑
- **popup.html**: 扩展弹出界面
- **config.js**: 可配置的URL转换规则
- **build.js**: 自动打包脚本

### 修改后重新构建

1. 修改相关文件
2. 运行 `build.bat` 或 `npm run package`
3. 在Chrome扩展页面点击"重新加载"按钮

### 调试

1. 在Chrome扩展页面点击"检查视图" > "背景页"
2. 打开开发者工具查看控制台输出
3. 修改代码后需要重新加载扩展

## 注意事项

1. 确保Node.js已安装（用于构建）
2. 修改配置后需要重新构建扩展包
3. 安装扩展后需要重新加载才能生效
4. 某些网站可能会阻止协议重定向

## 故障排除

**问题：扩展不工作**
- 检查Chrome扩展页面是否已启用
- 查看背景页控制台是否有错误
- 确认配置文件语法正确

**问题：构建失败**
- 确认Node.js已正确安装
- 检查package.json文件是否完整
- 删除node_modules文件夹后重新安装

**问题：URL转换不正确**
- 检查config.js中的正则表达式
- 确认URL模式配置正确
- 查看背景页控制台输出 
