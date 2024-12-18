# BitSet

**运算符优先级表**

| 优先级 | 运算符类别 | 运算符                               | 描述                                 |
| ------ | ---------- | ------------------------------------ | ------------------------------------ |
| 1      | 后缀运算符 | `[]`, `()`, `.`                      | 数组索引、函数调用、成员访问         |
| 2      | 单目运算符 | `++`, `--`, `+`, `-`, `~`, `!`       | 自增自减、正负号、按位取反、逻辑非   |
| 3      | 算术运算符 | `*`, `/`, `%`                        | 乘法、除法、取余                     |
| 4      | 算术运算符 | `+`, `-`                             | 加法、减法                           |
| 5      | 关系运算符 | `<`, `<=`, `>`, `>=`                 | 小于、小于等于、大于、大于等于       |
| 6      | 等号运算符 | `==`, `!=`                           | 等于、不等于                         |
| 7      | 位运算符   | `&`                                  | 按位与                               |
| 8      | 位运算符   | `^`                                  | 按位异或                             |
| 9      | 位运算符   | `|`                                  | 按位或                               |
| 10     | 逻辑运算符 | `&&`                                 | 逻辑与                               |
| 11     | 逻辑运算符 | `||`                                 | 逻辑或                               |
| 12     | 条件运算符 | `?:`                                 | 三元条件运算符                       |
| 13     | 赋值运算符 | `=`, `+=`, `-=`, `*=`, `/=`, `%=` 等 | 赋值、加等、减等、乘等、除等、取余等 |
| 14     | 逗号运算符 | `,`                                  | 多个操作数之间的分隔符               |

## 代码实现

```java
// a = b
static void set(long[] a, long[] b) {
    System.arraycopy(b, 0, a, 0, a.length);
}

// a = a | (b << off)
static void orShiftLeft(long[] a, long[] b, int off) {
    int x = off >> 6, y = off & 0x3f;
    for (int i = a.length - 1, j = a.length - 1 - x; j >= 0; i--, j--) {
        a[i] |= b[j] << y;
        if (j > 0 && y > 0) {
            a[i] |= b[j - 1] >>> (0x40 - y);
        }
    }
}

// a = a | (b >> off)
static void orShiftRight(long[] a, long[] b, int off) {
    int x = off >> 6, y = off & 0x3f;
    for (int i = 0, j = x; j < a.length; i++, j++) {
        a[i] |= b[j] >>> y;
        if (j + 1 < a.length && y > 0) {
            a[i] |= b[j + 1] << (0x40 - y); // 溢出处理
        }
    }
}

// a = a << off
static void shiftLeft(long[] a, int off) {
    int x = off >> 6, y = off & 0x3f;
    for (int i = a.length - 1, j = a.length - 1 - x; i >= 0; i--, j--) {
        a[i] = j >= 0 ? a[j] << y : 0;
        if (j > 0 && y > 0) {
            a[i] |= a[j - 1] >>> (0x40 - y);
        }
    }
}

// a = a >> off
static void shiftRight(long[] a, int off) {
    int x = off >> 6, y = off & 0x3f;
    for (int i = 0, j = x; i < a.length; i++, j++) {
        a[i] = j < a.length ? a[j] >>> y : 0;
        if (j + 1 < a.length && y > 0) {
            a[i] |= a[j + 1] << (0x40 - y);
        }
    }
}

// a = a | b
static void or(long[] a, long[] b) {
    for (int i = 0; i < a.length; i++) {
        a[i] |= b[i];
    }
}

// return a >> pos & 1
static long get(long[] a, int pos) {
    return a[pos >> 6] >> (pos & 0x3f) & 1;
}

// a = a ^ (1 << pos) 翻转单一位置的位
static void xor(long[] a, int pos) {
    a[pos >> 6] ^= 1L << (pos & 0x3f);
}

// a = a | (1 << pos)
static void or(long[] a, int pos) {
    a[pos >> 6] |= 1L << (pos & 0x3f);
}

// a = a & (1 << pos)
static void and(long[] a, int pos) {
    a[pos >> 6] &= 1L << (pos & 0x3f);
}

// a = 0 清空所有位（reset）
static void reset(long[] a) {
    Arrays.fill(a, 0L);
}

// a = ~a 按位取反（NOT）操作
static void not(long[] a) {
    for (int i = 0; i < a.length; i++) {
        a[i] = ~a[i];
    }
}

// bitCount(a) 计算bitset中1的个数
static int bitCount(long[] a) {
    int count = 0;
    for (long x : a) {
        count += Long.bitCount(x);
    }
    return count;
}

// 打印
static String print(long[] a) {
    StringBuilder sb = new StringBuilder();
    for (int i = a.length - 1; i >= 0; i--) {
        sb.append(("0".repeat(Long.numberOfLeadingZeros(a[i])) + (a[i] != 0 ? Long.toBinaryString(a[i]) : "")) + " ");
    }
    return sb.toString();
}
```

## 相关题目

[CF2028F bitset+dp](https://codeforces.com/contest/2028/problem/F)