# 3 메모리와 디스크의 핵심: 순차 논리

컴퓨터는 어떻게 현재 처리 중인 수를 기억하면서 계산을 처리할까? 이 원리를 알기 위해서는 sequential logic(순차 논리)를 살펴 보아야 한다.

사람은 하루를 86,300초, 즉 지구 자전의 1/86,400이라는 periodic function으로 정의해서 시간을 측정한다.

컴퓨터 역시 이런 주기적인 전기 신호가 필요하다. 우선 inverter를 이용한 방법을 살펴보자

---

## 3.1 oscillator

inverter의 output을 다시 input에 연결하면 신호는 계속해서 complementary한 신호로 변할 것이다.

![oscillator](images/oscillator.png)

이런 장치를 **oscillator**(발진자)라고 하며, 이런 연결을 **feedback**(되먹임)이라고 한다. 이렇게 output은 0과 1을 oscillate(진동)한다.

이 값이 진동하는 속도는 propagation delay(전파 지연)에 따라 결정된다. 따라서 특히 온도가 영향을 줄 수 있기 때문에 주로 이를 방지할 수 있으면서 저렴한 crystal을 사용한다.(아래는 crystal의 schematic 기호이다.)

![크리스털 스키매틱 기호](images/schematic_crystal.png)

여기서 핵심이 되는 것이 **Piezoelectricity**(압전) 효과이다. 기계적 자극으로 일그러지면서 특정 압전 소자에 분극 효과가 발생하고, 분극 효과에 의해 순간적으로 전압이 발생하며 전류가 흐르게 된다.

> 피에르 퀴리 형제가 이 효과를 발견해서 Piezoelectricity라는 이름이 붙었다.

> 압전 소자에 인공 세라믹 재질을 많이 사용하면서 종종 압전 세라믹이라고도 부른다.

electrode(전극)을 crystal에 연결하고, 전류가 흐르면서 crystal이 구부러진다. 이런 자극에 crystal이 전기를 다시 만들어내는 시간은 매우 정확하기 때문에, 이를 응용해서 oscillator로 사용하는 것이다.

> 여러 발음체나 microphone(마이크)도 이런 piezoelectricity 효과를 이용한다. 이 경우에는 crystal에 전기 신호를 고압으로 입력해서 생기는 진동을 사용한다.

---

## 3.1.2 clock

여러 응용 프로그램에서 시간을 정확히 알 필요가 있지만, low level에서도 시간이 중요한 이유가 있다. 가령 시간을 알 수 있으면, 정해둔 시간만큼 adder의 지연을 기다릴 수 있다.

> 사용자보다 전자적/기계적 부품에 가까울 수록 low level(저수준)이라고 한다. 가령 트랜지스터는 트랜지스터로 구성된 게이트보다 low level이며, 이런 게이트나 여러 논리 회로로 조합해 만든 CPU는 트랜지스터보다 더 high level이다.

oscillator는 컴퓨터에 clock(시간을 셀 수 있게 해주는 신호)을 제공한다. 이에 따라 회로의 pace가 결정되며, 최대 clock 속도나 가장 빠른 tempo는 회로의 전파 지연 시간에 따라 결정한다.

그런데 부품들 사이에서도 지연 시간의 차이가 있다. 그래서 **binning**(부품의 특성에 따라 분류하는 과정)이 필요한데, 지연 시간이 짧은 부품과 긴 부품을 다른 bin으로 분류한다.

> 회로에서 종종 들을 수 있는 **overclocking**(오버클로킹)이란, 통계적으로 bin의 중간 정도에 위치하는 부품을 고장나지 않을 범위 안에서 clock을 빠르게 공급하는 도박을 한다는 뜻이다.

---

## 3.2 latch

주기적인 신호를 oscillator로 만든다면, 그 정보는 어떻게 기억할까? OR 게이트의 output을 input으로 다시 주는 feedback을 사용하면 기억할 수 있다.

> 기본적으로 아래는 1bit 메모리를 기억하는 방법이다.

- OR gate latch

![OR 게이트 래치](images/or_gate_latch.png)

