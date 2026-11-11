# BuddyKit Windows build harness

Windows 打包跑道。用公开仓库的 Windows runner（不消耗计费分钟、无额度上限）
构建安装包，构建输入不是明文源码。

- `.github/release-inputs/BuddyKit-*.tar.enc`：PyNaCl SecretBox 加密的源码归档，
  明文 sha256 与预期提交号写在工作流里，解密后逐项校验；
- 密钥只存在于 Actions secret `BUDDYKIT_BUILD_KEY`，不在此仓库；
- 解密、构建、验证都在 runner 临时目录完成，环境销毁即消失；
- 上传的安装包同样先加密（`BuddyKit-3.0.0-Setup.exe.enc` + `sealed.json`），
  下载后本地用同一密钥解封并核对 sha256。

工作流：`.github/workflows/package-windows.yml`（`workflow_dispatch`，需传 40 位提交号）。
门禁与私有仓库的同名工作流一致：分析、全量测试、PS5.1/MSVC 契约、构建、运行时装配、
no-uia、单实例、Inno Setup、静默安装冒烟，任一未过则禁止上传。
