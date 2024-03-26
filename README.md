1. pnpm create vite my-vue-app 

2. Select a framework  ==> vue

3. Select a variant: => ts

4. 修改 .vscode/extensions.json

   ```
   {
     "recommendations": [
       "Vue.volar",
       "csstools.postcss",
       "dbaeumer.vscode-eslint",
       "streetsidesoftware.code-spell-checker"
     ]
   }
   ```

5.添加 settings.json

```
{
  "editor.tabSize": 4,
  "editor.formatOnSave": false,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.fixAll.stylelint": "explicit"
  },
  "stylelint.enable": true,
  "stylelint.validate": ["css", "less", "postcss", "scss", "vue", "sass"],
  "files.eol": "\n",
  "search.exclude": {
    "**/node_modules": true,
    "**/*.log": true,
    "**/*.log*": true,
    "**/bower_components": true,
    "**/dist": true,
    "**/elehukouben": true,
    "**/.git": true,
    "**/.gitignore": true,
    "**/.svn": true,
    "**/.DS_Store": true,
    "**/.idea": true,
    "**/.vscode": false,
    "**/yarn.lock": true,
    "**/tmp": true,
    "out": true,
    "dist": true,
    "node_modules": true,
    "CHANGELOG.md": true,
    "examples": true,
    "res": true,
    "screenshots": true,
    "yarn-error.log": true,
    "**/.yarn": true
  },
  "files.watcherExclude": {
    "**/.git/objects/**": true,
    "**/.git/subtree-cache/**": true,
    "**/.vscode/**": true,
    "**/node_modules/**": true,
    "**/tmp/**": true,
    "**/bower_components/**": true,
    "**/dist/**": true,
    "**/yarn.lock": true
  },
  "errorLens.enabledDiagnosticLevels": ["error", "warning"],
  "cSpell.words": [
    "zsdx",
    "browserslist",
    "qrcode",
    "pinia",
    "nprogress",
    "stylelint",
    "vitejs",
    "sortablejs",
    "commitlint",
    "echarts",
    "vueuse",
    "pnpm",
    "composables",
    "vite",
    "axios",
    "wangeditor",
    "typecheck"
  ]
}
```

6.pnpm install 安装依赖项

7.配置 package.json  端口 以及环境变量 scripts:配置如下

```
 "dev": "vite --port 8089 --host",
 "test":"vite --mode test --port 8089 --host",
 "pre":"vite --mode pre --port 8089 --host",
 "prod":"vite --mode prod --port 8089 --host",
 "build:dev": "rimraf dist && vite build --mode dev",
 "build:test": "rimraf dist && vite build --mode test",
 "build:pre": "rimraf dist && vite build --mode pre",
 "build:pro": "rimraf dist && vite build --mode pro",
 "preview": "vite preview",
```

项目根目录创建 env 文件夹

```
	├── env							// 环境变量文件夹
	│	  ├──.env						// 默认 dev
	│	  ├──.dev						// 开发环境
	│	  ├──.test					// 测试环境
	│	  ├──.pre						// 预发环境
	│	  └──.pro						// 生产环境
```

8. scripts 添加 clean 清除 vite缓存命令

   pnpm i -D  rimraf

```
    "clean:cache": "npx rimraf node_modules/.cache/ && npx rimraf node_modules/.vite"

```

9. 添加 postinstall 命令 用于管理 git 提交校验 已经 eslint 校验

   pnpm i -D  simple-git-hooks

   ```
   "postinstall": "npx simple-git-hooks"
   ```

   

   首先要配置 .npmrc 文件 

   ```
   shamefully-hoist=true
   strict-peer-dependencies=false
   registry=https://registry.npmmirror.com/
   @zsdx:registry=https://developer-center.wxhand.com/verdaccio/
   ```

    pnpm i  @zsdx/eslint-config -D

   这是会报错：需要执行 pnpm install 重置一下

   

   配置 eslint 与 commitlint 

   ```
   pnpm i  @zsdx/eslint-config   eslint@8.56.0 lint-staged@15.0.2  stylelint@15.10.0  stylelint-config-html@1.1.0  stylelint-config-prettier@9.0.5  stylelint-config-recommended-vue@1.5.0 stylelint-config-standard@29.0.0  stylelint-order@6.0.4 prettier @commitlint/cli @commitlint/config-conventional -D
   ```

   因为 stylelint 管理比较混乱 ，最好是锁版本 

   package.json 添加配置：

   ```
   "simple-git-hooks": {
       "pre-commit": "npx lint-staged",
       "commit-msg": "npx --no-install commitlint --edit $1"
     },
    "lint-staged": {
       "*.{js,ts,jsx,tsx}": [
         "eslint --fix"
       ],
       "*.vue": [
         "eslint --fix",
         "stylelint --fix"
       ],
       "*.{less,scss,css}": [
         "stylelint --fix",
         "prettier --write"
       ]
     },
     "eslintConfig": {
       "extends": "@zsdx" 
     }
   ```

如果没有 git 初始化 则会提示 ：

![image-20240326155638661](/Users/shisanduoniandeyedie/Library/Application Support/typora-user-images/image-20240326155638661.png)

这是需要在本地执行 

```
git init
```

执行后  pnpm  postinstall结果：

<img src="/Users/shisanduoniandeyedie/Library/Application Support/typora-user-images/image-20240326160000233.png" alt="image-20240326160000233" style="zoom:200%;" />

