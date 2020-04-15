# [Benchmark(性能测试)](/best_practice_benchmark.md)

rust官方提供宏`#\[bench]`进行性能测试，相比其它语言，ruby官方库benchmark或python官方库timeit，

rust的性能测试有点麻烦的，按[官方相关文档](https://doc.rust-lang.org/1.7.0/book/benchmark-tests.html)
的代码敲一遍，

运行`cargo bench`会报错如下

> `#![feature]` may not be used on the stable release channel

benchmark的代码只能用nightly的编译器进行编译

安装nightly版本的rust: `rustup install nightly`

运行benchmark代码：`cargo +nightly bench`

```rust
#![feature(test)] // 必须放在main.rs的第1-2行
extern crate test;

#[cfg(test)]
#[bench]
fn bench_add_1000_ints(bencher: &mut test::Bencher) {
  bencher.iter(|| {
    // fold类似于其它语言的reduce
    println!("reduce result = {}", (1..3).fold(0, |subtotal, element| subtotal + element));
  });
}
```

在rustc-1.4.4(nightly)上的运行结果是，无论多少计算量都显示`0 ns/iter (+/- 0)`

只能用打印时间戳的方式暂且记录运行时间了

```rust
fn benchmark_reduce() {
  use std::time::{SystemTime, UNIX_EPOCH};
  println!("{:?}", SystemTime::now().duration_since(UNIX_EPOCH).unwrap().as_nanos());
  (1..=1000).fold(0, |subtotal, element| subtotal + element);
  println!("{:?}", SystemTime::now().duration_since(UNIX_EPOCH).unwrap().as_nanos());
}
```

目前来看，Rust的benchmark是有问题的，官方至少还有两个benchmark的issue还是open的状态，先不继续研究了
