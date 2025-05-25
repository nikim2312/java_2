Rx-ultra — Библиотека реактивных потоков
Rx-ultra — это Java-библиотека для работы с асинхронными потоками данных, реализующая модель Observable/Observer. Предназначена для обработки событий, трансформации данных в потоке и управления многопоточностью через Schedulers.

Основные компоненты
Observable<T>
Представляет поток данных. Создаётся через метод Observable.create(...), в который передаётся логика генерации данных. Поддерживает цепочку операторов:

map(Function<T, R>) — трансформация элементов потока;

filter(Predicate<T>) — фильтрация элементов по условию;

flatMap(Function<T, Observable<R>>) — преобразование одного элемента в другой поток и объединение результатов;

limit(int) — ограничение количества элементов в потоке.

Observer<T>
Интерфейс для получения данных из Observable. Содержит три метода:

onNext(T item) — вызывается при получении нового элемента;

onError(Throwable t) — вызывается при возникновении ошибки;

onComplete() — вызывается при завершении потока.

Disposable
Объект, возвращаемый методом subscribe(...). Позволяет отменить подписку и остановить получение данных.

Schedulers
Компоненты для управления многопоточностью.

Доступные реализации:

ComputationScheduler — пул потоков, соответствующий количеству ядер CPU. Используется для CPU-bound задач;

IOThreadScheduler — кэшированный пул потоков. Подходит для операций ввода-вывода;

SingleScheduler — последовательное выполнение в одном потоке. Подходит для задач, где требуется строгий порядок выполнения.

Методы:

subscribeOn(Scheduler) — определяет поток, на котором выполняется подписка;

observeOn(Scheduler) — определяет поток, в котором вызываются методы Observer.

Тестирование
Библиотека покрыта модульными тестами с использованием JUnit и Mockito. Проверяются следующие сценарии:

корректная отправка значений (onNext, onComplete, onError);

корректность работы операторов map, filter, limit, flatMap;

поведение при асинхронной подписке (subscribeOn, observeOn);

завершение подписки через dispose().

Примеры использования
Пример 1: Подписка на простой поток
<pre> '''java
Observable<Integer> observable = Observable.create(observer -> {
  observer.onNext(1);
  observer.onNext(2);
  observer.onNext(3);
  observer.onComplete();
});

observable.subscribe(new Observer<>() {
  public void onNext(Integer item) { System.out.println("Получено: " + item); }
  public void onError(Throwable t) { System.err.println("Ошибка: " + t.getMessage()); }
  public void onComplete() { System.out.println("Завершено"); }
});''' </pre>
Пример 2: Использование операторов
<pre> '''java
Observable.create(observer -> {
  for (int i = 1; i <= 10; i++) observer.onNext(i);
  observer.onComplete();
})
.filter(i -> i % 2 == 0)
.map(i -> "Число: " + i)
.limit(3)
.subscribe(new Observer<>() {
  public void onNext(Object item) { System.out.println(item); }
  public void onError(Throwable t) { t.printStackTrace(); }
  public void onComplete() { System.out.println("Поток завершен"); }
});''' </pre>
Пример 3: Асинхронная подписка
<pre> '''java
Observable<Integer> observable = Observable.create(observer -> {
  observer.onNext(42);
  observer.onComplete();
});

observable
  .subscribeOn(new IOThreadScheduler())
  .observeOn(new SingleScheduler())
  .subscribe(new Observer<>() {
    public void onNext(Integer item) { System.out.println("Получено: " + item); }
    public void onError(Throwable t) { t.printStackTrace(); }
    public void onComplete() { System.out.println("Поток завершен"); }
  });''' </pre>
