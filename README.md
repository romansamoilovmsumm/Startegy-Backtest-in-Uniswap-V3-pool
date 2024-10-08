# Startegy Backtest in Uniswap V3 pool
This is a repository dedicated to the development and testing of liquidity placement strategies in uniswap v3

В проекте требовалось выбрать Uniswap V3 pool, а затем написать стратегию и сделать её бектест на основании исторических данных.

Начнем со стратегии: в начальный момент времени мы ждем какой-то промежуток времени и затем задаем пул ликвидности (механизм выбора размера ликвидности будет пояснен позже), промежутки цены которого будут зависеть от стреднеквадратичного отклонения за некоторый промежуток исторических данных (понятное дело, до того дня, на котором сейчас находится "стратегия"), когда цена выходит за этот промежуток, то мы пересоздаем пул ликвидности и таким образом можно оптимально создавать пулы, чтобы наша ликвидность была самая востребованная и как следствие максимизировали fees. То есть наша стратегия в некоторой пропорции держит средства в пуле и некоторые средства хэджирует при помощи фьючерса. Для оптимального способа оптимизации я применил дельта-хеджирование, причем в Uniswap V3 оно записывает достаточно просто. ко всему прочему перебалансировка (пропорций средств в хэдже и в пуле) происходит выходе цены за некоторый диапозон

Таким образом у моей стратегии теперь есть некоторые начальные параметры, (стандартное мат. отклонение для концентрированной ликвидности (то есть влияет на тот промежуток, с которым создается пул), отклонение цены (влияет на то, при каких колебаниях происходит перебалансировка), разбалансировка внутри пула (техническая штука))

Для бектеста я сначала использовал исторические данные и по понятным причинам результаты могли быть слишком "переученной", поэтому я моделлировал цену через геометрическо-броуновское движение. Тогда, положим, у нас есть 100 разных траекториях, мы прогоняем нашу стратегию на конкретных параметрах и записываем доходность на каждой из траектории, а затем используем функцию полезности, далее я процитирую себя же из моего Jupyter ноутбука


Каким образом нам подбирать требуемые значения? Для этого предлагаю сгенерировать 100 траекторий, а затем оценить доходность на них, каким же образом мы будем оценивать доходности?

На самом деле вопрос абсолютно не тривиальный, так как положим у нас есть траектория которая даёт доходность 0.5, а другая 10, а другая стратегия даёт выгоду 1 и 1.2, как тогда определить наилучший?

Положим у нас есть доходности: $[a_1, ..., a_n]$, будто бы разумно сказать, что это некоторая выборка случайной величины $e^{\xi(\theta)}$, тогда очевидно нам хотелось бы чтобы наша $\xi(\theta)$ имела маленькую дисперсию и желательно положительное значение, я пока не изучал то, как задаются функции полезности для стратегий, поэтому строгого доказательства такой "оптимизации" не будет, поскольку она исходит из моих математических интуиций.

Таким образом по итогу получилось улучшить доходность стратегии вдвое, далее идут сценарные моделирования при разных коэффициетах сноса и разных $\sigma$. В качестве иллюстраций того, как работает стратегия я вывожу гистограмму, где в качестве доходностей указана доходность конкретной стратегии, сначала показываю доходности стратегии, где максимизировалось матожидание, затем, где минимизировалась дисперсия, а потом изначальную стратегию
