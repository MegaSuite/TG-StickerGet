# 使用官方的 Golang 镜像作为基础镜像
FROM golang:1.19 AS builder

# 设置工作目录
WORKDIR /app

# 复制当前目录下所有文件到工作目录
COPY . .

# 编译 Go 程序
RUN go build -o StickerDownloader

# 下载并安装 ffmpeg
RUN apt-get update && apt-get install -y wget xz-utils \
    && mkdir -p ./ffmpeg \
    && wget https://johnvansickle.com/ffmpeg/releases/ffmpeg-release-i686-static.tar.xz \
    && tar -xvf ffmpeg-release-i686-static.tar.xz \
    && mv ffmpeg-*-static/ffmpeg ./ffmpeg/ffmpeg \
    && rm -rf ffmpeg-*-static ffmpeg-release-i686-static.tar.xz

# 克隆并构建 lottie2gif
RUN apt-get install -y git cmake make \
    && git clone --recursive https://github.com/rroy233/lottie2gif.git \
    && cd lottie2gif \
    && mkdir build \
    && cd build \
    && cmake .. \
    && make \
    && cd .. \
    && mv output/lottie2gif ../lottie2gif/lottie2gif

# 使用一个较小的基础镜像来减少最终镜像的大小
FROM debian:bullseye-slim

# 安装运行时所需的依赖
RUN apt-get update && apt-get install -y ca-certificates procps

# 设置工作目录
WORKDIR /app

# 复制文件
COPY --from=builder /app /app

# 复制并提权后台脚本
COPY run.sh /app/
RUN chmod +x /app/run.sh

# 运行后台脚本
CMD ["bash","/app/run.sh"]
