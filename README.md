# Домашнее задание к занятию «Ветвления в Git»

Цель задания

В процессе работы над заданием вы потренируетесь делать merge и rebase. В результате вы поймете разницу между ними и научитесь решать конфликты.

### Задание «Ветвление, merge и rebase»

**Шаг 1.** Предположим, что есть задача — написать скрипт, выводящий на экран параметры его запуска. Давайте посмотрим, как будет отличаться работа над этим скриптом с использованием ветвления, merge и rebase.

Создайте в своём репозитории каталог branching и в нём два файла — merge.sh и rebase.sh — с содержимым:

`#!/bin/bash
`# display command line options

`count=1
`for param in "$*"; do
`    echo "\$* Parameter #$count = $param"
`    count=$(( $count + 1 ))
`done

Этот скрипт отображает на экране все параметры одной строкой, а не разделяет их.

Шаг 2. Создадим коммит с описанием prepare for merge and rebase и отправим его в ветку main.

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/1.jpg)

Подготовка файла merge.sh

**Шаг 1.** Создайте ветку git-merge.

**Шаг 2.** Замените в ней содержимое файла merge.sh на:

#!/bin/bash
# display command line options

count=1
for param in "$@"; do
    echo "\$@ Parameter #$count = $param"
    count=$(( $count + 1 ))
done

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/2.jpg)

**Шаг 3.** Создайте коммит merge: @ instead *, отправьте изменения в репозиторий.

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/3.jpg)

**Шаг 4.** Разработчик подумал и решил внести ещё одно изменение в merge.sh:

#!/bin/bash
# display command line options

count=1
while [[ -n "$1" ]]; do
    echo "Parameter #$count = $1"
    count=$(( $count + 1 ))
    shift
done

Теперь скрипт будет отображать каждый переданный ему параметр отдельно.

Шаг 5. Создайте коммит merge: use shift и отправьте изменения в репозиторий.

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/4.jpg)


**Изменим main**

**Шаг 1.** Вернитесь в ветку main. 

**Шаг 2.** Предположим, что пока мы работали над веткой git-merge, кто-то изменил main. Для этого изменим содержимое файла rebase.sh на:

#!/bin/bash
# display command line options

count=1
for param in "$@"; do
    echo "\$@ Parameter #$count = $param"
    count=$(( $count + 1 ))
done

echo "====="

В этом случае скрипт тоже будет отображать каждый параметр в новой строке.

**Шаг 3.** Отправляем изменённую ветку main в репозиторий.

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/5.jpg)


**Подготовка файла rebase.sh**

**Шаг 1.** Предположим, что теперь другой участник нашей команды не сделал git pull либо просто хотел ответвиться не от последнего коммита в main, а от коммита, когда мы только создали два файла merge.sh и rebase.sh на первом шаге.

Для этого при помощи команды git log найдём хеш коммита prepare for merge and rebase и выполним git checkout на него так: git checkout 8baf217e80ef17ff577883fda90f6487f67bbcea (хеш будет другой). 

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/6.jpg)

**Шаг 2.** Создадим ветку git-rebase, основываясь на текущем коммите. 

**Шаг 3.** И изменим содержимое файла rebase.sh на следующее, тоже починив скрипт, но немного в другом стиле:

#!/bin/bash
# display command line options

count=1
for param in "$@"; do
    echo "Parameter: $param"
    count=$(( $count + 1 ))
done

echo "====="

**Шаг 4.** Отправим эти изменения в ветку git-rebase с комментарием git-rebase 1.

**Шаг 5.** И сделаем ещё один коммит git-rebase 2 с пушем, заменив echo "Parameter: $param" на echo "Next parameter: $param".

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/7.jpg)

Промежуточный итог

Мы сэмулировали типичную ситуации в разработке кода, когда команда разработчиков работала над одним и тем же участком кода, и кто-то из разработчиков предпочитает делать merge, а кто-то — rebase. Конфликты с merge обычно решаются просто, а с rebase бывают сложности, поэтому давайте смержим все наработки в main и разрешим конфликты.

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/8.jpg)


**Merge**

Сливаем ветку git-merge в main и отправляем изменения в репозиторий, должно получиться без конфликтов:

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/9.jpg)

В результате получаем такую схему:

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/10.jpg)


**Rebase**

**Шаг 1.** Перед мержем ветки git-rebase выполним её rebase на main. Да, мы специально создали ситуацию с конфликтами, чтобы потренироваться их решать. Шаг 2. Переключаемся на ветку git-rebase и выполняем git rebase -i main. В открывшемся диалоге должно быть два выполненных коммита, давайте заодно объединим их в один, указав слева от нижнего fixup. В результате получаем:

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/11.jpg)

Если посмотреть содержимое файла rebase.sh, то увидим метки, оставленные Git для решения конфликта:

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/12.jpg)

**Шаг 3.** Удалим метки, отдав предпочтение варианту:

echo "\$@ Parameter #$count = $param"

**Шаг 4.** Сообщим Git, что конфликт решён git add rebase.sh и продолжим rebase git rebase --continue.

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/13.jpg)

**Шаг 5.** Опять получим конфликт в файле rebase.sh при попытке применения нашего второго коммита. Давайте разрешим конфликт, оставив строчку echo "Next parameter: $param".

**Шаг 6.** Далее опять сообщаем Git о том, что конфликт разрешён — git add rebase.sh — и продолжим rebase — git rebase --continue.

В результате будет открыт текстовый редактор, предлагающий написать комментарий к новому объединённому коммиту:

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/14.jpg)

После сохранения изменения Git сообщит:

Successfully rebased and updated refs/heads/git-rebase

**Шаг 7.** И попробуем выполнить git push либо git push -u origin git-rebase, чтобы точно указать, что и куда мы хотим запушить.

Эта команда завершится с ошибкой:

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/15.jpg)

Это произошло, потому что мы пытаемся перезаписать историю.

**Шаг 8.** Чтобы Git позволил нам это сделать, добавим флаг force:

**Шаг 9.** Теперь можно смержить ветку git-rebase в main без конфликтов и без дополнительного мерж-комита простой перемоткой:

![alt text](https://github.com/MaratKN/git-03-branching/blob/main/16.jpg)

В качестве результата работы по всем заданиям приложите ссылку на .md-файл в вашем репозитории.

https://github.com/MaratKN/git-03-branching/network

