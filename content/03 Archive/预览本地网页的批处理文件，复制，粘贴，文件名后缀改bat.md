Obsidian + Quartz v4

写完笔记，在本地随便哪里，自己找着方便就行，我怼到桌面上了。

建一个文本文件，".txt"后缀的，新建文本文件.txt，把下边内容一粘贴，保存，然后把这个文本文件后缀名改成bat #bat文件

粘贴的内容如下：

`````
@echo off
chcp 65001 >nul
REM ==============================
REM Clean + One-click start Quartz local preview
REM ==============================

set QUARTZ_DIR=E:\quartz
set PORT=8080
set BROWSER_URL=http://localhost:%PORT%

echo =================================
echo Stopping any running Quartz services...
echo =================================

REM Kill node.exe using port 8080
for /f "tokens=5" %%a in ('netstat -aon ^| findstr :%PORT%') do (
    taskkill /PID %%a /F >nul 2>&1
)

REM Check if port is free
netstat -aon | findstr :%PORT% >nul
if %ERRORLEVEL%==0 (
    echo Port %PORT% is still in use. Please close conflicting programs.
    pause
    exit /b
)

echo Starting Quartz local preview server...

REM Start Quartz hidden in background
powershell -WindowStyle Hidden -Command "Start-Process cmd -ArgumentList '/c cd /d \"%QUARTZ_DIR%\" && npx quartz build --serve' -WindowStyle Hidden"

REM Wait 3 seconds for server to start
timeout /t 3 >nul

REM Open default browser to local preview
start "" "%BROWSER_URL%"

echo Quartz local preview started. Browser should open automatically.


::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
::                                                                          ::
::                          Quartz 本地预览 bat 功能说明                    ::
::                                                                          ::
:: 功能总结：                                                                ::
:: 1️⃣ 自动清理旧服务                                                        ::
::    - 检查指定端口（默认 8080）是否被占用                                  ::
::    - 如果有旧的 Node.js Quartz 服务在跑，会自动结束对应进程            ::
::    - 避免出现端口冲突和重复服务启动                                        ::
::                                                                          ::
:: 2️⃣ 启动本地 Quartz 服务                                                  ::
::    - 在指定的 Quartz 目录下启动 npx quartz build --serve                 ::
::    - 使用隐藏命令行窗口，后台运行服务                                     ::
::    - 确保启动干净、不会打开多个 CMD 窗口                                  ::
::                                                                          ::
:: 3️⃣ 自动打开浏览器                                                        ::
::    - 等待服务启动（默认 3 秒）                                           ::
::    - 自动打开默认浏览器访问本地网页（如 http://localhost:8080）          ::
::    - 无需手动输入地址                                                    ::
::                                                                          ::
:: 4️⃣ 实时显示最新笔记                                                      ::
::    - 指向固定的 content 文件夹（如 E:\quartz\content）                    ::
::    - 修改笔记后，Quartz 的热刷新功能可以在浏览器自动更新显示             ::
::    - 避免多 bat 文件启动导致的“显示旧内容”问题                            ::
::                                                                          ::
:: 5️⃣ 解决乱码问题                                                          ::
::    - CMD 设置 UTF-8 编码（chcp 65001）                                   ::
::    - 所有提示信息改为英文，保证在任何 Windows 系统都不会显示乱码        ::
::                                                                          ::
:: 6️⃣ 安全与干净                                                            ::
::    - 不依赖 Obsidian 插件                                               ::
::    - 不污染笔记目录                                                     ::
::    - 不上传到 GitHub                                                     ::
::    - 可以固定任务栏，一键点击即可启动                                     ::
::                                                                          ::
:: 使用体验：                                                                ::
::    - 双击 bat 文件或任务栏快捷方式                                      ::
::    - 后台启动 Quartz 服务                                                ::
::    - 自动打开浏览器显示本地预览                                          ::
::    - 可多次点击，无风险，无重复服务                                      ::
::    - 所有本地笔记修改即时在浏览器更新                                    ::
::                                                                          ::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


`````

