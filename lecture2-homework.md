# 第2课 课后作业


### 第一题 Num2Bits

```go
pragma circom 2.1.4;

//Num2Bits(nBits)的nBits为位数的个数， 最高位为bit[nBits-1], 当nBits=253时，最高位为bit[252]. 如果要用Num2Bits 表示p/2, 需要的nBits=253。 
如果用Num2Bits(252)来表示p/2，会出现错误，所以需要用Num2Bits(253)来表示p/2. 但是p/2 != 2^253 -1. 也就是是说p/2+1 也可以用 Num2Bits(253)来表示。

pragma circom 2.1.4;

template Num2Bits(nBits){
    signal input in;
    signal output {binary} out[nBits];
    assert(in < 2 ** (nBits+1));
    log("in:",in);

    var num = in;
    var val = 0;
    var e2 = 1;
    for (var i=0;i<nBits;i++){
        out[i] <-- num % 2;
        out[i]*(1-out[i]) === 0;
        num = num >> 1;
        val = val + out[i] * e2;
        e2 = e2+e2;
    }
    val === in;
}

component main = Num2Bits(8);

/* INPUT = {
"in": "255"
}*/
```

### 第二题 IsZero

```go
pragma circom 2.1.4;

template IsZero () {
    signal input in;
    signal output out;
    
    signal inv;

    inv <-- in!=0? 1/in : 0;
    out <== -in * inv + 1; 
    in * out === 0;
}

component main = IsZero();

/* INPUT = {
    "in": "235"
} */
```

### 第三题 IsEqual

```go
pragma circom 2.1.4;

template IsZero () {
    signal input in;
    signal output out;
    
    signal inv;

    inv <-- in!=0? 1/in : 0;
    out <== -in * inv + 1;
    in * out === 0;
}

template IsEqual () {
    signal input in[2];
    signal output out;
    out <== IsZero()(in[0]-in[1]);
}

component main = IsEqual();

/* INPUT = {
    "in": ["235", "235"]
} */
```

### 第四题 selector

```go
pragma circom 2.1.4;
include "circomlib/comparators.circom";

template CalculateTotal(n) {
    signal input in[n];
    signal output out;

    signal sums[n];

    sums[0] <== in[0];

    for (var i = 1; i < n; i++) { //通过遍历来解决condition 不能为unknow的问题。
        sums[i] <== sums[i-1] + in[i];
    }

    out <== sums[n-1];
}

template Selector (nChoices) {
    signal input in[nChoices];
    signal input index;
    signal output out;

    component total = CalculateTotal(nChoices);
    component eqs[nChoices];

    // For each item, check whether its index equals the input index.
    for (var i = 0; i < nChoices; i ++) {
        eqs[i] = IsEqual();
        eqs[i].in[0] <== i;
        eqs[i].in[1] <== index;

        // eqs[i].out is 1 if the index matches. As such, at most one input to
        // calcTotal is not 0.
        total.in[i] <== eqs[i].out * in[i];
    }

    out <== total.out;
}

component main = Selector(8);

/* INPUT = {
    "in": ["0","1","2","3","4","5","6","7"],
    "index":"0" 
} */
```

### 第五题IsNegative

```go
pragma circom 2.1.4;
include "circomlib/comparators.circom";

template LessThanBits254(n) {
    assert(n <= 254);  //enlarged 
    signal input in[2];
    signal output out;

    component n2b = Num2Bits(n+1);  // 

    n2b.in <== in[0]+ (1<<n) - in[1]; //最高位为1,

    out <== 1-n2b.out[n];  //如果最高位被借走了，说明 in[0]<in[1]. 
}

template IsNegative () {
    signal input in;
    signal output out;
    signal remainder;
    var halfp = 10944121435919637611123202872628637544274182200208017171849102093287904247808;
   
    out <-- in \ halfp;
    remainder <-- in % halfp;
    out*(1-out) === 0;
    
    component lt = LessThanBits254(254);
    lt.in[0] <== remainder;
    lt.in[1] <== halfp;
    lt.out === 1;

    in === out * halfp + remainder;  

}

component main = IsNegative();

/* INPUT = {
    "in": "10944121435919637611123202872628637544274182200208017171849102093287904247809"
} */
```

### 第6题LessThan

```go
pragma circom 2.1.4;

template Num2Bits(nBits){
    signal input in;
    signal output {binary} out[nBits];

    var num = in;
    var val = 0;
    var e2 = 1;
    for (var i=0;i<nBits;i++){
        out[i] <-- num % 2;
        out[i]*(1-out[i]) === 0;
        num = num >> 1;
        val = val + out[i] * e2;
        e2 = e2+e2;
    }
    val === in;
}

template LessThan(n) {
    assert(n <= 252);  //enlarged 
    signal input in[2];
    signal output out;
    
    component n2b = Num2Bits(n+1); 

    n2b.in <== in[0]+ (1<<n) - in[1];
    out <== 1-n2b.out[n];
}

template LessEqThan(n) {
    assert(n <= 252);  //enlarged 
    signal input in[2];
    signal output out;
    
    component lt = LessThan(n);
    lt.in[0] <== in[0];
    lt.in[1] <== in[1];
    out <== lt.out;
}

template GreaterThan(n) {
    assert(n <= 252);  //enlarged 
    signal input in[2];
    signal output out;
    
    component lt = LessThan(n);

    lt.in[0] <== in[1];
    lt.in[1] <== in[0];
    out <== lt.out;
}

template GreaterEqThan(n) {
    assert(n <= 252);  //enlarged 
    signal input in[2];
    signal output out;
    
    component lt = LessThan(n);
    
    lt.in[0] <== in[1];
    lt.in[1] <== in[0]+1;
    out <== lt.out;
}

component main = LessThan(252);

/* INPUT = {
    "in": ["1", "2"]
} */
```

