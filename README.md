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
- include/linux/string.h
- include/linux/string.h
- include/uapi/linux/string.h

6.  


   
