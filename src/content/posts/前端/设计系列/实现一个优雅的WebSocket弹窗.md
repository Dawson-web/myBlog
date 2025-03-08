---
title: 实现一个优雅的WebSocket弹窗
tags: [猜想设计]
category: 猜想设计
published: 2024-10-22 23:23:25
draft: false 
image: /前端/设计系列/实现一个优雅的WebSocket弹窗/cover.webp

---

---
## 前言

最近再实习的过程中，碰到一个需要通过WebSocket连接服务器，并进行消息推送的需求，虽然leader说此项目当前不需要，实现弹窗效果，只需要直接展示出来即可，但我仍然想尝试实现一个优雅的WebSocket弹窗，于是下去研究了一下。

## 实现思路

### WebSocket配置

首先，我们需要连接WebSocket服务器，并监听消息。

```tsx
export function contectWebSocket() {
  const url = new URL("ws://localhost:4000/ws");
  url.searchParams.append("token", localStorage.getItem("token") || "");
  const socket = new WebSocket(url);
  return { socket, url };
}
```
上述我们已经简单的实现了WebSocket的连接，并获取到WebSocket对象，大家可以根据自己的需求进行修改。
不过相信大家也已经注意到了上述的`token`，这是为了实现WebSocket的鉴权，以及建立连接的唯一标识。主要用于服务端向特定用户推送消息

然后，我们监听WebSocket的`open`、`message`、`error`、`close`事件，并做相应的处理。

```tsx
let lockReconnect = false;

export async function createWebSocket(
  setMessage: React.Dispatch<React.SetStateAction<IWSMessage>>
) {
  let timer: null | NodeJS.Timer = null;
  let { socket, url } = contectWebSocket();
  socket.onopen = () => {
    setInterval(() => {
      socket.send(
        JSON.stringify({
          type: "message",
          token: localStorage.getItem("token") || "",
        })
      );
    }, 2000);
  };

  socket.onmessage = function (event) {
    try {
      const message = JSON.parse(event.data);
      setMessage(message);
      console.log("Received message:", message);
    } catch (e) {
      console.log("Received non-JSON data:", e);
    }
  };

  // 监听关闭事件
  socket.onclose = () => {
    console.log("WebSocket connection closed");
    websocketReconnect();
  };

  // 监听错误事件
  socket.onerror = (error) => {
    console.error("WebSocket error observed:", error);
    websocketReconnect();
  };

  function websocketReconnect() {
    if (lockReconnect) {
      // 是否已经执行重连
      return;
    }
    console.log("尝试重连...");
    // 没连接上会一直重连，设置延迟避免请求过多
    if (!timer) {
      timer = setInterval(function () {
        console.log("1.尝试重连...");
        socket = new WebSocket(url);

        socket.onopen = function () {
          console.log("连接成功");
          lockReconnect = true;
          clearInterval(timer);
          timer = null;
          socket.send(JSON.stringify({ type: "subscribe", topic: "news" }));
        };

        socket.onclose = function () {
          console.log("连接关闭，准备重连");
          lockReconnect = false;
          websocketReconnect();
        };

        socket.onerror = function (error) {
          console.error("连接出错:", error);
          lockReconnect = false;
        };
      }, 3000);
    }
  }

  return socket;
}
```
为了避免连接中断或者别的特殊情况，我们需要实现一个自动重连的功能，这里我们使用了`setInterval`定时发送心跳包，如果连接中断，则自动重连。

### 弹窗组件

接下来，我们需要实现一个弹窗组件，用于展示WebSocket推送的消息。

