# 2 전자 회로의 조합 논리

오늘날 컴퓨터 clock speed(클록 속도)는 4GHz이며, 이는 1초에 40억 가지 계산을 처리할 수 있다는 뜻이다.

40억 분에 1초 동안 전자가 이동할 수 있는 거리는 75mm뿐인데, CPU는 한 면이 18mm이다. 따라서 겨우 CPU를 두 번 왕복할 정도인데, 앞으로 CPU가 더 줄일 수 있다면 성능은 더 높아질 것이다.

하지만 현대 컴퓨터 칩 내부의 선은 서로 몇 nm 단위로 떨어져 있다. 이 때문에 crosstalk(누화) 효과가 발생할 수 있으며, 이를 방지하기 위해 더 높은 noise immunity를 갖는 디지털 회로를 사용해야 한다.(판정 기준이 필요하다.)

---

## 2.1 analog to digital

여러 엔지니어 및 과학자가 transfer function(전이 함수)를 어떻게 변환할지 고민했다.

아래는 카메라 센서의 transfer function 그래프다.

![전이 함수 그래프 예제](images/transfer_function_ex.png)

- x축: input. 광량

- y축: output. 센서가 기록한 빛

그래프를 보면 광량이 많을수록 이미지 노출이 과해지고, 적을수록 이미지 노출이 부족해지는 것을 알 수 있다. 그렇기 때문에 목표는 그래프의 **직선부**에 많이 닿게 해야 한다.

예를 들어 카메라는 주로 셔터 속도나 조리개를 조절하는 장치를 만들어서 그래프의 직선부에 해당하는 결과를 얻을 수 있게 조절한다. 

> 비슷하게 스피커나 이어폰에 들어 있는 amplifier circuit(증폭 회로)도 이런 트릭에 속한다.

- gain 조절

![증폭 전이 함수에서 게인의 효과](images/amplifier_gain.png)

볼륨과 같은 장치는 **gain**을 조절한다. gain은 곡선의 기울기 가파름를 조절한다. 하지만 gain을 너무 크게 조절하면 output이 더 이상 input과 비슷한 모양을 유지하지 못한다.

![증폭기 클리핑](images/amplifier_clipping.png)

이처럼 **threshold**를 넘어버리면 output의 모양이 **distortion**(왜곡)된다. 그런데, 이 <U>distortion을 잘 이용하면, continuous한 신호를 discrete한 신호로 바꿀 수 있다.</U>

---

## 2.1.1 bit와 distortion

10진법보다 bit를 사용하면 바로 이런 하드웨어를 구현하는 것에서 이점을 얻을 수 있다.

![10진 문턱값과 2진 문턱값](images/threshold_hardware.png)

10진법 즉, 10가지 threshold를 이용해서 transfer function을 구분할 간단한 방법은 존재하지 않는다. 하지만 2진법이라면 훨씬 간단하게 이를 구현할 수 있다.

참고로 이렇게 활용하는 function의 하단부 영역을 **cutoff**라고 하며, 상단부 영역을 **saturation**이라고 한다.

---

## 2.2 논리 연산 구현

연산 규칙은 밸브를 생각하면 구현이 어떻게 되는지 이해하기 쉽다. 실제로 gate 밸브가 손잡이를 이용해 밸브를 열고 닫듯이, 회로에서도 이런 절차가 일어난다.

이 밸브를 두 개 연결한다고 하자. 밸브는 직렬(series)이나 병렬(parallel)로 연결할 수 있다.

- AND: 밸브 두 개의 series connection

  - 두 밸브가 모두 열려야 true가 된다.

![밸브 AND](images/valve_and.png)

- OR: 밸브 두 개의 parallel connection

  - 두 밸브 중 하나만 열려도 true가 된다.

![밸브 OR](images/valve_or.png)

> 밸브에서 gate를 열고 물이 전달되는 데까지 시간이 걸리는 것처럼, 전기도 마찬가지다. 이런 효과를 **propagation delay**(전파 지연)이라고 한다.

> 온도나 여러 조건에 따라 흐름이 결정된다.

resistance(저항)도 비슷하게 묘사할 수 있다.

