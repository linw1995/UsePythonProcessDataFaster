# 如何用 Python 快速处理数据

## 使用方式

本幻灯片需通过 [slides](https://github.com/maaslalani/slides) 打开，请根据文档安装该工具来使用，以获得更好的阅读体验。

### 代码块执行环境安装

```shell
python3.9 -m venv --without-pip --clear venv
source venv/bin/activate
pdm sync
```

### 播放幻灯片

```shell
# 安装 slides 命令
GO111MODULE=on go get -u github.com/maaslalani/slides/cmd
# 播放幻灯片
slides slides.md
```