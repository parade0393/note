在项目中使用rxjava遇到需要合并请求的时候，很多时候会需要使用有关delayError操作符，（concatDelayError，mergeDelayError）concatDelayError可以保证顺序。二者都可以保证观察者可以接收到所有的成功事件，即使某一个被观察者发送了error事件。正常情况下，如果没有使用切换线程操作符，自然支持这种效果。但是如果观察者和被观察者不在同一个线程，则需要使用observeOn的重载方法，如下

```java
public static void main(String[] args) {
        Observable<String> observable1 = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(@NonNull ObservableEmitter<String> emitter) throws Throwable {
                emitter.onError(new Throwable("出错了"));
                emitter.onComplete();
            }
        });

        Observable<String> observable2 = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(@NonNull ObservableEmitter<String> emitter) throws Throwable {
                emitter.onNext("a");
                emitter.onComplete();
            }
        });
        Observable<String> observable3 = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(@NonNull ObservableEmitter<String> emitter) throws Throwable {
                emitter.onNext("b");
                emitter.onComplete();
            }
        });

        List<Observable<String>> list = new ArrayList<>();
        list.add(observable1);
        list.add(observable2);
        list.add(observable3);
        Observable.concatDelayError(list)
                .subscribeOn(Schedulers.io())
//                .observeOn(Schedulers.io())//这样写不会达到预期的效果，遇到错误后还是会立即终止，不会延迟发送错误事件
                .observeOn(Schedulers.io(),true)//指定可以延迟发送事件，达到遇到错误不影响接收其他成功事件
                .subscribe(new Observer<String>() {
                    @Override
                    public void onSubscribe(@NonNull Disposable d) {

                    }

                    @Override
                    public void onNext(@NonNull String s) {
                        System.out.println("收到数据-------:"+s);
                    }

                    @Override
                    public void onError(@NonNull Throwable e) {
                        System.out.println("发生错误："+e.getMessage());
                    }

                    @Override
                    public void onComplete() {

                    }
                });

        while (true){

        }
    }
```