接下来在根目录添加 .stylelintignore  .stylelintrc.cjs  committlint.config.cjs 文件

.stylelintignore

```
/dist/*
/public/*
public/*
/node_modules/*
/dist/*
.min.css
```

.stylelintrc.cjs

```
module.exports = {
    root: true,
    defaultSeverity: 'error',
    extends: ['stylelint-config-standard', 'stylelint-config-prettier'],
    plugins: ['stylelint-order'],
    overrides: [
        {
            files: ['*.vue', '**/*.vue', '*.html', '**/*.html', '**/*.less'],
            extends: ['stylelint-config-html'],
            rules: {
                // 指定关键帧名称的模式
                'keyframes-name-pattern': null,
                // 禁止未知的伪类选择器
                'selector-pseudo-class-no-unknown': [
                    true,
                    {
                        ignorePseudoClasses: ['deep', 'global']
                    }
                ],
                // 禁止未知的伪元素选择器
                'selector-pseudo-element-no-unknown': [
                    true,
                    {
                        ignorePseudoElements: ['v-deep', 'v-global', 'v-slotted']
                    }
                ]
            }
        },
        {
            files: ['*.less', '**/*.less'],
            customSyntax: 'postcss-less',
            extends: [
                'stylelint-config-standard',
                'stylelint-config-recommended-vue',
                'stylelint-config-prettier'
            ]
        }
    ],
    rules: {
        // 不允许未知函数
        'function-no-unknown': null,
        // 指定类选择器的模式
        'selector-class-pattern': null,
        // 禁止空源码
        'no-empty-source': null,
        // 指定字符串使用单引号
        'string-quotes': 'single',
        // 禁止未知的@规则
        'at-rule-no-unknown': [
            true,
            {
                ignoreAtRules: [
                    'tailwind',
                    'apply',
                    'variants',
                    'responsive',
                    'screen',
                    'function',
                    'if',
                    'each',
                    'include',
                    'mixin'
                ]
            }
        ],
        // 指定@规则名的大小写
        'at-rule-name-case': 'lower',
        // 指定缩进
        indentation: [
            4,
            {
                severity: 'warning'
            }
        ],
        // 禁止未知的伪类选择器
        'selector-pseudo-class-no-unknown': [
            true,
            {
                ignorePseudoClasses: ['global']
            }
        ],
        // 禁止未知的伪元素选择器
        'selector-pseudo-element-no-unknown': [
            true,
            {
                ignorePseudoElements: ['v-deep']
            }
        ],
        'order/properties-order': [
            // Positioning 布局方式
            'position',
            'top',
            'right',
            'bottom',
            'left',
            'z-index',
            'display',
            'justify-content',
            'align-items',
            'float',
            'clear',
            'overflow',
            'overflow-x',
            'overflow-y',
            // Box Model 尺寸
            'width',
            'min-width',
            'max-width',
            'height',
            'min-height',
            'max-height',
            'margin',
            'margin-top',
            'margin-right',
            'margin-bottom',
            'margin-left',
            'padding',
            'padding-top',
            'padding-right',
            'padding-bottom',
            'padding-left',
            'border',
            'border-style',
            'border-width',
            'border-color',
            'border-top',
            'border-top-style',
            'border-top-width',
            'border-top-color',
            'border-right',
            'border-right-style',
            'border-right-width',
            'border-right-color',
            'border-bottom',
            'border-bottom-style',
            'border-bottom-width',
            'border-bottom-color',
            'border-left',
            'border-left-style',
            'border-left-width',
            'border-left-color',
            'border-radius',
            // Typographic（文本相关）
            'text-align',
            'text-justify',
            'text-indent',
            'text-overflow',
            'text-decoration',
            'white-space',
            'color',
            'font-size',
            'font-family',
            'font-weight',
            // Visual（视觉效果）
            'background',
            'background-position',
            'background-repeat',
            'background-size',
            'background-color',
            'background-clip',
            'opacity',
            'filter',
            'list-style',
            'outline',
            'visibility',
            'box-shadow',
            'text-shadow',
            'resize',
            'transition'
        ]
    }
};

```

commitlint.config.cjs

```
module.exports = {
    extends: ['@commitlint/config-conventional'],
    rules: {
        'type-enum': [
            2,
            'always',
            [
                'feat', // 新功能(feature)
                'fix', // 修补bug
                'docs', // 文档(documentation)
                'style', // 格式、样式(不影响代码运行的变动)
                'refactor', // 重构(即不是新增功能，也不是修改BUG的代码)
                'perf', // 优化相关，比如提升性能、体验
                'test', // 添加测试
                'ci', // 持续集成修改
                'build', // 影响构建系统或外部依赖的更改
                'chore', // 构建过程或辅助工具的变动
                'revert', // 回滚到上一个版本
                'workflow', // 工作流改进
                'mod', // 不确定分类的修改
                'wip', // 开发中
                'types', // 类型修改
                'release' // 版本发布
            ]
        ]
    }
};

```

这时 git 提交检查以及 eslint 校验都已经成功，可以提交代码到仓库了

如果提交格式不对则报错：

![image-20240326180327961](/Users/shisanduoniandeyedie/Library/Application Support/typora-user-images/image-20240326180327961.png)



10.添加 ts 检查命令

pnpm i -D  vue-tsc

```
"typecheck": "vue-tsc --noEmit"
```

