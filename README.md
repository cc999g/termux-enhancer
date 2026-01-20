# Termux Enhancer（中文说明）

Termux Enhancer 是一个用于在 Termux 环境中增强和管理系统配置的小脚本集合（当前仓库仅包含单个脚本 termux-enhancer.sh）。

主要功能
- 检查并修正脚本内的仓库链接（check_links）。当脚本内的 repo 链接不一致时，会尝试自动替换为 https://github.com/cc999g/termux-enhancer。
- “最快镜像”自动配置功能（auto_configure_fastest_mirror）：脚本中提供了自动检测并替换软件源为最快镜像的占位逻辑，但默认关闭，需显式启用才能执行真实配置。
- 提供环境变量和命令行参数来开启/关闭镜像自动配置，便于无交互和自动化使用。

默认行为
- 为了安全和可控，镜像自动配置默认被禁用（ENABLE_FASTEST_MIRROR=false）。脚本启动时不会修改系统/包管理器的源��除非显式启用。

如何部署（在 Termux 中）
1. 将脚本复制到主目录（或其他你喜欢的目录）：
   cp termux-enhancer.sh ~/termux-enhancer.sh
2. 赋予可执行权限：
   chmod +x ~/termux-enhancer.sh
3. 运行脚本以验证：
   ~/termux-enhancer.sh

脚本的配置方式
- 环境变量（推荐用于自动化）
  - ENABLE_FASTEST_MIRROR：
    - 默认：false
    - 设置为 true 将启用自动配置最快镜像（前提是脚本实现了相应逻辑）。
  - 使用示例：
    ENABLE_FASTEST_MIRROR=true ~/termux-enhancer.sh

- 命令行参数（临时覆盖环境变量）：
  - --enable-fastest-mirror    启用镜像自动配置
  - --disable-fastest-mirror   禁用镜像自动配置（默认）
  - -h, --help                 显示帮助信息
  - 示例：
    ~/termux-enhancer.sh --enable-fastest-mirror

- 在脚本内部修改（高级用户）：
  - 直接编辑脚本顶部的变量（例如修改 ENABLE_FASTEST_MIRROR 默认值或更改 repo_url）。
  - 注意备份原始脚本以便回退。

脚本启动方式
- 交互/手动启动：
  - 直接运行： ./termux-enhancer.sh
  - 使用 env 覆盖： ENABLE_FASTEST_MIRROR=true ./termux-enhancer.sh
  - 后台运行并记录日志（示例）： nohup ./termux-enhancer.sh > ~/termux-enhancer.log 2>&1 &

- 定时启动（可选）：
  - Termux 不自带系统 crond；若需定时执行可以安装 cronie 或使用 Android 的定时工具/Tasker。

自启动（开机/开应用自启）
推荐使用 Termux:Boot 或第三方自动化工具：

1) 使用 Termux:Boot（推荐，最简单）
- 安装 Termux:Boot 应用（从 F-Droid 或 Play 商店）。
- 在 Termux 中创建自启动目录（若不存在）：
  mkdir -p ~/.termux/boot
- 将脚本复制或创建一个简短的启动脚本到该目录，例如 ~/.termux/boot/start-termux-enhancer.sh：
  #!/data/data/com.termux/files/usr/bin/sh
  # 可在这里设置必要的环境变量
  export ENABLE_FASTEST_MIRROR=false
  exec /data/data/com.termux/files/home/termux-enhancer.sh >> /data/data/com.termux/files/home/termux-enhancer-boot.log 2>&1
- 赋予可执行权限并重启设备/重启 Termux:Boot 服务。
- 注意：Termux:Boot 脚本在启动时可能不会像交互 shell 一样加载用户���环境（~/.profile），因此需要在启动脚本中显式导出所需的环境变量。

2) 使用 Termux:Widget（手动一键启动）
- 将启动脚本放入 ~/shortcuts 或 ~/.shortcuts（依据你安装的 widget 要求），然后通过桌面小部件手动运行。

3) 使用 Android 自动化工具（如 Tasker）
- 使用 Tasker 在引导完成后运行相应的 shell 命令： su -c 'am start -n com.termux/.HomeActivity' 或直接运行脚本（视权限而定）。

重要注意事项与建议
- 危险提示：自动修改包管理器源会带来风险（下载被劫持、依赖混乱等）。脚本目前把自动配置设为默认关闭，确保在实现任何源替换逻辑时先做完整的备份与可恢复机制（例如备份 sources.list 或相应文件）。
- 备份建议：在修改任何源之前，备份相关文件到安全位置，例如：
  cp /data/data/com.termux/files/usr/etc/apt/sources.list /data/data/com.termux/files/home/sources.list.backup
- 日志：自启动时将输出重定向到日志文件，便于排查故障。

如何贡献或扩展
- 若你希望我把“最快镜像检测与替换”功能实现成可用代码，请说明：
  - 目标如何检测镜像（ping/下载测速/HTTP head），
  - 需要支持的源类型（Termux apt 源、第三方源等），
  - 是否需要交互确认或自动替换。
- 我可以基于上述需求把检测、排序、备份与替换流程实现并提交示例代码。

英文说明请查看：README_EN.md
