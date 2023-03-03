# Многопоточное приложение и параллельные вычисления

## Эксперементные вещи
#### Запуск потоков через цикл

Идея: хотел все потоки, выполняющую программу, засунуть в один массив, а потом последовательно вызвать их с помощью цикла (этой идеи я придерживался основное время разработки программы)

![img.png](ImagesForMd/img.png)

НО: что-то пошло не так, и программа начала кидать ошибку, вылетать при количестве монахов > 4.

Пробовал вставить мьютексы - не помогло (их можео увидеть в программе)

В итоге позже пришла гениальная идея, которая сделала выполнение программы на ура при любом количестве монахов. А вот какую именно, Вы можете посмотреть ниже.

#### Реализация с помощью ООП

Чтобы не путаться и долго не искать ошибки, я решил реализовать по началу программу с помощью ООП (задатки Вы можете увидеть в классе "Monah"), но в процессе работы с pthread выяснилось, что функции, которые передаются в потоки должны быть статическими, что в целом логично. И, соответственно, пришлось делать большинство членов класса статическими.

Фуннции, которые пришлось делать статическими:

- Round
- GetRandomNum
- MakePairs
- GetWinner

Данные, которые пришлось делать статическими:

- std::vector<Monah> monahs
- int size_

Поэтому лишний класс я решил удалить, и все эти члены вынести на уровень main.

## Заметки
- Программа понимает, что надо импортирвать данные с файла по заданному пути и загрузить её вывод в выходной файл или сгенерировать, если в аргументах консоли были введены соответсвующие пути к файлам через пробел или 'r', соответственно.
- В входных файлах все данные надо указывать через \n


## Условия задачи

Задача о Пути Кулака. 

На седых склонах Гималаев стоит древний буддистский монастырь: Гуань-Инь-Янь. Каждый год в день сошествия на землю боддисатвы Монахи монастыря собираются на совместное празднество и
показывают свое совершенствование на Пути Кулака. Всех соревнующихся
монахов разбивают на пары, победители пар бьются затем между собой и так
далее, до финального поединка. Монах который победил в финальном бою,
забирает себе на хранение статую боддисатвы. Реализовать многопоточное
приложение, определяющего победителя. В качестве входных данных используется массив, в котором хранится количество энергии Ци каждого монаха. При победе монах забирает энергию Ци своего противника. Разбивка на
пары перед каждым сражением осуществляется случайным образом. Монах,
оставшийся без пары, удваивает свою энергию, отдохнув от поединка. При
решении использовать принцип дихотомии.

## Описание модели параллельных вычислений, используемой при разработке многопоточной программы

При разработки многопоточной программы "Задача о Пути Кулака" использовался принцип дихотомии, подразумевающий разделение программы на потоки в виде дерева. 

В данном случае на вход подаётся множества листьев - Монахи, а на выход подаётся корень задачи - Монах, забравший себе статую боддиставы.

## Программа на С++

#### Подключение необходимых файлов и библиотек

![img_1.png](ImagesForMd/img_1.png)

- Библиотека fstream нужна для работы с файлами
- Заголовок unistd.h нужен для задержки времени, которая в свою очередь необходима для получения случайного, неповторяющегося числа
- файл Monah.cpp нужен для дальнейшей работы программы

#### Инициализация статических переменных

![img_2.png](ImagesForMd/img_2.png)

- В первой строчке объявляем мьютексы для того, чтобы последовательно закрепллять их выполнения за текущим потоком среди потоков
- monahs нужен для хранения и управления монахами
- size_ нужен для хранения текущего размера monahs

#### Разработка необходимых функций
- AddMonah для добавления монаха в monahs

![img_3.png](ImagesForMd/img_3.png)

- GetRandomNum - для получения случайного числа из диапозона (при генерации пары для вершины в методе MakePairs)

![img_4.png](ImagesForMd/img_4.png)

- GetRandomSize - для генерации случайного числа в режиме "r"

![img_5.png](ImagesForMd/img_5.png)

- GetRandomCi - для генерации случайного размера ци в режиме "r"

![img_6.png](ImagesForMd/img_6.png)

- MakePairs - для генерации пар случайных вершин, использующиеся в проведении битв между монахами

![img_7.png](ImagesForMd/img_7.png)

