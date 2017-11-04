<!--v-sharos 10/13/2105 virtual device security-->

Tänk på följande säkerhetsaspekter när du använder den virtuella StorSimple-enheten:

* Den virtuella enheten är säkrad via Microsoft Azure-prenumerationen. Detta innebär att om du använder den virtuella enheten och Azure-prenumerationen komprometteras, de data som lagras på din virtuella enhet är också känslig.
* Den offentliga nyckeln för certifikatet som används för att kryptera data som lagras i Azure StorSimple på ett säkert sätt få tillgång till den klassiska Azure-portalen och den privata nyckeln sparas med StorSimple-enhet. Både offentliga och privata nycklar lagras på den virtuella StorSimple-enheten i Azure.
* Den virtuella enheten finns i Microsoft Azure-datacenter.

