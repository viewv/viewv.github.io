---
title: Puncturable Pseudorandom Functions
date: 2024-10-02 14:15:15 +0200
categories: [Cryptography, 密码学]
tags: [cryptography]     # TAG names should always be lowercase
---

马上就要开始新学期了，本来计划假期写一下密码学课程学到的新东西，但是一直摸鱼到现在。
回想起来这密码学课，我跟导师说这密码学课太难了，导师还谐谑的说，Antoine 算是你同事啊，只能说我和Antoine合砍哥德尔奖了。得亏 Lucjan 好人期末考试难度不高。

## Puncturable Pseudorandom Functions

Puncturable Pseudorandom Functions (Puncturable PRFs) 是一种特殊类型的伪随机函数 (PRF)，在某些应用场景中具有重要意义。

### 1. 伪随机函数 (PRF)

一个伪随机函数是一个确定性的算法，它接受一个密钥和一个输入，并产生一个输出。其输出在统计上与真正的随机数无法区分。简单来说，给定一个密钥 $k$ 和一个输入 $x$，PRF 输出$F_k(x)$ 应该与从一个真正的随机函数中独立采样的结果无法区分。

### 2. Puncturable PRF 的定义

Puncturable PRF 是一种增强的 PRF，允许我们“打孔”它，也就是说，可以在某些特定的输入点上禁用该 PRF 的输出，而在其他输入点上仍然保持其伪随机性。

### 3. 主要特性

Puncturable PRF 具备以下关键特性：

- **选择性禁用**：可以生成一种修改过的密钥，使得在某些特定输入（打孔点）上无法计算出对应的输出，但在所有其他输入上仍然能计算出 PRF 的伪随机输出。
- **安全性**：修改后的密钥使得禁用点上的输出完全不可得，但在未禁用点上的输出仍然保持伪随机性。

### 4. 应用场景

Puncturable PRFs 在许多密码学应用中具有重要作用。例如：

- **密码学协议**：在某些加密协议中，可能需要在某些特定条件下撤销某些输入的访问权限，但不影响其他输入的访问。
- **访问控制**：在细粒度访问控制系统中，可以根据需要动态地修改访问权限。
- **基于时间的访问**：在某些时间点后撤销对某些数据的访问权限，同时保持对其他时间点数据的访问。

### 5. 工作机制

一个简单的 Puncturable PRF 示例：

1. **初始化**：设定一个初始密钥$k$
2. **计算 PRF**：使用密钥 $k$ 和输入 $x$ 计算 $F_k(x)$
3. **打孔操作**：选择要打孔的输入点$x'$, 生成一个新的密钥 $k'$, 使得$F_{k'}(x')$ 无法计算或固定为某个值。
4. **计算打孔后的 PRF**：对于所有 $x \neq x'$, 使用$k'$计算$F_{k'}(x)$，其结果与原密钥$k$的结果保持一致且伪随机。

> 问 GPT 让他给我写个例子我看看：
>
> 以下代码展示了如何使用 Puncturable PRF：

我们使用一个简单的哈希函数，假设如下哈希函数：
$$
\text{hash}(x) = (x \cdot 2654435761) \mod 2^{32}
$$
这是一个简化的哈希函数，仅用于示范。我们会展示如何使用这个哈希函数构造一个简单的 Puncturable PRF，并如何打孔特定的输入。

```python
class SimplePuncturablePRF:
    def __init__(self, key):
        self.key = key
        self.punctured_points = set()

    def hash(self, x):
        # 简化的哈希函数，用于示例
        return (x * 2654435761) % (2 ** 32)
    
    def prf(self, x):
        if x in self.punctured_points:
            raise ValueError("This input has been punctured and cannot be used.")
        return self.hash(self.key ^ x)
    
    def puncture(self, x):
        self.punctured_points.add(x)

# 示例使用
key = 12345
prf = SimplePuncturablePRF(key)

# 计算PRF输出
input1 = 1
input2 = 2

output1 = prf.prf(input1)
output2 = prf.prf(input2)

print(f"PRF({input1}) = {output1}")
print(f"PRF({input2}) = {output2}")

# 打孔输入1
prf.puncture(input1)

# 尝试计算被打孔的输入
try:
    prf.prf(input1)
except ValueError as e:
    print(e)  # This input has been punctured and cannot be used.

# 计算未被打孔的输入
output2_again = prf.prf(input2)
print(f"PRF({input2}) again = {output2_again}")
```

### 解释

1. **初始化PRF**：

    ```python
    key = 12345
    prf = SimplePuncturablePRF(key)
    ```

2. **计算PRF输出**：

    对于输入1和2，计算其PRF输出：

    ```python
    input1 = 1
    input2 = 2
        
    output1 = prf.prf(input1)
    output2 = prf.prf(input2)
        
    print(f"PRF({input1}) = {output1}")
    print(f"PRF({input2}) = {output2}")
    ```

1. **打孔操作**：

    打孔输入1，之后尝试计算输入1时会报错：

    ```python
    prf.puncture(input1)
        
    try:
        prf.prf(input1)
    except ValueError as e:
        print(e)  # This input has been punctured and cannot be used.
    ```

2. **未被打孔的输入**：

    对于未被打孔的输入2，仍然可以正常计算PRF输出：

    ```python
    output2_again = prf.prf(input2)
    print(f"PRF({input2}) again = {output2_again}")
    ```

感觉这个例子纯暴力展示，参考意义不大。