```tsx

import { IWSMessage } from "@/types/websocket";
import clsx from "clsx";
import { MessageSquareMoreIcon } from "lucide-react";
import { FC, useMemo, useState } from "react";

let time: NodeJS.Timeout | null = null;

function Content(message: IWSMessage | undefined) {
  // TODO: 根据消息类型返回不同的内容
  switch (message?.type) {
    case "text":
      return (
        <div className="flex items-end gap-2">
          <MessageSquareMoreIcon className="w-5 h-5" />
          现在的时间是 {message.content}
        </div>
      );
  }
}

const ScoketMessage: FC<{
  message: IWSMessage | undefined;
  className?: string;
}> = ({ message, className }) => {
  const [show, setShow] = useState(false);
  const date = new Date();
  const currentTime = `${date.getHours()}:${date.getMinutes()}`;
  useMemo(() => {
    setShow(true);
    if (!time) {
      time = setTimeout(() => {
        setShow(false);
      }, 3000);
    } else {
      clearTimeout(time);
      time = setTimeout(() => {
        setShow(false);
      }, 3000);
    }
  }, [message]);
  return (
    <div
      className={clsx(
        "flex items-center justify-around gap-2 w-auto min-w-[300px] bg-theme_gray rounded-md shadow-xl z-50 overflow-hidden opacity-60 backdrop-blur-sm font-medium ",
        className,
        {
          "transition-all duration-500  h-10 ease-in-out p-4": show,
          "transition-all duration-500  h-0 ease-in-out p-0": !show,
        }
      )}
    >
      {Content(message)}
      <p className="opacity-80">{currentTime}</p>
    </div>
  );
};
export default ScoketMessage;
```

整个` ScoketMessage `的核心逻辑基本可以归纳为

``` tsx
  useMemo(() => {
    setShow(true);
    if (!time) {
      time = setTimeout(() => {
        setShow(false);
      }, 3000);
    } else {
      clearTimeout(time);
      time = setTimeout(() => {
        setShow(false);
      }, 3000);
    }
  }, [message]);

```
当`message`发生变化时，会触发缓存，更新` time `计时器状态，以此更新` show ` state的状态。
通过show来控制弹窗显示和隐藏的样式渲染。

不知道大家是否注意到我是将` ScoketMessage `单独封装成组件，内部并无创建WebSocket连接，而是只是单纯的作为一个显示组件
其实，或许本该就封装成一个组件的，且因该创建WebSocket连接，但最开始也就出现问题在此处，如若我将WebSocket放在组件内部时
请注意`message`发生变化时会触发组件重渲染造成触发`UseEffEct`建立多次连接
或许大家看过React官方文档的会知道我们可以知道,在组件外声明` let isInited = false; `然后
```tsx
  useEffect(() => {
    if (!isInited) {
      createWebSocket(setMessage);
      isInited = true;
    }
  }, []);
```
可以避免组件重渲染时多次建立WebSocket连接，但问题在于，我们仍希望尽量保持组件的纯粹，避免过多的副作用，因此，我们选择将WebSocket连接放在组件外部，并在组件内部使用`useEffect`监听`message`的变化，从而更新弹窗的显示和隐藏状态。（不过，因人而异吧，此处因为组件我是放在` Layout `布局中，其作为一个转接本生就作为一个高集成的组件，与其放于` ScoketMessage `不如直接放在` Layout `）

### 思考

前段时间看了看React官网的Hooks介绍，其中讲到` useMemo `是一个昂贵的开销，在很多时候遇见一个state的更新会造成另一个state的更新，此时` useMemo `无疑是很方便的
但考虑到开销原因又很犹豫正如此处的,如若不使用` useMemo `则需要使用` useEffect `副作用，我之前考虑过` useCallback `但返回的是一个函数，意味着我并不能直接执行，且实际上和` useMemo `并无二置，因此，此处我被迫选择了` useMemo `，但不知道大家是否有更好的办法呢？
```tsx
  useMemo(() => {
    setShow(true);
    if (!time) {
      time = setTimeout(() => {
        setShow(false);
      }, 3000);
    } else {
      clearTimeout(time);
      time = setTimeout(() => {
        setShow(false);
      }, 3000);
    }
  }, [message]);

```
> 考虑到Layout组件因人而异，如果大家需要使用的话只需加入三处代码在你代码的合适位置即可
 ```tsx
   const [message, setMessage] = useState<IWSMessage>({
    type: "tetx",
    content: "欢迎来到 ISES",
  });
  // ——————————————————————————————————
    useEffect(() => {
    if (!isInited) {
      createWebSocket(setMessage);
      isInited = true;
    }
  }, []);
  // ——————————————————————————————————
  <ScoketMessage message={message} className="fixed sm:top-2 top-0 " />

```


---