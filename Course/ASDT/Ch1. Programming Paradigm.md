### 过程式编程

#### C++

```cpp
void solve(int n) {
    int sum = 0;
    for (int i = 1; i < n; i++) {
        if (!(i % 3) || !(i % 5)) {
            sum += i;
        }
    }
    cout << "Sum is " << sum << endl;
}

// Output : Sum is 233168
```

#### Swift

```swift
import Foundation

func FP(input : Int = 1000) -> Int {
    var sum = 0
    for i in 1..<input {
        if((i % 3 == 0) || (i % 5 == 0)) {
            sum = sum + i
        }
    }
    return sum
}

print(FP(input : 1000))

// Output : 233168
```

### 面向对象编程

#### C++

```c++
#include <iostream>
#include <vector>
using namespace std;
class Number {
public:
    int _num;
    explicit Number(int n) {
        _num = n;
    }
    int getNumber() const {
        return _num;
    }
    bool isMod(int x) const {
        return _num % x == 0;
    }
    bool operator<(Number x) const {
        return _num < x.getNumber();
    }
    void operator++(int) {
        this->_num++;
    }
};

class ACC {
public:
    int sum;
    ACC(int n) {
        sum = n;
    }
    int sumWithMod(Number start, Number end, vector<int> &modList) {
        for (Number i = start; i < end; i++) {
            bool tag = false;
            for (int remainder : modList) {
                tag = tag || i.isMod(remainder);
            }
            sum = tag ? sum + i.getNumber() : sum;
        }
        return sum;
    }
};

int main(int argc, char *argv[]) {
    ACC solution(0);
    vector<int> arr = {3, 5};
    cout << solution.sumWithMod(Number(1), Number(1000), arr);
    return 0;
}

// Output : 233168
```

1. 该方式主要体现了面向对象编程的主要特性之一「封装」，通过重新定义`Number`对象向其加入`isMod`操作
2. 封装的目的是为了代码重用，在`main()`函数调用`solution.sumWithMod()`时可以任意改变其中的区间范围。

### 函数式编程

#### Swift

```swift
import Foundation

func FP(input : Int = 1000) -> Int {
    return stride(from: 3, to: input, by: 3).reduce(0, +) + stride(from: 5, to: input, by: 5).reduce(0, +) - stride(from: 15, to: input, by: 15).reduce(0, +)
}

print(FP(input : 1000))

// Output : 233168
```

1. `stride(from:to:by:)`返回一个序列，从from的参数值开始，以by作为步长，增加到循环终点to但并不包括to值

   - 通过`type(of: stride(from: 3, to: 10, by: 3))`我们可以得到该函数的返回值在Swift中定义为`StrideTo<Int>`

   - 我们可以打印出该序列

     ```swift
     for x in stride(from: 3, to: 10, by: 3) {
         print(x)
     }
     
     // Output : 3 6 9
     ```

2. `reduce(initialResult:nextPartialResult:)`返回使用给定**闭包**组合序列元素的结果
   - `initialResult`为组合值的初始值，当闭包执行时，`initialResult`会将自己的当前值传输给`nextPartialResult`
   - `nextPartialResult`是一个将组合值和序列元素组合成新的元素的闭包
   - 由`stride(from: 3, to: input, by: 3).reduce(0, +)`，前者会返回一个`StrideTo<int>`的序列，调用`reduce()`会将该序列使用闭包`+`累加，累加的初始值为0

3. 综上，该表达式的意思为：累加从3开始步长为3直到1000的序列，同时加上从5开始步长为5直到1000的序列，减去重复的从15开始步长为15直到1000的序列，即为我们的所求值。其中`stride(),reduce()`在函数式编程中也常常被使用到。

#### Haskell

```haskell
import Data.List (nub)
 
sum35 :: Integer -> Integer
sum35 n =
  let f = sumMul n
  in f 3 + f 5 - f 15
 
sumMul :: Integer -> Integer -> Integer
sumMul n f =
  let n1 = (n - 1) `div` f
  in f * n1 * (n1 + 1) `div` 2
 
--------------------------- Main ---------------------------
main :: IO ()
main =
  mapM_
    print
    [ sum35 1000
    ]	
    
--- Output : 233168 ---
```

1. `let [bindings] in [expressions]`是一个表达式，`[bindings]`中进行局部变量的定义，该表达式的值是`[expression]`

2. `div x y = x/y`为前缀函数的表示形式，为了书写的方便可以改写成中缀函数，在实际调用时写作

   ```
   x `div` y
   ```

