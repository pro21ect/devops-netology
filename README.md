```
1. Предположу что это связано с особенностью режимов DR или TUN. В отличии от режима NAT пакеты в данном режиме от клиента к серверу летят минуюя ipvs.
У ipvs есть собственная таблица тайм-аутов, в которой согласно таймингу есть время ожидания ответа от клиента.
Отсюда вывод ipvsadm -Ln будут висеть ещё некоторое время.
```
```
2.  
```
```
3.  
```