### 第7题IntegerDivide

```go
pragma circom 2.1.4;
include "circomlib/comparators.circom";
include "circomlib/sign.circom";

template LessThanBits254(n) {
    assert(n <= 254);  //enlarged 
    signal input in[2];
    signal output out;

    component n2b = Num2Bits(n+1);  

    n2b.in <== in[0]+ (1<<n) - in[1];

    out <== 1-n2b.out[n];
}

template IsNegative() {
    signal input in;
    signal output out;

    component num2Bits = Num2Bits(254);
    num2Bits.in <== in;
    component sign = Sign();
    
    for (var i = 0; i < 254; i++) {
        sign.in[i] <== num2Bits.out[i];
    }

    out <== sign.sign;
}

template IntegerDiv(nBits){
    assert(nBits <= 126);
    signal input dividend;
    signal input divisor;
    signal output remainder;
    signal output quotient;
    assert(dividend < 2 ** nBits); 
    assert(divisor < 2 ** nBits);  

    quotient <-- dividend \ divisor ;
    remainder <-- dividend % divisor;
    
    signal lt <== LessThan(nBits)([remainder, divisor]);
    lt === 1;

    dividend === divisor * quotient + remainder;
}

/*
 8 / 5 = 1, 3
 8 / -5 = -1, 3
 -8 / 5 = -1, -3
 -8 / -5 = 1, -3 
*/
template IntegerDivNeg(nBits){
    signal input dividend,divisor;
    signal output quotient,remainder;

    signal isDivdendNeg, isDivisorNeg;
    signal dividendSign, divisorSign;
    signal dividendAbs, divisorAbs;
    signal quotientSign,remainderSign;
    signal quotientAbs,remainderAbs;
    assert(divisor != 0);
    // signal isZero <== IsZero()(divisor);
    // isZero === 0;

    isDivdendNeg <== IsNegative()(dividend);
    isDivisorNeg <== IsNegative()(divisor);

    dividendSign <== 1 + isDivdendNeg * -2;  //1 or -1, 用计算取代判断
    divisorSign <== 1 + isDivisorNeg * -2;   //1 or -1
    quotientSign <== dividendSign * divisorSign; //确定商的符号,商的符号等于除数和被除数符号的乘积
    remainderSign <== dividendSign;  //余数的符号与被除数相同

    dividendAbs <== dividend * dividendSign; 
    divisorAbs  <== divisor * divisorSign;

    remainderAbs <-- dividendAbs % divisorAbs;
    quotientAbs <-- dividendAbs \ divisorAbs;

    quotient <== quotientAbs * quotientSign;
    remainder <== remainderAbs * remainderSign;

    signal lt<== LessThanBits254(nBits)([remainderAbs,divisorAbs]);
    lt === 1;

    dividend === quotient * divisor + remainder;
}

component main = IntegerDivNeg(254);

/* INPUT = {
    "dividend":"8",
    "divisor":"4"
} */
```

### 第8题Sort

```go
pragma circom 2.1.4;

include "circomlib/comparators.circom";

template Swap(nBits){
    signal input in[2];
    signal output out[2];

    signal gt <== GreaterThan(nBits)([in[0], in[1]]);
    out[0] <== (in[1] - in[0])*gt + in[0];
    out[1] <== (in[0] - in[1])*gt + in[1];
}

template BubbleSort(n, nBits){
    signal input in[n];
    signal output out[n];
    signal tmp[n][n][n]; //[round][step][element]

    component swap[n-1][n-1];

    tmp[0][0][0] <== in[0];
    tmp[0][0][1] <== in[1];

    for (var j=2;j<n;j++){
        tmp[0][j-1][j] <== in[j]; 
    }

    // (i-1)round, the ith round has (n-i-1) step.
    for (var i=0; i<n-1;i++){
        for (var j=0;j < n -i -1;j++){   
            swap[i][j] = Swap(nBits);
            swap[i][j].in[0] <== tmp[i][j][j];
            swap[i][j].in[1] <== tmp[i][j][j+1];

            tmp[i][j+1][j] <== swap[i][j].out[0];
            tmp[i][j+1][j+1] <== swap[i][j].out[1];
        }
        
        //prepare data for the next round 
        tmp[i+1][0][0] <== tmp[i][1][0];
        for (var k=1;k< n-i-1;k++){
            tmp[i+1][k-1][k] <== tmp[i][k+1][k]; 
        }
    }
    
    //pickup output
    out[0] <== tmp[n-2][1][0];
    out[1] <== tmp[n-2][1][1];

    for (var i=2;i<=n-1;i++){
        out[i] <== tmp[n-1-i][i][i];
    }
}

component main = BubbleSort(16, 252);

/* INPUT = {
    "in": ["9","16","7","25","4","3","34","1","0","5","100","20","200","1","18","8"]
} */
```