3. 该段程序本质为高斯求和，可以翻译成如下代码

   ```cpp
   #define n 1000
   int func(int f){ // f为公差
       int x = (n - 1) / f;
       return f * x * (x + 1) / 2;
   }
   cout << func(3) + func(5) - func(15);
   ```

#### TypeScript

```typescript
const range = (start, end, i = start, arr = []) =>
  i === end - 1 ? [...arr, i] : range(start, end, i + 1, [...arr, i]);
const main = (n: number) => {
  const accu = ([head, ...tail]: number[], sum: number) => {
    if (head == undefined) {
      return sum;
    }
    return accu(tail, head % 3 === 0 || head % 5 === 0 ? head + sum : sum);
  };
  return accu(range(1, n), 0);
};
console.log(main(1000));


// Output : 233168
```

1. `range(s,e)`函数返回一个`number[]`，元素为区间[s,e)的所有整数元素
   - 函数调用过程本质为一个递归调用，变化如下 - `range(1,n) -> range(1,n,1,[]) -> range(1,n,2,[1]) -> range(1,n,3,[1,2]) -> ... -> [1,2,3,4,5,...,n-1]`

2. `accu()`函数也是一个递归函数，输入为`number[]`和当前的和`sum`。调用过程示例如下`accu([1,2,3,4,5],0) -> accu([2,3,4,5],1) -> accu([3,4,5],3) -> accu([4,5],3) -> accu([5],7) -> accu(undefined,7) -> 7`

3. `main(n : number)`函数传入表示递归的终点，在这里调用`main(1000)`，返回结果233168，通过`console.log()`将结果返回到控制台

### 逻辑式编程

#### Prolog

```prolog
sum_of_multiples_of_3_and_5_slow(N, TT) :-
	sum_of_multiples_of_3_and_5(N, 1, 0, TT).
 
sum_of_multiples_of_3_and_5(N, K, S, S) :-
	3 * K >= N.
 
sum_of_multiples_of_3_and_5(N, K, C, S) :-
	T3 is 3 * K, T3 < N,
	C3 is C + T3,
	T5 is 5 * K,
	(   (T5 < N, K mod 3 =\= 0)
	->  C5 is C3 + T5
	;   C5 = C3),
	K1 is K+1,
	sum_of_multiples_of_3_and_5(N, K1, C5, S).
 
```

输出如下：

```terminal
 ?- sum_of_multiples_of_3_and_5_slow(1000, TT).
TT = 233168 .
```

1. 如果`sum_of_multiples_of_3_and_5(N,1,0,TT)`为真，那么`sum_of_multiples_of_3_and_5_slow(N,TT)`为真

2. 如果3 * k >= N，则`sum_of_multiples_of_3_and_5(N,K,S,S)`为真，故顺便推导出1.也为真，输出结果

3. 对于第三条规则`sum_of_multiples_of_3_and_5(N,K,C,S)`为真的条件是如下规则

   - T3 = 3*K，T3 < N
   - C3 = C + T3
   - T5 = 5*K，T5 < N，K%3不等于0时 => 推出C5 = C3 + T5，否则C5 = C3
   - K1 = K + 1
   - 且`sum_of_multiples_of_3_and_5(N,K1,C5,S)`为真

4. 可以尝试改写成为另一种容易理解的方式

   ```
   int sum_of_multiples_of_3_and_5(N,K,C,S){
   		if(3 * K < N){
   				C3 = C + T3
   				T5 = 5 * K
   				if(T5 < N && k % 3 != 0){
   						C5 = C3 + T5
   				}else{
   						C5 = C3
   				}
   				K++;
   				return sum_of_multiples_of_3_and_5(N,K,C5,S).
   		}
   }
   ```

   该程序逻辑上等价于

   ```
   return sum_of_multiples_of_3_and_5(1000,K+1,C+3*K+5*K or C+3*K, S)
   ```

   - 即，从K=1开始向1000遍历，如果3\*K<1000，则当前的和累加3\*K，如果5*K<1000，当前的和累加5\*K。

   - 若5\*K不满足条件，则只累加3*K
   - 如果3\*K不满足条件，则满足第二条规则的3*K>=N，此时第二条规则中(N,K,S,S)和第三条规则(N,K,C,S)进行对比，相当于将C的值赋予给了S，写作`S = C`
   - 在第一条规则中，满足`sum_of_multiples_of_3_and_5(N, 1, 0, TT)`，则将TT的值传递给`sum_of_multiples_of_3_and_5_slow(N, TT) `
   - 此时TT为我们所求的最后结果，通过询问系统可以得到答案。