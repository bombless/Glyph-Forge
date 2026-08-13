# Glyph Forge

构建时生成字体位图（Building font bitmap at build-time）。

> 这个仓库旨在在构建阶段把向量字体或字形渲染成位图（bitmap glyphs），并把结果打包进 Rust 程序，以便在嵌入式系统、游戏或需要确定性字体位图的场景中使用。


## 状态

当前仓库使用 Rust 开发。请在发布到 crates.io 或 docs.rs 之前确保 Cargo.toml 中的信息（crate 名称、版本、作者、许可证等）已正确填写。


## 快速开始

下面给出如何把本库添加到项目、以及在构建阶段生成字体位图的常见做法。具体 API 以 crate 的导出为准，下面的代码为示例模版，请根据实际导出的类型和函数名调整。

1. 在 Cargo.toml 中添加依赖（发布到 crates.io 后使用真实版本号）：

```toml
[dependencies]
# 发布到 crates.io 之后替换为 crates 的实际名称与版本
glyph-forge = "0.1"
```

2. 在构建脚本（build.rs）中生成位图资源（示例，需根据实际 API 修改）：

```rust
// build.rs
fn main() {
    // 示例流程（伪代码）：
    // 1. 读取字体文件，例如：assets/SomeFont.ttf
    // 2. 使用 glyph-forge 提供的 API 将所需字符或字形渲染为位图
    // 3. 将生成的位图写入 OUT_DIR 下的静态文件，供主 crate 使用 include_bytes! 或 include_str! 引入

    // 伪代码：
    // let font = glyph_forge::Font::from_path("assets/SomeFont.ttf").unwrap();
    // let bmp = glyph_forge::render_glyphs(&font, "ABCDEFGHIJKLMNOPQRSTUVWXYZ").unwrap();
    // let out_dir = std::env::var("OUT_DIR").unwrap();
    // std::fs::write(std::path::Path::new(&out_dir).join("glyphs.bin"), &bmp).unwrap();
}
```

3. 在代码中包含生成的资源：

```rust
// main.rs 或 lib.rs
// 静态包含在 build.rs 中写入 OUT_DIR 的文件
// let glyphs: &[u8] = include_bytes!(concat!(env!("OUT_DIR"), "/glyphs.bin"));

// 然后按 crate 提供的格式解析并使用
// let font_atlas = glyph_forge::Atlas::from_bytes(glyphs).unwrap();
```


## 功能与设计目标

- 在构建时把字体/字形渲染为位图，避免运行时依赖字体渲染库。
- 支持把渲染结果序列化为紧凑的二进制资源或 Rust 源代码（例如生成一个 const 字节数组）以便直接嵌入到可执行文件中。
- 适用于嵌入式、无操作系统环境或不希望在运行时加载字体文件的场景。


## 示例

仓库中应包含一个 `examples/` 目录，示例包括：

- build-time 生成并包含字形位图的完整最小项目
- 使用不同字体、不同像素大小和不同字集（ASCII、Unicode 子集）的示例

如果尚未添加，请考虑添加 `examples/simple_build`，并在 README 中链接到该示例。


## 文档

发布到 crates.io 并生成文档后，推荐链接到 docs.rs：

- 文档（发布后）：https://docs.rs/<crate-name>

在 README 中包含 API 使用示例，并把详细说明放在 crate 文档注释和 docs.rs 中。


## 发布到 crates.io

发布前检查：

- Cargo.toml 中的 `name`, `version`, `authors`, `license`/`license-file`, `description`, `homepage`, `repository`, `documentation`, `keywords`, `categories` 等字段是否完整。
- 包含 `README.md`（本文件）、`LICENSE` 文件、以及必要的源文件。

常用步骤：

```bash
# 登录 crates.io（只需第一次）
cargo login <YOUR_API_TOKEN>

# 检查打包内容
cargo package --list

# 发布（确保版本号已更新）
cargo publish
```

注意：发布后版本不可修改，若发布失败请根据 cargo 的错误提示修正后重试。


## 测试

- 使用 `cargo test` 运行单元测试。
- 建议在 CI（如 GitHub Actions）中添加构建与测试的流程，确保 build.rs 在 CI 中也能正常运行（如果依赖本地字体文件，请在 CI 上准备或下载测试字体）。


## 常见问题与调试

- 字体文件路径：在 build.rs 中读取字体文件时，使用相对路径可能在不同环境下有差异，建议把字体放到仓库的 `assets/` 下，并在 build.rs 中使用相对于 crate 根目录的路径或通过环境变量传入。
- 构建缓存：当 build.rs 的输入（字体、配置）变化时，应确保构建脚本触发重新构建（利用 cargo:rerun-if-changed= 机制）。


## 贡献

欢迎贡献：

- 提交 issue 报告 bug 或提出功能请求。
- 通过 Pull Request 提交补丁或改进。

请在贡献前阅读并补充 CONTRIBUTING.md（如有必要），并在 PR 中包含可复现的测试或示例。


## 许可证

请在仓库根目录添加 LICENSE 文件并在此处说明许可证类型，例如 MIT 或 Apache-2.0。若尚未确定许可证，请尽快补充以便用户知晓如何使用该库。


---

如果你希望我把 README 改成英文版、添加 badges（crates.io / docs.rs）、或基于仓库中的代码生成更详细的使用示例（例如直接从导出的 API 自动生成示例代码），告诉我，我可以继续：

- 自动扫描仓库以提取实际 public API 并生成可复制的示例（需要访问代码）。
- 或者我现在直接把这个 README 提交到仓库根目录。