- GetWinner - для получения победителя в парной схватке

![img_8.png](ImagesForMd/img_8.png)

- Round - для проведения очередного раунда

![img_9.png](ImagesForMd/img_9.png)

![img_10.png](ImagesForMd/img_10.png)

- main - для запуска программы и сборки всех разработанных функций воедино

![img_11.png](ImagesForMd/img_11.png)

![img_12.png](ImagesForMd/img_12.png)

![img_13.png](ImagesForMd/img_13.png)

#### Разработка класса Monah
**private data**
- Данные для хранения (ци монаха)
![img.png](ImagesForMd/img11.png)
- Функция для выяснения является ли ци, переданное через конструктор, корректным.
![img_1.png](ImagesForMd/img_18.png)

Ограничился INT_MAX так, как нужно ещё как-то удваивать ци монаха. В плане, если максимальным значением было бы uint64_t_MAX, то ци могло стать меньше, чем было из-за макс числа uint64_t.

**public data**
- Конструктор с ци монаха

![img.png](ImagesForMd/img14.png)

- Получение ци монаха

![img_1.png](ImagesForMd/img_19.png)

- Увеличение ци монаха в 2 раза, если не нашлась для него пара в раунде

![img_2.png](ImagesForMd/img_20.png)

- Получение строкового представления класса Monah

![img_3.png](ImagesForMd/img_21.png)

- Перегрузка операции << для вывода класса Monah

![img_4.png](ImagesForMd/img_22.png)

- Перегрузка операции == для сравнения двух монахов

![img_5.png](ImagesForMd/img_23.png)


#### Поддержка случайного ввода данных

![img.png](ImagesForMd/img3.png)

![img_1.png](ImagesForMd/img_30.png)

#### Файловый ввод/вывод
- Инициализация файлов

![img.png](ImagesForMd/img4.png)

- Чтение данных + закрытие входного файла

![img_1.png](ImagesForMd/11.png)

- Запись данных + закрытие выходного файла

![img_2.png](ImagesForMd/36.png)

![img_3.png](ImagesForMd/1.png)

![img_4.png](ImagesForMd/4.png)

![img_5.png](ImagesForMd/7.png)

![img_6.png](ImagesForMd/8.png)

![img_7.png](ImagesForMd/9.png)

#### Комментирование кода

![img.png](ImagesForMd/90.png)

- GetRandomNum

![img_1.png](ImagesForMd/0.png)

- GetRandomSize

![img_2.png](ImagesForMd/214.png)

- GetRandomCi

![img_3.png](ImagesForMd/32.png)

- MakePairs

![img_4.png](ImagesForMd/45.png)

![img_5.png](ImagesForMd/40.png)

- Round

![img_6.png](ImagesForMd/41.png)

- main

![img_7.png](ImagesForMd/21.png)

## Ограничение по входным данным
- кол-во монахов должно быть > 1 и < 2147483648, но свыше 10 монахов программа будет выполняться сликом долго (дольше 20 секунд)
- ci монаха должно быть > 0 и < 2147483648

## Разработка тестов для программы
####  Проверка выполнения работоспособности
- Входные данные: tests/Console/1/1.in
- Выходные данные: tests/Console/1/1.out

#### Большие потоки
- Входные данные: tests/Console/2/2.in
- Выходные данные: tests/Console/2/2.out

#### Рандомные числа
- Входные данные: "r" в командной строке
- Выходные данные: Monah with ci = ... won

#### Файловый ввод/вывод
- Входные данные: "inputPath outputPath" в командной строке
- Содержимое файлов можете увидеть tests/file/i.txt
- Вывод в файл: tests/file/o.txt

## Тестирование программы
#### Проверка выполнения работоспособности

![img.png](ImagesForMd/78.png)

=> ok

#### Большие потоки

![img_1.png](ImagesForMd/m.png)

![img_2.png](ImagesForMd/w.png)

![img_3.png](ImagesForMd/r.png) 

=> ok

#### Рандомные числа

![img_4.png](ImagesForMd/i.png)

![img_5.png](ImagesForMd/l.png)

=> ok

#### Файловый ввод/вывод

![img_6.png](ImagesForMd/y.png)

![img_7.png](ImagesForMd/k.png)
