## 前言

vapor 是纯swift实现的web开发框架和服务。

## 安装

- 检查本地环境，Swift版本和Xcode版本和Vapor的版本是否适配。

  ``` shell
  eval "$(curl -sL check.vapor.sh)"
  ```

  执行上面的命令, 如果遇到下面的错误，很可能是因为你安装了多个版本的xcode，它希望使用最新的版本。

  ``` shell
  Error: Your Xcode (9.3) is too outdated.
  Please update to Xcode 10.1 (or delete it).
  Xcode can be updated from the App Store.
  ```

  此时需要在xcode的偏好设置的Locations里，将Command Line Tools修改为最新的xcode版本。
  ![屏幕快照 2018-11-27 上午11.40.25](https://ws4.sinaimg.cn/large/006tNbRwgy1fxmgyyc0q4j30m80f8tar.jpg)

  或者使用下面的命令选择最新的xcode：

  ```shell
sudo xcode-select --switch /Applications/Xcode.app
  ```



- 使用mac下的homebrew安装vapor，首先确保安装了homebrew。再执行👇的命令安装, 另附[homebrew安装地址](https://brew.sh)

  ``` shell
  brew install vapor/tap/vapor
  ```



## Hello World

- 创建初始工程HelloVapor, 后面的branch参数应该是版本控制相关的命令。

  ``` shell
  vapor new HelloVapor --branch=beta
  ```

- 进行目录中，创建相应的xcode工程。 -y 是帮你输入创建过程中的 “open Xcode Project”询问的回答，是否创建完成并打开。

  ``` shell
  vapor xcode -y
  ```

- 运行项目，选择xcode中的tartget->Run 到设备 My Mac, 运行项目即可。

- 在浏览器中访问localhost:8080/hello 即可访问到我们的服务器反馈。