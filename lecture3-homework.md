
# 第3课 课后作业

## 第1题 二次非剩余 Quadratic nonresidue

(1) **完备性**：如果 $QR(m, x)=0$ ，就意味着 $x$ 在模 $m$ 下是二次非剩余的。如果Verifier抛出硬币，那么只有两种可能。

* 如果 $b=0$， $y=s^2x$ ，并且Prover是诚实的，那么 $QR(m,y)=QR(m,s^2x)$ 。由于 $QR(m,x)=0$ ，那么 $QR(m,s^2x)=0$ ，因此Verifier接收到的是0，并且  $QR(m,y)=0=b$ ，验证者接受并通过。

* 如果 $b=1$， $y = s^2$ ，Prover计算可得 $QR(m,y)=QR(m,s^2)$ 。 这说明 $s^2$ 一定是模 $m$ 的二次剩余。因为此时必定存在一个数 $s$ ，使得 $s^2 = s^2 \mod m$ 。因此 $QR(m,s^2)=1$ ，Verifier就会接收到1，验证 $QR(m,y)=1=b$ ，验证者收到并通过。

(2) **可靠性**：
* 如果 $QR(m,x)=1 $，那么 $x$是模 $m$ 的二次剩余。如果Verifier选择 $b=0$ ，则会发送 $y = s^2x$ ， $QR(m,y)=QR(m,s^2x)=1 \neq b$ 
* 如果Verifier选择 $b=1$ ， 那么会发送 $y=s^2$ ， $QR(m,y)=QR(m,s^2)=1=b$ 。如果Prover诚实计算 $QR(m,y)$ ，只会有 $1/2$ 的概率可以通过验证，如果Prover不诚实计算 $QR(m,y)$ ，通过验证的概率不会超过 $1/2$ 。总而言之，Verifier都会以超过 $1/2$ 的概率拒绝。

## 第2题 二次剩余 Quadratic residue

(1)**完备性**： $QR(m,x)=1$ ， Verifier选择的 $b$ 的取值有两种可能：

* 如果 $b=0$ ，此时Prover计算的 $u=t$ 。Prover将 $u$ 发送给Verifier进行验证，等式两边分别为 $y = xt^2\mod m$ 和 $u^2 x\mod m = t^2x\mod m$ 。等式两边相等，可以通过验证。

* 如果 $b=1$ ，此时Prover计算 $u = st$ 。Prover将 $u$ 发送给Verifier进行验证，等式两边分别为 $y = xt^2\mod m$ 和 $u^2\mod m = s^2t^2\mod m$ 。由于 $QR(m,x)=1$ ，因此 $s^2 = x\mod m$ ，于是等式左右两边相等，验证同样通过。

(2)**可靠性**：如果 $QR(m,x)=0$ 。

* 如果 $b=0$ ，由于 $y=xt^2 = u^2x\mod m$ ，Prover选择 $u=t$ 可以通过验证。

* 如果 $b=1$ ，由于 $QR(m,x)=0$ ，不存在 $s$ ，使得 $s^2 = x\mod m$ 。因此Verifier验证 $y = xt^2 = u^2\mod m$ 会失败。

总之，Verifier会以超过 $1/2$ 的概率拒绝。

(2*)**知识可靠性**：暂时未考虑清楚

(3)**零知识**：暂时未考虑清楚

## 第3题 双线性自映射意味着DDH的失效 Self-pairing implies failure of DDH

答：如果在等式 $\alpha \beta g = y$ 两端同时计算与 $g$ 的映射，得到 $e(\alpha \beta g, g) = e(\alpha g, \beta g) = e(y, g)$ 。由此可以说明等式 $\alpha \beta g = y$ 成立。

## 第4题 BLS 签名聚合 BLS signature aggregation

答：验证算法可以接受正确的签名。因为在验证 $(pk,m,\sigma)$ 时，等式两端分别为 $e(g_0,\sigma) = e(g_0, \alpha H(m))$ 和 $e(pk,H(m)) = e(\alpha g_0, H(m))= e(g_0,\alpha H(m))$ 。等式两端相等，就说明正确的签名会始终通过验证算法。

选择消息攻击下的存在性不可伪造性：
* 在多项式时间计算出 $\alpha$ 是困难的，因此攻击者很难伪造签名 $\sigma \leftarrow \alpha H(m)$ 。因为这是一个离散对数困难问题;
* 哈希函数具有抗碰撞性，因此无法在多项式时间内找到一个 $m$ ，使得 $H(m)$ 等于一个事先给定的值。所以攻击者想要任意选择消息 $m$ 使得 $H(m)$ 等于一个特定的值，这在多项式时间内也无法做到。