우선 OR 게이트의 feedback 구조를 보자. input 신호가 0 to 1이 되는 순간 output은 0에서 1로 바뀌며 상태를 유지하게 된다. 즉, 신호가 변했다는 것을 기억하게 된다.

하지만 이 회로만으로는 output을 다시 0으로 만들 수 없다. 아래는 feedback을 끊고 reset할 방법을 추가한 구조다.

<br/>

- AND-OR gate latch

![AND-OR 게이트 래치](images/and-or_latch.png)

- inverter의 output $\overline{reset}$ : bar는 **opposite**을 의미한다.(리셋 바로 읽는다)

  - 즉, 0이면 1, 1이면 0이 된다. 이러한 성질 때문에 true를 표현하기 위해 오히려 0을 사용한다는 점에서 **active low**(액티브 로우)라고 부르기도 한다. (반대는 active high)

  - 값을 기억하고 있기 위해서는 $\overline{reset}$ 는 1을 유지하고 있어야 한다.

- $\overline{reset}$ 은 AND 게이트와 이어진다. 만약 $\overline{reset}$ 가 0(reset=1)이 되면, 1로 기억하고 있던 out이 다시 0으로 reset된다.

<br/>

- S-R latch

S-R(set-reset) latch는 NAND 게이트를 이용한 방법이다. 이 구조는 $Q$ 와 $\overline{Q}$ 로 서로 complementary한 output을 내놓는다.

> NOR를 쓰면 input이 active high한 latch를 만들 수 있다. 그러나 이 설게는 더 전력을 많이 사용하고 복잡하며 비용도 더 소모하게 된다.

> NAND는 무조건 모든 input이 1이 되지 않는 이상 output이 1이라는 점을 기억하자.

![S-R latch](images/s-r_latch.png)

- $\overline{set}$ , $\overline{reset}$ 의 **초깃값은 알 수 없다**. (온도나 습도에 따라서도 달라지기 때문에 알 수 없다.) 따라서 초깃값을 상정한 계산은 불가능하며, <U>로직을 한 차례 재설정</U>하는 과정을 거쳐야 한다.

  - 예를 들어 $\overline{set}$ 를 0, $\overline{reset}$ 를 1로 재설정하면, <U>초기 상태가 무엇이었든</U> $Q$ 와 $\overline{Q}$ 가 각각 1과 0이 된다.

- 주의할 점은 $\overline{set}$ 도 0, $\overline{reset}$ 도 0이면, 두 output이 complementary하지 않기 때문에(둘 다 1이 된다.) 사용하면 안 된다.

- 마찬가지로 동시에 low나 high로 바뀌는 경우도 전파 지연 시간에 따라 출력이 달라질 수 있어서 결과를 예측할 수 없게 된다.

> 회로 설계가 대칭적이기 때문에 지연 시간이 비슷하다는 장점도 갖는다.

---

## 3.2.1 gate가 있는 latch

- gated S-R latch

아래는 S-R latch에 gate 한 쌍을 추가한 Gated S-R latch 회로이다.

![게이트가 있는 S-R latch](images/gated_S-R_latch.jpg)

![게이트가 있는 S-R latch 2](images/s-r_latch_and_gate.png)

> (not A or not B) = A NAND B. 즉, 위 회로의 기능 자체는 동일하다. 아래 회로는 이미 complementary한 신호를 받은 경우일 뿐으로 해석에 주의. ENABLE = gate이다.

- $gate$ = 0 : $set$ 과 $reset$ 은 아무런 영향을 받지 않는다.

- $gate$ = 1 : $set$ 과 $reset$ 중 1인 값이 0으로 바뀌게 된다.(S-R latch에서 양쪽 다 1인 경우는 사용할 수 없었다.)

즉, $set$ 과 $reset$ 을 서로 complementary한 신호로 받아도, 둘 다 0이 되는 경우를 gate를 open하는 것으로 구현할 수 있는 셈이다.

즉, inverter를 입력 쪽에 추가하면, 1 bit 입력 $D$ 하나만을 받아서 회로를 구성할 수 있다.

