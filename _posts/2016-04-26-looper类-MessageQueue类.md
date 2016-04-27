---
layout: post
title: Android异步线程，Looper, MessageQueue
categories: [移动开发]
tags: [Looper, MessageQueue]
---
### Looper类

Looper类有一个静态成员变量mThreadLocal，该类提供线程局部存储（Thread Local Storage），也就是内部存储的变量的作用域是线程，内部使用了Thread.getCurrentThread作为key来获取变量。

调用Looper类的静态方法prepare()，可以为当前线程创建Looper对象，并且把Looper对象放到线程局部存储里，MessageQueue是Looper的成员，他们是聚合关系。

```
public static void prepare() {
    prepare(true);
}

private static void prepare(boolean quitAllowed) {
   if (sThreadLocal.get() != null) {
        throw new RuntimeException("Only one Looper may be created per thread");
    }
    sThreadLocal.set(new Looper(quitAllowed));
}
```

Looper的静态方法loop()，来使当前线程进入处理Message的循环。


```
    public static void loop() {
        final Looper me = myLooper();
        if (me == null) {
            throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
        }
        final MessageQueue queue = me.mQueue;

        // Make sure the identity of this thread is that of the local process,
        // and keep track of what that identity token actually is.
        Binder.clearCallingIdentity();
        final long ident = Binder.clearCallingIdentity();

        for (;;) {
            Message msg = queue.next(); // might block
            if (msg == null) {
                // No message indicates that the message queue is quitting.
                return;
            }

            // This must be in a local variable, in case a UI event sets the logger
            Printer logging = me.mLogging;
            if (logging != null) {
                logging.println(">>>>> Dispatching to " + msg.target + " " +
                        msg.callback + ": " + msg.what);
            }

            msg.target.dispatchMessage(msg);

            if (logging != null) {
                logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);
            }

            // Make sure that during the course of dispatching the
            // identity of the thread wasn't corrupted.
            final long newIdent = Binder.clearCallingIdentity();
            if (ident != newIdent) {
                Log.wtf(TAG, "Thread identity changed from 0x"
                        + Long.toHexString(ident) + " to 0x"
                        + Long.toHexString(newIdent) + " while dispatching to "
                        + msg.target.getClass().getName() + " "
                        + msg.callback + " what=" + msg.what);
            }

            msg.recycleUnchecked();
        }
    }

```

代码中在用死循环来处理MessageQueue中的Message，其中msg.target就是sendMessage的handler，所以target.dispatchMessage就是以下代码

```

public void dispatchMessage(Message msg) {
        if (msg.callback != null) {
            handleCallback(msg);
        } else {
            if (mCallback != null) {
                if (mCallback.handleMessage(msg)) {
                    return;
                }
            }
            handleMessage(msg);
        }
    }
    
```
调用了handleMessage，也就是我们平常所实现的handler的handleMessage方法。