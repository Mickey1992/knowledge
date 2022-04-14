## 搭建node + typescript开发环境

### 安装nodejs

1. 下载

   [Download | Node.js (nodejs.org)](https://nodejs.org/en/download/)

2. 运行javascript测试代码

   ```bash
   node test.js
   ```

3. 初始化工程

   ``` bash
   npm init
   ```

   - npm是什么
     - npm = Node Package Manage 
     - 是nodejs上的包管理工具
     - 安装nodejs的同时被安装
   - 初始化后本地文件夹中会出现`package.json`文件
     - 项目的一些配置信息
     - 在`package.json`中定义用到的包

### 安装typescript

1. 下载安装

   ```bash
   npm install typescript --save-dev
   ```

   - 安装完成后发生变化的文件及文件夹

     - `package.json`

       添加了typescript的dependency定义

     - `package-lock.json`

       `package.json`中定义的包的详细信息（哪里下的...），以确保不同的人根据`package.json`定义下到的包是完全一致的

     - `node_modules`文件夹

       存放项目种用到的包

       这些包本身不需要提交到git上，所以修改`.gitignore`中添加`node_modules`

2. 初始化typescript config

   ```bash
   npx tsc --init
   ```

   - 初始化后本地文件夹中会出现`tsconfig.json`文件

     - 里面定义了typescript编译时的一些设置

   - 更改编译的输出路径

     - 目的：和java一样，编译后生成的`.class`文件并不需要进行版本管理，因此可以单独设置一个文件夹，在`.gitignore`中添加忽略

       ```
       "outDir": "./dist", 
       ```

   - 编译typescript代码

     - 使用`tsconfig.json`中的配置进行编译

       ```bash
       npx tsc
       ```

     - 不使用`tsconfig.json`中的配置

       ```bash
       npx tsc test.ts
       ```

     - `npx`是什么

       `npm`提供的命令，自动下载指定的包并执行

   - 简化编译&运行手顺

     - 在`package.json`中的script中增加`start`脚本

       ```json
       "main": "./dist/test.js",
       "scripts": {
           "test": "echo \"Error: no test specified\" && exit 1",
           "start": "npx tsc && node dist/test.js"
         },
       ```

     - 编译&运行

       ```bash
       npm start
       ```

   ### 总结：各个文件夹和文件的用处

   - `package.json`
     - 项目的一些配置信息
     - 定义项目中用到的包
   - `package-lock.json`
     - `package.json`中定义的包的详细信息（哪里下的...），以确保不同的人根据`package.json`定义下到的包是完全一致的
   - `node_modules`
     - 根据`package.json`的定义下载下来的包
   - `tsconfig.json`
     - typescript编译时的一些设置





