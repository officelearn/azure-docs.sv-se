| Resurs | Kostnadsfri | Delad (förhandsversion) | Basic | Standard | Premium </th> |
| --- | --- | --- | --- | --- | --- |
| [Webb-, Mobil- eller API apps](https://azure.microsoft.com/services/app-service/) per [apptjänstplan](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup> |10 |100 |Obegränsade<sup>2</sup> |Obegränsade<sup>2</sup> |Obegränsade<sup>2</sup> |
| [Logikappar](https://azure.microsoft.com/services/app-service/logic/) per [apptjänstplan](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup> |10 |10 |10 |20 per kärna |20 per kärna |
| [App Service-plan](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |1 per region |10 per resursgrupp |100 per resursgrupp |100 per resursgrupp |100 per resursgrupp |
| Beräkning instanstyp |Delad |Delad |Dedikerad<sup>3</sup> |Dedikerad<sup>3</sup> |Dedikerad<sup>3</sup></p> |
| [Skala ut](../articles/app-service/web-sites-scale.md) (max. instanser) |1 delad |1 delad |3 dedikerade<sup>3</sup> |10 dedikerade<sup>3</sup> |20 dedikerade (50 i ASE)<sup>3,4</sup> |
| Storage<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |500 GB<sup>4,5</sup></p> |
| CPU-tid (5 minuter)<sup>6</sup> |3 minuter |3 minuter |Obegränsade, betala enligt standardpriserna [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unlimited, betala per användning till standardpriser |Unlimited, betala per användning till standardpriser |
| Processortid (dag)<sup>6</sup> |60 minuter |240 minuter |Obegränsade, betala enligt standardpriserna [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Unlimited, betala per användning till standardpriser |Unlimited, betala per användning till standardpriser |
| Minne (1 timme) |1 024 MB per App Service-plan |1 024 MB per app |Gäller inte |Saknas |Gäller inte |
| Bandbredd |165 MB |Obegränsad och [dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad dataöverföring gäller |Obegränsad dataöverföring gäller |Obegränsad dataöverföring gäller |
| Programarkitektur |32-bitars |32-bitars |32-bitars/64-bitars |32-bitars/64-bitars |32-bitars/64-bitars |
| Web Sockets per instans<sup>7</sup> |5 |35 |350 |Obegränsat |Obegränsat |
| Samtidiga [felsökning av anslutningar](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md) per program |1 |1 |1 |5 |5 |
| [azurewebsites.NET underdomän med FTP/S och SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |X |X |X |X |X |
| [Anpassad domän](../articles/app-service/app-service-web-tutorial-custom-domain.md) stöd | |X |X |X |X |
| Anpassad domän [SSL-stöd](../articles/app-service/app-service-web-tutorial-custom-ssl.md) | | |Obegränsade SNI SSL-anslutningar |Obegränsade SNI SSL och 1 IP SSL-anslutningar ingår |Obegränsade SNI SSL och 1 IP SSL-anslutningar ingår |
| Integrerad belastningsutjämnare | |X |X |X |X |
| [Alltid på](../articles/app-service/web-sites-configure.md) | | |X |X |X |
| [Schemalagda säkerhetskopieringar](../articles/app-service/web-sites-backup.md) | | | | Schemalagda säkerhetskopieringar varannan timme, maximalt 12 säkerhetskopieringar per dag (manuell + schemalagd) | Schemalagda säkerhetskopieringar varje timme, högst 50 säkerhetskopieringar per dag (manuell + schemalagd) |
| [Automatisk skalning](../articles/app-service/web-sites-scale.md) | | | |X |X |
| [WebJobs](../articles/app-service/web-sites-create-web-jobs.md)<sup>8</sup> |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) stöd | |X |X |X |X |
| [Slutpunktsövervakning](../articles/app-service/web-sites-monitor.md) | | |X |X |X |
| [Mellanlagringsplatser](../articles/app-service/web-sites-staged-publishing.md) | | | |5 |20 |
| Anpassade domäner per app</a> | |500 |500 |500 |500 |
| SLA | |<p> |99,9 % |99.95%<sup>10</sup> |99.95%<sup>9</sup> |

<sup>1</sup>appar och lagringskvoter är per App Service-plan om inget annat anges.  
<sup>2</sup>det faktiska antalet appar som du kan ha på de här datorerna beror på aktiviteten för appar, storleken på instanserna av datorer och motsvarande resursutnyttjandet.  
<sup>3</sup>dedikerade instanser kan vara av olika storlekar. Se [priser för Apptjänst](https://azure.microsoft.com/pricing/details/app-service/) för mer information.  
<sup>4</sup>nivån premium ger upp till 50 beräkningsinstanser instanser (beroende på tillgänglighet) och 500 GB diskutrymme när du använder App Service-miljöer och 20 beräkningsinstanser och 250 GB lagring annars.  
<sup>5</sup>lagringsgränsen är den totala innehållsstorleken över alla appar i samma App Service-planen. Flera lagringsalternativ finns i [App Service Environment](../articles/app-service/environment/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup>dessa resurser är begränsade av fysiska resurser på de dedikerade instanserna (instansstorlek och antalet instanser).  
<sup>7</sup>om du skalar en app i Basic-nivån till två instanser du har 350 samtidiga anslutningar för var och en av de två instanserna.  
<sup>8</sup>kör anpassade körningsbara filer och/eller skript på begäran, enligt ett schema eller kontinuerligt som bakgrundsjobb i din App Service. ”Alltid på” krävs för kontinuerlig körning av WebJobs. Azure Scheduler Kostnadsfri eller Standard krävs för schemalagda WebJobs. Det finns ingen fördefinierade gräns för hur många av WebJobs som kan köras i en App Service-instans, men det finns praktisk gräns som beror på vad programkoden försöker utföra.   
<sup>9</sup>serviceavtal på 99,95% tillhandahålls för distributioner som använder flera instanser med Azure Traffic Manager som konfigurerats för redundans.  

