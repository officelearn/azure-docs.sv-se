| Resurs | Standardgräns | Obs! |
| --- | --- | --- |
| Application Gateway |50 per prenumeration | Maximal 1000 |
| IP-konfigurationer för frontend |2 |1 offentlig och 1 privat |
| Frontend-portar |20 | |
| Backend-adresspooler |20 | |
| Backend-servrar per pool |100 | |
| HTTP-lyssnare |20 | |
| Regler för HTTP-belastningsutjämning |200 |Antal HTTP-lyssnare * n, n = 10 standard |
| HTTP-inställningar för backend |20 |1 per backend-adresspool |
| Instanser per gateway |10 | För flera instanser, öppna supportärende |
| SSL-certifikat |20 |1 per HTTP-lyssnare |
| Autentiseringscertifikat |5 | Högst 10 |
| Minsta timeout för begäran |1 sekund | |
| Högsta timeout för begäran |24 timmar | |
| Antal platser |20 |1 per HTTP-lyssnare |
| URL-mappningar per lyssnare |1 | |
| Samtidiga WebSocket-anslutningar |5000| |
|Maximal URL-längd|8000|
| Maximala storleken för filöverföring Standard |2 GB | |
| Maximal uppladdning storlek WAF |Medelhög WAF-gatewayer – 100 MB<br>Stora WAF-gatewayer – 500 MB| |
|Storleksgräns för WAF brödtext (utan-filer)|128 KB|

