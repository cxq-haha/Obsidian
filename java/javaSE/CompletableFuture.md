具有的能力：

-   将两个异步计算合并为一个，这两个异步计算之间相互独立，同时第二个又依赖于第一个的结果
-   等待Future集合种的所有任务都完成。
-   仅等待Future集合种最快结束的任务完成（有可能因为他们试图通过不同的方式计算同一个值），并返回它的结果。
-   通过编程方式完成一个Future任务的执行（即以手工设定异步操作结果的方式）。
-   应对Future的完成时间（即当Future的完成时间完成时会收到通知，并能使用Future的计算结果进行下一步的的操作，不只是简单地阻塞等待操作的结果）

## CompletableFutureAPI

### 创建CompletableFuture方式：

```java
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier);
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier, Executor executor);

public static CompletableFuture<Void> runAsync(Runnable runnable);
public static CompletableFuture<Void> runAsync(Runnable runnable, Executor executor);
```

### 获取线程运行结果：

```java
// 调用线程会阻塞，直至任务完成
public T    get()
public T    get(long timeout, TimeUnit unit)
// 当结果不存在或者调用时刻没有完成任务，给定一个确定的值
public T    getNow(T valueIfAbsent)
// join()与get()区别在于join()返回的结果或者抛出一个unchecked异常，而get()返回一个具体的异常
public T    join()
```

### 线程运行完成后对结果进行操作

如果处理异步线程结果后不需要返回任何内容，可以用whenXXX()方法
如果处理结果后需要返回数据给方法的调用者，可以使用handleXXX()方法

```java
public CompletableFuture<T>     whenComplete(BiConsumer<? super T,? super Throwable> action)
// Asyn表示开启一个异步线程处理任务的执行结果
public CompletableFuture<T>     whenCompleteAsync(BiConsumer<? super T,? super Throwable> action)
public CompletableFuture<T>     whenCompleteAsync(BiConsumer<? super T,? super Throwable> action, Executor executor)

public <U> CompletableFuture<U>     handle(BiFunction<? super T,Throwable,? extends U> fn)
public <U> CompletableFuture<U>     handleAsync(BiFunction<? super T,Throwable,? extends U> fn)
public <U> CompletableFuture<U>     handleAsync(BiFunction<? super T,Throwable,? extends U> fn, Executor executor)
```

传入的action为任务处理的返回结果和任务处理过程中产生的异常，可以这样使用：

```java
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {  
            try {  
                Thread.sleep(1000);  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            }  
            return "Thread_result";  
        });  
        
        // 处理future的任务结果
        CompletableFuture<String> handle = future.handle((result, error) -> {  
              
            return result + " P1";  
        });  
  
//        CompletableFuture<String> handle1 = handle.handle((result, error) -> {  
//            return result + " P2";  
//        });  
  
        System.out.println(handle.join());
        // 输出结果 “Thread_result P2”
```

如果不需要处理异常，只处理任务返回的结果，可以使用thenAcceptXXX()


```java
public CompletableFuture<Void>  thenAccept(Consumer<? super T> action)
public CompletableFuture<Void>  thenAcceptAsync(Consumer<? super T> action)
public CompletableFuture<Void>  thenAcceptAsync(Consumer<? super T> action, Executor executor)
```

如果只想对异常进行处理，可以使用exceptionally()

```java
public CompletableFuture<T> exceptionally(Function<Throwable, ? extends T> fn)
```

### 任务组合

将两个互相独立的任务组合

```java
CompletableFuture<List<String>> total = CompletableFuture.supplyAsync(() -> {
            // 第一个任务获取美术课需要带的东西，返回一个list
            List<String> stuff = new ArrayList<>();
            stuff.add("画笔");
            stuff.add("颜料");
            return stuff;
        }).thenCompose(list -> {
            // 向第二个任务传递参数list(上一个任务美术课所需的东西list)
            CompletableFuture<List<String>> insideFuture = CompletableFuture.supplyAsync(() -> {
                List<String> stuff = new ArrayList<>();
                // 第二个任务获取劳技课所需的工具
                stuff.add("剪刀");
                stuff.add("折纸");
                // 合并两个list，获取课程所需所有工具
                List<String> allStuff = Stream.of(list, stuff).flatMap(Collection::stream).collect(Collectors.toList());
                return allStuff;
            });
            return insideFuture;
        });
        System.out.println(total.join().size());
```

合并两个任务，并且合并两个任务的结果

```java
CompletableFuture<List<String>> painting = CompletableFuture.supplyAsync(() -> {
            // 第一个任务获取美术课需要带的东西，返回一个list
            List<String> stuff = new ArrayList<>();
            stuff.add("画笔");
            stuff.add("颜料");
            return stuff;
        });
        CompletableFuture<List<String>> handWork = CompletableFuture.supplyAsync(() -> {
            // 第二个任务获取劳技课需要带的东西，返回一个list
            List<String> stuff = new ArrayList<>();
            stuff.add("剪刀");
            stuff.add("折纸");
            return stuff;
        });
        CompletableFuture<List<String>> total = painting
                // 传入handWork列表，然后得到两个CompletableFuture的参数Stuff1和2
                .thenCombine(handWork, (stuff1, stuff2) -> {
                    // 合并成新的list
                    List<String> totalStuff = Stream.of(stuff1, stuff1)
                            .flatMap(Collection::stream)
                            .collect(Collectors.toList());
                    return totalStuff;
                });
        System.out.println(JSONObject.toJSONString(total.join()));
```

等待所有任务执行完成

```java
public static CompletableFuture<Void> allOf(CompletableFuture<?>... cfs)
```

等待任意一个（最快）的任务执行完成

```java
public static CompletableFuture<Void> allOf(CompletableFuture<?>... cfs)
```