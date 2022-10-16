# suricata
Возьмём для примера вот [этот](https://securelist.com/the-sessionmanager-iis-backdoor/106868/) отчёт

Запустим на компьютере жертвы Wireshark для сохранения дампа сетевого трафика, который в дальнейшем будем прогонять через правила Suricata. 
С помощью BurpSuite (для наших целей free version вполне хватит)
```
apt-get update
apt-get install burpsuite 
```
формируем пакет и отправляем на компьютер жертвы.
![burp](/images/1.png)
Сохраняем наш трафик 
![wireshark](/images/2.jpg)
и идём писать правила:

`alert http any any -> any any (msg:"Win32/SessionManager Request Cookie SM_SESSIONID"; flow:established,to_server; content:"SM_SESSIONID="; depth:13; http_cookie; sid:10003; rev:1;)`

Сохраняем файл как cookie.rules и начинаем тестировать:

`suricata -vvv -r $path_to_pcap -S $path_to_rule -k none | grep Alerts`

После запуска в текущей директории появятся файлы:
- fast.log
- stats.log
- suricata.log

Нас интересует fast.log (туда попадут алерты со сработок)
![output](/images/3.png)