<br/>

- gated D latch

![게이트가 있는 D latch](images/gated_D_latch.png)

> Clk = gate

- gate가 1일 때는 0이 1, 1이 0으로 바뀌게 된다. 즉 $\overline{S}$ , $\overline{R}$ 이 된다.

- 반면 gate가 0이라면 두 $S$ , $R$ 값이 모두 1이 된다. 이 경우 $memory$ 와 $\overline{memory}$ 가 서로 반전된다.

> 앞서 재설정 과정을 적용하려면, $D$ 를 1, $gate$ 를 1로 설정하면 된다. 이 경우 $Q$ 와 $\overline{Q}$ 가 각각 1과 0이 된다.

> 위에서 $D$ 가 0이었다면 $Q$ 와 $\overline{Q}$ 는 각각 0과 1로 설정된다. S-R latch가 대칭 구조라는 점을 생각하자.

아래는 회로의 타이밍 다이어그램이다.

![게이트가 있는 D latch 타이밍 다이어그램](images/D_latch_and_gate_timing_diagram.png)

> Q가 바뀌는 구간을 유심히 살펴보자.

- gate = 1( $\overline{gate}$ = 0 ) : $D$ 의 변화에 따라 <U>신호가 바뀌게 된다.</U> 

- gate = 0( $\overline{gate}$ = 1 ) : $D$ 의 변화와 관계 없이 $memory$ 와 $\overline{memory}$ 만 한 차례 반전된다.

이를 다르게 생각해서 짧은 시간에만 $D$ 에 의해 생기는 변화를 조절하면, 좀 더 정확하게 신호를 조정할 수 있을 것이다.

---

## 3.2.2 flip-flop

앞서 언급한 것처럼 정확한 시간에 순간적으로만 변화가 생기도록 만들면 잘못된 결과가 생길 가능성을 최소화할 수 있을 것이다. 이처럼 level이 변화할 때 데이터를 catch하는 것을 **edge**(에지)라고 부른다.

이런 edge에 의해 데이터 변화를 만들어내는 latch가 있다. 3개의 S-R latch를 연결해서 만들 수 있는 D filp-flop부터 살펴보자.

![D 플립플롭](images/D_flip_flop.png)

- $Q$ 와 $\overline{Q}$ 가 서로 complementary한 output이다.

이 D flip-flop은 <U>0 to 1로 바뀌는 순간</U>에 변화가 생긴다. 이를 **positive edge-triggered** flip-flop이라고 한다.(반대는 1 to 0일 때 변화가 생기는 negative edge-triggered이다.)

회로의 동작을 이해하기 위해 아래 그림을 살펴보자. 그림 다섯 개를 각각 설명할 것이다.

![D 플립플롭 동작](images/D_flip_flop_2.png)

> 가는 선 : 0, 굵은 선 : 1 을 memory하고 있음을 나타낸다.

- 첫 번째 그림

  - clock = 0

  - $\overline{S}$ = 1

  - $\overline{R}$ = 1

NAND는 모든 input이 1이어야 0으로 바뀌므로 변화는 없다. 즉, clock = 0일 때는 D가 무슨 값이어도 결과는 바뀌지 않는다. 

무조건 $\overline{S}$ , $\overline{R}$ 은 1로 고정된다.

- 두 번째, 세 번째 그림

  - clock = 1

  - D = 0 / 1

  - $\overline{S}$ = 1

  - $\overline{R}$ = 0

(clock = 1) $\overline{R}$ 에 0이 저장되어 있으므로, 이 상태에서는 D가 무슨 값이어도 무조건 1로 바뀌게 된다.

이때 $\overline{R}$ = 0을 바꾸려면 clock=0으로 조절해야 가능하다.

- 네 번째, 다섯 번째 그림

  - clock = 1

  - D = 0 / 1

  - $\overline{S}$ = 0

  - $\overline{R}$ = 1

(clock = 1) $\overline{S}$ 에 저장된 값이 0일 때는 무조건 $\overline{R}$ = 1이 된다. 

