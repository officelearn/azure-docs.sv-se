<!--v-sharos 10/13/2105 virtual device security-->

Ha följande säkerhetsöverväganden i åtanke när du använder den virtuella StorSimple-enheten:

* Den virtuella enheten skyddas via Microsoft Azure-prenumerationen. Det innebär att om du använder den virtuella enheten och din Azure-prenumeration komprometteras, de data som lagras på din virtuella enhet är också sårbara.
* Den offentliga nyckeln för certifikatet som används för att kryptera data som lagras i Azure StorSimple på ett säkert sätt få tillgång till den klassiska Azure-portalen och den privata nyckeln sparas med StorSimple-enheten. På den virtuella StorSimple-enheten lagras både offentliga och privata nycklar i Azure.
* Den virtuella enheten finns i Microsoft Azure-datacentret.

