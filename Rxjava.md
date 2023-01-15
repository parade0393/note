1. 观察者模式

   1. 身边的一直在使用的观察者模式->控件的点击事件监听

      ```java
      //控件button是被观察者，匿名内部类OnClickListener是观察者，setOnclickListener是订阅，回调方法onClick是事件，只有订阅了才会响应监听事件
      button.setOnClickListener(new View.OnClickListener() {
          @Override
          public void onClick(View v) {
              
          }
      });
      ```

   2. Rxjava的观察者模式

      ```java
      //在Rxjava的默认规则中，事件的发起和消费都是在同一个线程，也就是可以同步观察，虽然Rxjava就是为了异步观察(哈哈)，再详细一点就是在哪个线程调用subscribe就在哪个线程产生事件，在哪个线程产生事件就在哪个线程消费事件
      //Rxjava的事件分为3种 onNext,onError和onComplete
      //只有订阅了，被观察这才会发送事件(发起网络请求)->通常是一个事件序列，若干个onNext，一个onComplete或一个onError
      Observable.just(1)//1.创建被观察者
              .subscribeOn(Schedulers.io())//指定在io线程产生事件
              .observeOn(AndroidSchedulers.mainThread())//在主线程消费事件
              .subscribe(new Observer<Integer>() {//2.subscribe订阅 3.new Observer观察者
                  @Override
                  public void onSubscribe(@NonNull Disposable d) {
                  }
                  @Override
                  public void onNext(@NonNull Integer integer) {
                      //4.事件,相当于(onClick)
                  }
                  @Override
                  public void onError(@NonNull Throwable e) {
                      //4.事件
                  }
                  @Override
                  public void onComplete() {
                      //4.事件
      ```

      