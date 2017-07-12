<!--alkohli 02/21/2017 cloud appliance security-->

Ha följande säkerhetsöverväganden i åtanke när du använder StorSimple Cloud Appliance:

* Molninstallationen skyddas via din Microsoft Azure-prenumeration. Det innebär att om du använder molninstallationen och din Azure-prenumeration komprometteras, så blir även de data som lagras i molninstallationen sårbara.
* Den offentliga nyckeln för certifikatet som används för att kryptera data som lagras i StorSimple görs tillgänglig för Azure Portal på ett säkert sätt, och den privata nyckeln sparas i StorSimple Cloud Appliance. Med StorSimple Cloud Appliance lagras både den offentliga och privata nyckeln i Azure.
* Molninstallationen finns på Microsoft Azure-datacentret.

