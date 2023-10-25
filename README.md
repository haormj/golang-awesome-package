# golang-awesome-package

## 概述

本文档主要是记录个人在编写golang程序过程中遇到的一些很好用的库

## 列表

- golang.org/x/sync/errgroup
- github.com/shirou/gopsutil 获取系统和进程一些资源使用情况
- https://github.com/google/go-pipeline

## 使用示例

### errgroup

```go
package main

import (
	"context"
	"fmt"
	"os"
	"os/signal"
	"syscall"

	"golang.org/x/sync/errgroup"
)

func main() {
	if err := run(); err != nil {
		fmt.Println(err)
		return
	}
}

func run() error {
	ctx, cancel := context.WithCancel(context.Background())
	g, ctx := errgroup.WithContext(ctx)

	ch := make(chan os.Signal, 1)
	defer close(ch)
	signal.Notify(ch, syscall.SIGTERM, syscall.SIGINT, syscall.SIGQUIT)

	g.Go(func() error {
		// start1
		return nil
	})

	g.Go(func() error {
		// start2
		return nil
	})

	g.Go(func() error {
		select {
		case <-ctx.Done():
		case s := <-ch:
			fmt.Println("receive system signal", "signal", s)
			// 主动结束并等待所有goroutine正常退出
			cancel()
		}
		return nil
	})

	return g.Wait()
}
```
