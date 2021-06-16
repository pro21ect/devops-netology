1. Искомый вызов согласно задаче chdir("/tmp"). Использование вызова позволяет изменить каталог процесса напрямую.

2. В результате выполнения команды strace /bin/bash -c 'file /dev/tty' получен ответ /usr/share/misc/magic.mgc

3. Итак запукаю процесс записи в файл
- cat /dev/urandom >> test
-   Выясняю PID процесса
- ps aux | grep cat
-   удаляю файл
- rm test
- обнуляю дескриптор
- cat /proc/2927/fd/3 > /dev/null

4. Зомби процессы не занимают ресурсы перечисленные в вопросе. Они - это запись в таблице процессов.

5. Просматривал командой strace -o log.txt /usr/sbin/opensnoop-bpfcc дальше в файле поиском. 
- /usr/lib/python3.8/lib-dynload
- include/linux/string.h
- include/uapi/linux/string.h

6.  Основная работа программа выполняется строкой uname({sysname="Linux", nodename="vagrant", ...}) = 0 
-  Часть информации может быть получена по следующим адресам /proc/sys/kernel/ {ostype, hostname, osrelease, version, domainname}. 

7. ";" - последовательное выполнение команд
-   "&&" - последовательное выполнение команд обусловленное кодом возврата от предыдущей команды.
- При ошибке следующая команда не выполняется
- set -e - не совсем аналог &&, команда останавливает выполнение скрипта при коде возврата больше 0, т.е. команды в скрипте после ошибки выполнены не будут.

8. set -euxo pipefail использовать хорошо, потому что команда позволяет отлавливать ошибки в сценариях.
- e опция прекращает работу скрипта при ошибке.
- u влияет на переменные, при обращении к не существующей переменной будет выдавать ошибку и выводить название не правильной переменной
- x выводит выполняемые команды в stdout перед выполненинем, полезно для отладки
- o pipefail - прекращает выполнение скрипта, даже если одна из частей пайпа завершилась ошибкой. В этом случае bash-скрипт завершит выполнение, 
- если mycommand вернёт ошибку, не смотря на true в конце пайплайна: mycommand | true.  

9. S - interruptible sleep - самое часто встречающийся статус. Это спящие процессы, ожидающие какого-либо события.
Дополнительные значки обозначают:
- < высокий приоритет, отъедает ресурсы у других пользователей
- N низкий приоритет 
- L процесс имеет страницы, заблокированные в памяти (для ввода-вывода в реальном времени и пользовательского ввода-вывода)
- s является лидером сессии
- l многопоточен
- + приоритетный процесс