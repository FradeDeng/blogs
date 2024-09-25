在使用 Go Modules 时，并不是必须显式设置 `GOPATH` 和 `GOROOT`，但了解它们的作用和默认值是有帮助的。

### GOROOT

- **定义**：`GOROOT` 是 Go 安装目录的路径，其中包含了标准库和工具链。
- **默认值**：如果你是通过官方安装包安装的 Go，那么 `GOROOT` 通常会被自动设置为安装目录，例如 `/usr/local/go`（Linux/MacOS）或 `C:\Go`（Windows）。
- **手动设置**：一般情况下不需要手动设置 `GOROOT`，除非你有多个 Go 版本安装在不同的目录下，需要切换使用。

### GOPATH

- **定义**：`GOPATH` 是 Go 工作区的路径，默认情况下，Go 会在这个目录下查找和存放第三方包和项目。
- **默认值**：从 Go 1.8 开始，如果没有显式设置 `GOPATH`，它会默认为用户主目录下的 `go` 目录，例如：
    - Linux/MacOS: `~/go`
    - Windows: `%USERPROFILE%\go`

- **Go Modules 与 GOPATH**：
    - **启用 Go Modules**：从 Go 1.11 开始，Go 引入了 Go Modules 来管理依赖。在启用了 Go Modules 的项目中，`GOPATH` 不再是必需的，因为 Go Modules 会将依赖下载到项目本地的 `vendor` 目录或全局缓存目录（通常在 `~/.cache/go-build` 或 `~/.go/pkg/mod` 中）。
    - **兼容模式**：如果你在 `GOPATH` 目录结构内使用 Go Modules，Go 会进入兼容模式，这意味着它会尝试模拟旧版 Go 的行为。为了避免这种情况，建议在 `GOPATH` 外面创建项目目录。

### 总结

- **GOROOT**：通常不需要手动设置，除非你有特殊需求。
- **GOPATH**：从 Go 1.11 开始，使用 Go Modules 时，`GOPATH` 不再是必需的。为了更好地利用 Go Modules 的功能，建议在 `GOPATH` 外面创建项目目录。

### 示例

假设你已经安装了 Go，并且没有显式设置 `GOPATH` 和 `GOROOT`，你可以按照以下步骤创建和管理一个使用 Go Modules 的项目：

1. **创建项目目录**：
   ```sh
   mkdir -p ~/projects/my-go-project
   cd ~/projects/my-go-project
   ```

2. **初始化 Go Module**：
   ```sh
   go mod init my-go-project
   ```

3. **编写代码并添加依赖**：
   ```sh
   # 编写代码
   echo 'package main\n\nimport "fmt"\n\nfunc main() { fmt.Println("Hello, World!") }' > main.go

   # 添加依赖
   go get github.com/some/package@v1.0.0
   ```

4. **构建和运行项目**：
   ```sh
   go build
   ./my-go-project
   ```

通过这种方式，你可以在不设置 `GOPATH` 和 `GOROOT` 的情况下，顺利地使用 Go Modules 管理项目依赖。