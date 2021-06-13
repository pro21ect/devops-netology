1. Команда cd встроенная. Объясняется просто type cd. Результат cd is a shell builtin

2. Какая альтернатива без pipe команде grep <some_string> <some_file> | wc -l - команда подсчитывает число строк контекста
- альтернатива grep -c <some_string> <some_file>

3. Выясняется командой например pstree -Apn, либо ps -q 1 -o comm= PID1 - процесс systemd. Система инициализации.

4. Как будет выглядеть команда, которая перенаправит вывод stderr ls на другую сессию терминала?
- ls -lha 2>/dev/pts/1 

5. Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? Приведите работающий пример.
- Да возможно.
```
- Пример cat <toch >test1
```
6. Получится ли вывести находясь в графическом режиме данные из PTY в какой-либо из эмуляторов TTY? Сможете ли вы наблюдать выводимые данные?
- cat /dev/tty > /dev/pts/1

7. Выполните команду bash 5>&1. К чему она приведет? Что будет, если вы выполните echo netology > /proc/$$/fd/5? Почему так происходит?
- bash 5>&1  Создаёт файловый дескриптор 5, перенаправляет его в stdout. Командой lsof -p $$ созданный десриптор можно посмотреть
- echo netology > /proc/$$/fd/5 - выводит netology, т.к дескриптор перенаправляется в stdout.

8. Получится ли в качестве входного потока для pipe использовать только stderr команды, не потеряв при этом отображение stdout на pty? - Напоминаем: по умолчанию через pipe передается только stdout команды слева от | на stdin команды справа. Это можно сделать, поменяв стандартные - потоки местами через промежуточный новый дескриптор, который вы научились создавать в предыдущем вопросе.
- bash 3>&1 
- command 3>&1 1>&2 2>&3 | command такое решение меняет потоки местами с использованием промежуточного дескриптора.

9. Что выведет команда cat /proc/$$/environ? Как еще можно получить аналогичный по содержанию вывод?
- Покажет переменные окружения доступные для процесса $$.
- Альтернатива например xargs --null --max-args=1 echo < /proc/<pid>/environ
- Команда env или printenv
- Команда set

10. Используя man, опишите что доступно по адресам /proc/<PID>/cmdline, /proc/<PID>/exe
- /proc/<PID>/cmdline - содержит аргументы командной стоки, переданные при запуске процесса( кроме зомби). Аргументы командной
- строки отображаются в этом файле в виде набора строк, разделенных нулевыми байтами ('\0'), с дополнительным нулевым байтом
- после последней строки.
- /proc/$pid/exe - является символьной ссылкой на исполняемый бинарный файл. Пройдя по ссылке можно открыть его копию.В многопоточном процессе - содержимое этой символическойссылки недоступно, если основной поток уже завершен.
  
11. Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью /proc/cpuinfo
- cat /proc/cpuinfo параметры flags sse4.2

12. При открытии нового окна терминала и vagrant ssh создается новая сессия и выделяется pty. Это можно подтвердить командой tty, - которая упоминалась в лекции 3.2. Однако:
- vagrant@netology1:~$ ssh localhost 'tty'
- not a tty
- проблема возникает из-за особенностей работы vagrant c ssh
- альтернатива добавить строку в vagrantfile config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

13. Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую. Попробуйте сделать это, - воспользовавшись reptyr. Например, так можно перенести в screen процесс, который вы запустили по ошибке в обычной SSH-сессии.
- screen -S <процесс> - команда захватывает процесс и он будет продожаться при отключении сессии
- reptyr <PID of running process to attach> - перехватывает процесс в запущенный сеанс

14.sudo echo string > /root/new_file не даст выполнить перенаправление под обычным пользователем, так как перенаправлением - занимается процесс shell'а, который запущен без sudo под вашим пользователем. Для решения данной проблемы можно использовать - конструкцию echo string | sudo tee /root/new_file. Узнайте что делает команда tee и почему в отличие от sudo echo команда с sudo - tee будет работать.
- sudo echo string > /root/new_file - команда не будет работать потому что ">" - перенаправление выполняется с правами обычного пользователя
- echo string | sudo tee /root/new_file - такой формат передаёт рутовые права для записи.

