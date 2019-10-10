---
layout:     post
title:      Traitement du signal aléatoire(随机信号处理)
subtitle:   ISAE-SUPAERO课程
date:       2019-10-10
author:     TC YU
header-img: img/signal_et_system_page.jpg
catalog: true
tags:
    - Signal aléatoire
    - 随机信号
---

# Traitement du signal aléatoire 随机信号处理

## 1.Concepts généraux sur les processus et séquences aléatoires

**Notions abordées**

- Définition et exemples
- Spécification statistique : exhaustive et partielle via les moments (fonction moyenne et fonction de corrélation)
- Caractérisation d'un processus ou d'un couple de processus (stationnarité/ergodicité, orthogonalité, indépendance, décorrélation)*[平稳性/遍历，正交性，独立性，去相关]*


**Compétences visées**
 ··
- Identifier les processus aléatoires **(RP)** comme un outil pour la modélisation de signaux non-déterministes*[非确定性信号]*
- Définir mathématiquement ce qu'est un RP
- Donner des exemples de RP pouvant modéliser des signaux non-déterministes
- Définir et utiliser les outils permettant de spécifier statistiquement un RP et un couple de RP
	- de manière exhaustive avec les PDF et CDF à l'ordre n \in\mathbb{N} 
	- de manière partielle avec les moments du premier et second ordre
- Définir et utiliser les outils permettant de caractériser un processus aléatoire comme étant
	- stationnaire au sens strict **(SSS)**  et au sens large **(WSS)** *[严平稳过程和宽平稳过程]*
	- ergodique en moyenne
- Définir et utiliser les outils permettant la caractérisation d'un couple de RP comme étant
	- conjointement WSS
	- indépendants
	- décorrélés
	- orthogonaux
- Identifier la fonction d'autocorrélation **ACF** (et d'intercorrélation **CCF** resp.) comme une mesure de ressemblance
- Calculer et interpréter*[解释]* des ACF et CCF

$(\Omega,T,P )$
$\Omega$[样本空间] espace fondamental(ensemble des événements élémentaire) 
$T$[事件域] mesure des evenements defins sur $\Omega$
$P$[概率] mesure de probabilité ou probabilité aléaroire sur cet espace