![저항](images/valve_ohm's_raw.png)

처음에는 전기 회로에서 이런 gate를 조절하는 역할은 대체로 코일이 담당했다.(아래와 같은 구조를 릴레이라고 한다.) 코일에 전력이 유도되면서, 스위치가 동작하게 된다.

![코일과 게이트](images/relay_coil.png)

또한 이런 점을 이용해서 NOT 연산을 구현할 수도 있다.

> 이런 구현 방식은 접점에 이물질이 있으면 오류가 발생하기도 했다. 실제로 나방이 끼면서 작동하지 않은 것에서 bug라는 단어가 탄생했다.

> 또한 코일의 전원을 갑자기 차단하면 순간적으로 초고압이 발생하기 때문에, 공기에도 전기가 통할 수 있다. 이런 현상이 마모를 일으키기도 한다.

그 다음에는 진공관을 이용했으며, 더 나아가서 익숙한 트랜지스터가 탄생한다. 하지만 트랜지스터를 사용해도 AND 함수 같은 간단한 회로를 만들 때조차 부품이 너무 많이 필요했다.

> transistor(트랜지스터)는 transfer resistor(전송 저항)을 줄인 말에서 탄생했다. semiconductor(반도체)를 사용해서 만든다.

> 가장 보편적이며 중요한 트랜지스터는 BJT(Bipolar junction transistor)와 FET(Field effect transistor)다.

그 다음으로 나온 것이 바로 **intergrated circuit**(집적 회로)이며, 복잡한 시스템을 트랜지스터 하나를 만드는 정도의 비용으로 만들 수 있다.

---

## 2.3 논리 게이트

아래는 논리 게이트를 표현하는 기호이다. 2-input gate를 중점으로 설명한다.

![게이트 스키매틱 기호](images/gate_schematic.png)

inverter(인버터) 기호의 ○를 유의하자. ○가 없는 경우 buffer(버퍼)에 해당되며, buffer는 단지 input을 output으로 전달하기만 한다. 반면 inverter는 input을 반전시켜 output으로 전달한다.

> ○는 출력을 반전시킨다는 의미이다.

그런데 AND나 OR 게이트가 가장 효율적이지는 않다. TTL(transistor-transistor logic, 트랜지스터-트랜지스터 논리)든 CMOS(complementary metal–oxide–semiconductor)든 논리 게이트에서 가장 단순한 회로는 **NAND**(not and)나 **NOR**(not or)이다.

- NAND, NOR은 트랜지스터는 2개(TTL)나 4개(CMOS) 사용한다.(아래 그림이 CMOS로 구성한 것이다.)

![CMOS NAND, NOR](images/cmos_NAND_NOR.jpeg)

- AND, OR은 NAND와 NOR 회로 출력에 트랜지스터를 덧붙여 output을 반전시켜야 한다. (따라서 속도도 더 느리며, 전력도 더 많이 소모된다.)

![NAND와 NOR 게이트](images/gate_schematic_NAND_NOR.png)

이런 특성 때문에 AND와 OR에 ○를 붙여서 위처럼 기호를 표현한다. 또한 이들은 AND나 OR보다 표현할 수 있는 논리가 더 다양하다.

> 논리로는 NAND를 $\lnot(x \land y)$ , NOR를 $\lnot(x \lor y)$ 로 표현하기도 한다. 집합으로는 각각 $(A \cap B)^C$ , $(A \cup B)^C$ 로 표현할 수 있다.

- NAND와 NOR은 **같은 input을 두 개 받아서 inverter**로 만들 수 있다.

   - a NAND a, a NOR a는 NOT a와 같다.

![NAND, NOR 진리표](images/truth_table_NAND_NOR.png)

또한 드모르간의 법칙에 따라 inverter와 NAND 또는 NOR의 조합으로, AND와 OR 게이트를 표현할 수 있다.

- 모두 output에 인버터를 달면 각각 AND와 OR 게이트가 만들어 진다.

- NAND $\lnot(x \land y) = \lnot x \lor \lnot y$ : 두 input이 들어가는 부분에 각각 인버터를 달아주면 OR 게이트, 즉 $x \lor y$ 로 만들 수 있다. 

- NOR $\lnot(x \lor y) = \lnot x \land \lnot y$ : 두 input이 들어가는 부분에 각각 인버터를 달아주면 AND 게이트, 즉 $x \land y$ 로 만들 수 있다. 

> NAND와 NOR 모두 inverter로 만들 수 있었으므로, 결국 이 모든 논리를 NAND와 NOR로 표현한 것이다.

---

## 2.4 신호의 glitch와 hysteresis

앞서 distortion을 이용해서 아날로그 신호를 디지털 신호로 변환한다고 설명했다. 하지만 현실에서는 신호의 변환이 빠르게 진행되지 못하면서, noise(잡음)에 의해 threshold를 여러 번 오락가락할 수 있다.

![잡음 글리치](images/noise_glitch.png)

그림의 왼쪽은 noise가 없는 신호, 우측은 noise가 있는 신호다. 신호가 천천히 변하면서 noise에 의해 threshold를 여러 번 오가고 있다. 이 때문에 **glitch**가 생긴다.

이런 glitch를 **hysteresis**(이력 현상)으로 방지할 수 있다. 신호의 상태 변화에 따라 다른 threshold를 사용하는 것이다.

![hysteresis transfer function](images/hysteresis.png)

위 예시 그림은 올라가는 신호(0 to 1)와 내려가는 신호(1 to 0)이 서로 다른 transfer function을 쓰고 있다.

이에 따라 한번 threshold를 넘어가면, 다시 반전시키기 위해 더 많은 변화가 필요하게 된다. 즉, noise에 내성이 생기게 된다.

> 참고로 이 hysteresis를 사용하는 게이트가 있다. Schmitt trigger(슈미트 트리거)인데, 일반적인 게이트보다 더 복잡하고 비싸기 때문에 꼭 필요한 경우에만 사용한다.

참고로 0 to 1에서의 threshold를 **positive-going threshold**라고 하고, 1 to 0을 **negative-going threshold**라고 한다.

---

## 2.4.1 differential signalling

하지만 이런 hysteresis로도 threshold를 넘나드는 noise가 너무 많아 방지하기 힘든 경우가 있다. 이 때 전송선로를 2개로 만들어서, 서로 반대(**complementary**)로 흐르는 신호를 함께 이용하는 **differential signalling**(차동 신호) 방법을 적용할 수 있다.

> 벨이 발명한 twisted-pair cabling(연선 케이블링)도 노면전차가 만들어 내는 noise의 영향을 줄이기 위해, differential signalling을 적용한 것이다. 한 쌍의 선을 서로 꼬아서 만들었다.

> 현실에서도 USB, Ethernet 케이블(랜선), SATA(디스크 드라이브 인터페이스), HDMI, SD카드 방식인 UHS-II 등 다양한 곳에서 적용한다.(주로 고속 통신에 이용) 

서로 complementary한 신호가 흐를 때도, 전자기적 간섭은 두 선에 모두 비슷하게 noise를 발생시키므로, 이 두 신호의 차이값을 구하면 noise를 상당 부분 제거할 수 있다.

![차동 신호](images/differential_signalling.png)

위 그림은 input을 complementary outputs으로 변환하는 **driver**(드라이버)와 이런 두 신호를 받아서 다시 단일 신호로 만들어 주는 **receiver**(리시버)를 표현한 것이다.

> driver는 내부에서 Differential amplifier(차동 증폭기)를 이용해서 complementary한 신호를 만들어낼 수 있다.

![differential line driver](images/differential_line_driver.png)

아래 사진은 연선 이더넷 케이블이다.

![연선 이더넷 케이블](images/twisted-pair_ethernet_cable.png)

---

## 2.5 gate를 조합한 여러 회로

input이 2개인 NAND gate 하나를 설계하려면 원래 대략 10가지 부품이 필요했다. 하지만 **SSI**(small-scale integration, 소규모 집적 회로) 중 7400 IC라는 패키지는 네 가지 NAND gate를 제공하므로, 한 번에 부품 40개를 대신할 수 있는 것이다.

이처럼 SSI gate를 이용하면 다양한 회로를 만들 수 있다. 특히 자주 사용되는 조합은 **MSI**(medium-scale integration, 중간 규모 집적 회로)로도 제공한다. 뿐만 아니라 LSI(large-scale integration, 대규모 집적 회로), VLSI(very large-scale integration, 초대규모 집적 회로) 등도 있다.

---

### 2.5.1 adder

two's complement adder를 구성할 것이다. 이 adder는 다음과 같은 특성을 가지고 있었다.

![adder 진리표](images/adder.png)

- 두 bit의 합(Sum)은 각 bit의 XOR과 같다.

- 올림(Carry)은 두 bit의 AND와 같다.

간단히 두 연산만으로 구성한 adder는 다음과 같다.

![반가산기](images/half_adder.png)

이를 **half adder**(반가산기)라고 한다. 두 bit의 덧셈은 잘 수행할 수 있겠지만, 이 범위를 넘어가게 되면 또 다른 input이 필요하다.

아래는 세 bit를 처리하는 **full adder** 회로다.

![전가산기](images/full_adder.png)

- sum은 순서대로 XOR을 적용한다.

- carry가 한 차례라도 발생했으면 1을 내놓는다.

이제 이런 full adder를 이용해서 몇 자리 수의 bit 계산도 수행할 수 있다. 아래는 그 예시인 **ripple-carry adder**(리플 자리올림 가산기) 그림이다.

![리플 자리올림 가산기](images/ripple_carry_adder.png)

하지만 순차적인 계산 진행 때문에, 출력 시간 지연이 발생하게 된다. 특히 32bit나 62bit 계산을 한다고 가정하며 이런 지연 시간은 크게 늘어날 것이다. 

이런 문제점을 개선한 회로가 **carry look-ahead adder**(올림 예측 가산기)이다.

재귀식처럼 계산되는 부분을 최대한 합치는 것으로, AND와 OR 등 gate 는 더 늘어나지만 지연 시간은 줄어들게 된다. gate 자체의 값은 저렴하지만 전력은 더 소모하게 된다. 

> 어느 정도 계산 속도와 전력 소모 간의 trade-off가 성립하는 셈이다.

---

### 2.5.2 decoder

decoder는 encoding된 수를 다시 개별 bit 집합으로 만든다. 예를 들어 octal representation(8진 표현법)으로 encoding된 수를 다시 단일 bit들로 되돌리는 연산을 수행한다.

원래 8진 표현법은 3개씩 그룹으로 묶어서 encoding했다. 아래는 이를 반대로 수행하는 3:8 decoder이다. 각 자리 수가 0~7이므로 3bit, 즉 한 자리수를 계산하기 위해 input은 3개가 필요하다.

![3:8 디코더](images/38_decoder.png)

- input은 3개, output은 8개가 된다.

- input이 000이면 $Y_{0}$ 이 true가 된다.

- input이 001이면 $Y_{1}$ 이 true가 된다.

- ...

아래는 이 3:8 decoder의 schematic 기호다.

![3:8 decoder schematic](images/38_decoder_schematic.png)

---

### 2.5.3 demultiplexer

decoder를 이용해서 **demultiplexer**(디멀티플렉서)를 만들 수 있다. 주로 **demux**(디먹스)로 줄여 부르며, <U>input을 몇 가지 output 중 하나로 전달</U>하는 기능을 수행한다.

![1:4 demux](images/14_demux.png)

- $D$ : input signal

- $S_{0}$ , $S_{1}$ : decoder input

위는 1:4 demux이다. decoder input $S_{0}$ , $S_{1}$ 에 따라, $D$ 를 $Y_{0}$ 에서 $Y_{3}$ 중 하나로 전달한다.

아래는 1:4 demux의 schematic 기호다.

![1:4 demux schematic](images/14_demux_schematic.png)

---

### 2.5.4 selector(multiplexer)

**selector**(**multiplexer**)는 여러 input 중에서 하나의 input을 select하는 기능을 수행한다. 주로 **mux**로 줄여 부른다.

decoder에 gate를 추가하는 방식으로 구현할 수 있다. 아래는 4:1 mux 회로이다.

![4:1 selector](images/41_mux.png)

아래는 이 mux의 schematic 기호이다.

![4:1 selector schematic](images/41_mux_schematic.png)

---