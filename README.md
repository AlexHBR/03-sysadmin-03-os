# Домашнее задание к занятию "3.3. Операционные системы, лекция 1"

1.Какой системный вызов делает команда `cd`? В прошлом ДЗ мы выяснили, что `cd` не является самостоятельной  программой, это `shell builtin`, поэтому запустить `strace` непосредственно на `cd` не получится. Тем не менее, вы можете запустить `strace` на `/bin/bash -c 'cd /tmp'`. В этом случае вы увидите полный список системных вызовов, которые делает сам `bash` при старте. Вам нужно найти тот единственный, который относится именно к `cd`.    
>chdir("/tmp")  

2. Попробуйте использовать команду `file` на объекты разных типов на файловой системе. Например:
    ```bash
    vagrant@netology1:~$ file /dev/tty
    /dev/tty: character special (5/0)
    vagrant@netology1:~$ file /dev/sda
    /dev/sda: block special (8/0)
    vagrant@netology1:~$ file /bin/bash
    /bin/bash: ELF 64-bit LSB shared object, x86-64
    ```
    Используя `strace` выясните, где находится база данных `file` на основании которой она делает свои догадки.

>openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3  
>база - /usr/share/misc/magic.mgc


3. Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удален (deleted в lsof), однако возможности сигналом сказать приложению переоткрыть файлы или просто перезапустить приложение – нет. Так как приложение продолжает писать в удаленный файл, место на диске постепенно заканчивается. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).
>vi правим отркрытый файл.    
>![img.png](img.png)    
![img_1.png](img_1.png)    

4. Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?   
>Зомби-процессы освобождает все свои ресурсы, но остается запись в таблице процессов  

5. В iovisor BCC есть утилита `opensnoop`:
    ```bash
    root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
    /usr/sbin/opensnoop-bpfcc
    ```
    На какие файлы вы увидели вызовы группы `open` за первую секунду работы утилиты? Воспользуйтесь пакетом `bpfcc-tools` для Ubuntu 20.04. Дополнительные [сведения по установке](https://github.com/iovisor/bcc/blob/master/INSTALL.md).  
>![img_2.png](img_2.png)

6. Какой системный вызов использует `uname -a`? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в `/proc`, где можно узнать версию ядра и релиз ОС.    
>![img_3.png](img_3.png)



7. Чем отличается последовательность команд через `;` и через `&&` в bash? Например:
    ```bash
    root@netology1:~# test -d /tmp/some_dir; echo Hi
    Hi
    root@netology1:~# test -d /tmp/some_dir && echo Hi
    root@netology1:~#
    ```
    Есть ли смысл использовать в bash `&&`, если применить `set -e`?

>&& -  условный оператор,   
;  - разделитель последовательных команд  
test -d /tmp/some_dir && echo Hi - в данном случае echo  отработает только при успешном заверщении команды test  
set -e - прерывает сессию при любом ненулевом значении исполняемых команд в конвеере кроме последней.  
в случае &&  вместе с set -e- не имеет смысла использовать, так как при ошибке , выполнение команд прекратиться.   

8. Из каких опций состоит режим bash `set -euxo pipefail` и почему его хорошо было бы использовать в сценариях?  
>-e - прекращает выполнение скрипта если команда завершилась ошибкой, выводит в stderr строку с ошибкой  
-u - прекращает выполнение скрипта, если встретилась несуществующая переменная  
-x - выводит выполняемые команды в stdout перед выполненинем  
-o pipefail - прекращает выполнение скрипта, даже если одна из частей пайпа завершилась ошибкой  
При таком запуске скрипт получается безопасным, происходит автоматическая обработка ошибок.   


9. Используя `-o stat` для `ps`, определите, какой наиболее часто встречающийся статус у процессов в системе. В `man ps` ознакомьтесь (`/PROCESS STATE CODES`) что значат дополнительные к основной заглавной буквы статуса процессов. Его можно не учитывать при расчете (считать S, Ss или Ssl равнозначными).  
>![img_4.png](img_4.png)  
> R - running и runnable (исполняется или ожидает исполнения);  
> S - interruptible sleep (обычный спящий процесс, который может быть прерван, ожидает какого-то события);  
![img_5.png](img_5.png)
 