그런데 clock이 0으로 바뀌기 전까지 $\overline{S}$ = 0으로 바뀌지 않기 때문에, 결과적으로는 $D$ 값을 바꿔도 아무런 의미가 없다.

<br/>

즉, 위 조건들에서는 clock이 변화해야만 내부에 저장된 값들이 바뀌면서 $D$ 값에 따라 조절할 수 있는 것이다.

![D 플립플롭 7474](images/D_flip_flop_schematic.png)

> S, R input에 ○가 붙어 있는 점에 유의하자. 이는 $\overline{S}$ , $\overline{R}$ 에 해당한다. 따라서 S-R latch와 같다.

위는 1918년 나온 7474라 불리는 D 플립플롭의 schematic 기호다.

edge에 의해 상태 변화가 촉발되는 장치에서는 전파 지연 외 요소도 고려해야 한다.

- $t_{setup}$ : **setup time**(설정 시간)은 clock edge가 '발생하기 전'에 얼마나 input이 안정적으로 유지돼야 하는지를 나타낸다.

- $t_{hold}$ : **hold time**(유지 시간)은 clock edge가 '발생한 후' 얼마나 input이 안정적으로 유지돼야 하는지를 나타낸다.

다시 말해 clock edge가 전후로 setup time+hold time 동안 input이 안정적이여야만 올바른 출력으로 보는 것이다.

---

## 3.3 counter

flip-flop을 응용해서 1,2,3 순서대로 수를 세는 counter(카운터)를 만들 수 있다. 수를 세려면 oscillator에서 나온 시간을 세서 decoder로 공급하는 역할을 하면 된다.

아래는 3bit ripple counter(리플 카운터)다. signal이 0에서 1로 가는 숫자를 세서 3비트 수( $C_2, C_1, C_0$ 를 만들어 낸다.

> 왼쪽에서 오른쪽으로 ripple(물결)이 퍼져나가듯 개수를 세기 때문에 이런 이름이 붙었다. $C_0$ 은 $C_1$ 을 바꾸고, $C_1$ 은 $C_2$ 를 바꾸는 식으로 과정을 반복한다.

![3비트 리플 카운터](images/3bit_ripple_counter.png)

- $\overline{reset}$ 신호를 사용하면 counter를 0으로 reset할 수 있다.

- 출력 $\overline{Q}$ 가 자신의 input $D$ 와 연결되어 있다.

각 bit가 다른 bit의 상태 변화 이후 약간의 시차를 두고 바뀌기 때문에, 이 회로를 **asynchronous counter**(비동기 카운터)라고 부른다.

> 이런 성질 때문에 언제 결과를 살펴봐야 할지 알기 어렵다는 단점이 있다. 또한 오랜 시간이 걸리게 된다. 때때로 전파 지연에 따라 출력이 틀리기까지 한다.

이런 asynchronous counter의 문제를 해결할 수 있는 counter로, **synchronous counter**(동기 카운터)가 있다. 아래는 3bit asynchronous counter다.

![3비트 동기 카운터](images/3bit_synchronous_counter.png)

이 counter는 모든 flip-flop에 같은 clock이 들어간다. 따라서 상태가 모두 동시에 변하게 된다.

정확한 출력 생성 시점을 알기 위해서는 여전히 전파 지연은 고려해야 하지만, filp-flop 연결로 생긴 시차를 고려하지 않아도 된다.

counter도 하나의 schematic 기호로 표현할 수 있다. 종류에 따라 일부만 제공할 수도 있다.

![카운터 스키매틱 기호](images/counter_schematic.png)

- CLR : reset, 즉 counter를 0으로 되돌린다.

- EN : counter를 활성화한다.(true일 때만 counter가 활성화)

- $U/\overline{D}$ : counter가 개수를 증가시킬지(1), 감소시킬지(0)를 결정한다.

- LD : counter를 input에서 지정한 값으로 설정한다.

---

## 3.4 register

값을 쉽게 기억하는 D flip-flop을 한 패키지에 넣은 register 회로가 있다.

![레지스터](images/register.png)

---