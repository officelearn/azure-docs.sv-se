| Resurs | Kostnadsfri | Delad (förhandsversion) | Basic | Standard | Premium (förhandsgranskning)</th> |
| --- | --- | --- | --- | --- | --- |
| [Webb, Mobil eller API apps](https://azure.microsoft.com/services/app-service/) per [programtjänstplanen](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup> |10 |100 |Obegränsade<sup>2</sup> |Obegränsade<sup>2</sup> |Obegränsade<sup>2</sup> |
| [Logikappar](https://azure.microsoft.com/services/app-service/logic/) per [programtjänstplanen](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup> |10 |10 |10 |20 per kärna |20 per kärna |
| [App Service-plan](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |1 per region |10 per resursgrupp |100 per resursgrupp |100 per resursgrupp |100 per resursgrupp |
| Compute-typen för instansen |Delad |Delad |Dedikerad<sup>3</sup> |Dedikerad<sup>3</sup> |Dedikerad<sup>3</sup></p> |
| [Skalbar](../articles/app-service/web-sites-scale.md) (max instanser) |1 delad |1 delad |3 dedikerade<sup>3</sup> |10 dedikerade<sup>3</sup> |20 dedikerad (50 i ASE)<sup>3,4</sup> |
| Lagring<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |500 GB<sup>4,5</sup></p> |
| CPU-tid (5 minuter)<sup>6</sup> |3 minuter |3 minuter |Obegränsade, betala på standard [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad betala vid standardpriser |Obegränsad betala vid standardpriser |
| CPU-tid (dag)<sup>6</sup> |60 minuter |240 minuter |Obegränsade, betala på standard [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad betala vid standardpriser |Obegränsad betala vid standardpriser |
| Minne (1 timme) |1 024 MB per App Service-plan |1 024 MB per program |Saknas |Saknas |Saknas |
| Bandbredd |165 MB |Obegränsade, [priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/) tillämpas |Obegränsad dataöverföring tillämpas |Obegränsad dataöverföring tillämpas |Obegränsad dataöverföring tillämpas |
| Programarkitektur |32-bitars |32-bitars |32-bitars/64-bitars |32-bitars/64-bitars |32-bitars/64-bitars |
| Web Sockets per instans<sup>7</sup> |5 |35 |350 |Obegränsat |Obegränsat |
| Samtidiga [felsökningsprogrammet anslutningar](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md) per program |1 |1 |1 |5 |5 |
| [azurewebsites.NET underdomänen med FTP-/ S och SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |X |X |X |X |X |
| [Anpassad domän](../articles/app-service/app-service-web-tutorial-custom-domain.md) stöd | |X |X |X |X |
| Anpassad domän [SSL-stöd](../articles/app-service/app-service-web-tutorial-custom-ssl.md) | | |Obegränsade SNI SSL-anslutningar |Obegränsade SNI SSL och 1 IP SSL-anslutningar som ingår |Obegränsade SNI SSL och 1 IP SSL-anslutningar som ingår |
| Integrerad belastningsutjämnare | |X |X |X |X |
| [Always On](../articles/app-service/web-sites-configure.md) | | |X |X |X |
| [Schemalagda säkerhetskopieringar](../articles/app-service/web-sites-backup.md) | | | |En gång per dag |Var 5: e minut<sup>8</sup> |
| [Automatisk skalning](../articles/app-service/web-sites-scale.md) | | | |X |X |
| [WebJobs](../articles/app-service/web-sites-create-web-jobs.md)<sup>9</sup> |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) stöd | |X |X |X |X |
| [Slutpunktsövervakning](../articles/app-service/web-sites-monitor.md) | | |X |X |X |
| [Mellanlagringsplatser](../articles/app-service/web-sites-staged-publishing.md) | | | |5 |20 |
| Anpassade domäner per program</a> | |500 |500 |500 |500 |
| SLA | |<p> |99,9 % |99.95%<sup>10</sup> |99.95%<sup>10</sup> |

<sup>1</sup>appar och lagringskvoter är per App Service-plan om inte annat anges.  
<sup>2</sup>det faktiska antalet appar som du kan vara värd för på dessa datorer beroende aktivitet appar, storleken på datorinstanserna och motsvarande resursutnyttjandet.  
<sup>3</sup>dedikerade instanser kan vara av olika storlekar. Se [priser för Apptjänst](https://azure.microsoft.com/pricing/details/data-transfers/pricing/details/app-service/) för mer information.  
<sup>4</sup>premiumnivån kan upp till 50 beräknar instanser (beroende på tillgänglighet) och 500 GB diskutrymme när du använder Apptjänstmiljöer och 20 compute-instanser och 250 GB lagring på annat sätt.  
<sup>5</sup>lagringsgränsen är den totala storleken för innehåll över alla appar i samma App Service-plan. Flera lagringsalternativ finns i [Apptjänstmiljö](../articles/app-service/environment/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup>resurserna är begränsad av fysiska resurser för dedikerade instanser (instansstorleken och antalet instanser).  
<sup>7</sup>om du skalar en app i den grundläggande nivån till två instanser, har du 350 samtidiga anslutningar för var och en av två instanser.  
<sup>8</sup>premiumnivån kan säkerhetskopieringsintervall ned upp till var femte minut när du använder Apptjänstmiljöer och 50 gånger per dag på annat sätt.  
<sup>9</sup>köra anpassade körbara filer eller skript på begäran, enligt ett schema eller kontinuerligt som en bakgrundsaktivitet i App Service-instans. ”Alltid på” krävs för kontinuerlig körning av WebJobs. Azure Scheduler Kostnadsfri eller Standard krävs för schemalagda WebJobs. Det finns ingen fördefinierad gräns för antalet WebJobs som kan köras i en App Service-instans, men det finns praktiska gränserna som beror på vad programkoden försöker utföra.   
<sup>10</sup>SLA på 99,95% för distributioner som använder flera instanser med Azure Traffic Manager som konfigurerats för redundans.  

