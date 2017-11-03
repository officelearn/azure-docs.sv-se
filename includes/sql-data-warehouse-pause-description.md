
<!--
includes/sql-data-warehouse-include-pause-description.md

Latest Freshness check:  2016-04-22 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-powershell.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-rest-api.md

-->
Du kan pausa och återuppta beräkning resurser på begäran för att spara kostnader. Till exempel om du inte använder databasen under natten och helger, kan du pausa under dessa tider och återuppta under dagen. Du kommer inte att debiteras för dwu: er när databasen har pausats.

När du pausar en-databas:

* Beräknings-och minnesresurser återgår till poolen med tillgängliga resurser i datacentret
* DWU är noll för pausen varaktighet.
* Datalagring påverkas inte och dina data förblir intakt. 
* SQL Data Warehouse avbryter alla åtgärder som körs eller står i kö.