![](https://raw.githubusercontent.com/lialittis/lialittis.github.io/master/img/signal%20aleatoire1.png)

```
graph TD
	A[随机现象]  --> B[随机试验]
	B --> C[概率空间]
	C --> D[随机变量]
```

条件概率复习：乘法公式；全概率公式；Bayes公式；
独立：相互独立=>两两独立；
随机变量：将样本空间种各个样本点对应到实数轴上，产生随机变量（分析方式：分布函数或分布律，概率密度，数学特征函数和各阶矩阵）；

### 1.1Fonctions de répqrtition et densité de probabilité[分布函数和概率密度]
(CDF cumulative distribution function)
(PDF probability density function)

**对于一个随机过程**
* 一阶
$F_X(x,t) = P(X(t)\leq x)$
$f_X(x,t) = \frac{\partial F_X}{\partial x}(x,t)$

deux paramètres:x,t.

### 1.2Moments

**1. Moyenne** 

$m_X(t) = E\{X(t)\}$

Lorsaue la moyenne est identiquement nulle, on parle de processus aléatoire centré.

考虑二维变量的独立性对二维变量的数学期望的影响。

条件数学期望，是指在给定X= x的条件下求Y的数学期望，计算积分时从qi概率密度入手。


从数学期望引出矩的概念：
原点矩：数学期望，均方值，互相关
中心矩：方差，协方差

**2. Fonction d'autocorrélation**

$$R_X(t_1,t_2) = E\{X(t_1)X^*(t_2)\} = \int_{-\infty}^{\infty}\int_{-\infty}^{\infty}x_1x_2^*f_x(x_1,x_2^*t_1,t_2)dx_1dx_2^*$$

当时间相同时，它也表示为在相应时间的puissance
$P_X(t) = R_X(t,t) = E\{|X(t)|^2\}$

**3. Fonction d'autocovariance** 

Autrement dit, la fonction d'autocovariance est définie comme la fonction d'autocorrélation du processus centré.

$C_X(t_1,t_2) = R_X(t_1,t_2) + m_X(t_1)m_X^*(t_2)$

c'est **symétrie hermitienne** pour Cx(t1,t2) et Rx(t1,t2).

**4. Variance d'un processus aléatoire**

La variance correspond donc à la puissance du processus centré.

$\sigma^2_X(t) = P_X(t) - |m_X(t)|^2$

**5.Coefficient de correlation**

La coefficient de corrélation quantifie le degré de dépendance linéaire entre X(t1) et X(t2);
1 => lineairement dependant, 0 => décorrélé



#### Certaines choses importantes

Pour un exemple Tension délivrée par un oscillateur, la fonction d'autocorrélation ne dépend que de $*tau = t_1-t_2$; la variance du processus est indépendante du temps t.



**6. Pour un couple de processus**

Fonction d'intercorrelation(CCF Cross-correlation function)

Processus orthogonaux
如果两个随机过程的互相关等于零的话，我们可以认为他们是正交过程

Fonction d'intercovariance

Processus décorrélés
不相关的条件是，协方差为零。此时，他们的互相关方程等于两者的期望的共轭乘积。

###1.3 Stationnarité

** 1.Processus stationnaire au sens strict**
(SSS Strict sense stationary random process)严平稳过程
Un processus aléatoire X est stationnaire au sens strict si sa fonction de répartition à l'ordre n est égale à celle du signal aléatoire translaté X(t+T) pour tout T et pour tout $$$ n \geq 1$$$.

* Ordre 1: la moyenne d'un processus stationnaire au sens strict est constante.
* Ordre 2: la fonction d'autocorrélation d'un processus stationnaire au sens strict dépend uniquement de la différence de temps $$$ \tau = t_1 - t_2 $$$. Dans ce cas on peut redéfinir la fonction d'autocorrélation comme une fonction d'un seul paramètre.
# why?

**2. Process stationnaire au sens large ou au second ordre**
(WSS Wide sense stationatry process)宽平稳过程
Un processus stochastique est tationnaire au sens large ou au second ordre si sa moyenne est constante et si sa fonction d'autocorrélation est indépendante de l'origine des temps.

**3. Processus conjointement stationnaires au sens large**

### 1.4 Ergodicité 遍历
Dans tous les cas, la notion d'ergodicité n'a de sens que si l'on suppose le processus au moins stationnaire au sens large.

**1. Moyenne et fonction d'auto-corrélation temporelle**









### Bilan 
1. Comment définit-on un processus aléatoire ?
2. Comment spécifie-t-on statisquement un processus aléatoire
	a. entièrement ?
	b. partiellement ?
3. Comment sont définis les moments à l'ordre 1 et 2
	a. d'une variable aléatoire
	b. d'un processus aléatoire
4. Est-ce que la puissance d'un processus est égale à sa variance ?
5. Quelle est la différence entre la stationnarité au sens strict et la stationnairité au sens large ?
6. QUi implique qui (=>,<=)?
	stationnarité au sens strict	stationnarité au sens large
	stationnatité au second ordre	stationnarité au sens large
	décorrélation					indépendance
7. Relier les caractéristiques d'un (ou deux) processus avec l'outil mathématique qui permet de le définir. Préciser alore son utilisation.
	stationnarité au sens strict	FONCTION D'INTERCORRELATION
	stationnarité qu second ordre	FONCTION D'INTERCOVARIANCE
	orthofonalité	FONCTIONS DE REPARTITION
	décorrélation	MONMENTS A L'ORDRE 1 ET 2
	indépendance
8. Comment mesurer en pratique la moyenne statistique d'un processus aléatoire ergodique par rapport à sa moyenne ?



















































