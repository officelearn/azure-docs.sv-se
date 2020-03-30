---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 505e2d8eec20853fba3743b40cbe289585d14d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304998"
---
| Resurs | Kostnadsfri | Delad | Basic | Standard | Premium (v2) | Isolerat </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Webb-, mobil- eller API-appar](https://azure.microsoft.com/services/app-service/) per [Azure App Service-plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Obegränsad<sup>2</sup> |Obegränsad<sup>2</sup> |Obegränsad<sup>2</sup> |Obegränsad<sup>2</sup>|
| [App Service plan](../articles/app-service/overview-hosting-plans.md) |10 per region |10 per resursgrupp |100 per resursgrupp |100 per resursgrupp |100 per resursgrupp |100 per resursgrupp|
| Typ av beräkningsinstans |Delad |Delad |Dedikerade<sup>3</sup> |Dedikerade<sup>3</sup> |Dedikerade<sup>3</sup></p> |Dedikerade<sup>3</sup>|
| [Skala ut](../articles/app-service/manage-scale-up.md) (maximala instanser) |1 delad |1 delad |3 dedikerade<sup>3</sup> |10 dedikerade<sup>3</sup> |30 dedikerade<sup>3</sup>|100 dedikerade<sup>4</sup>|
| Lagring<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU-tid (5 minuter)<sup>6</sup> |3 minuter |3 minuter |Obegränsad, betala till [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala till [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala till [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala till [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| CPU-tid (dag)<sup>6</sup> |60 minuter |240 minuter |Obegränsad, betala till [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala till [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala till [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala till [standardpriser](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Minne (1 timme) |1 024 MB per apptjänstplan |1 024 MB per app |Ej tillämpligt |Ej tillämpligt |Ej tillämpligt |Ej tillämpligt |
| Bandbredd |165 MB |Obegränsad, [dataöverföringshastigheter](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad, [dataöverföringshastigheter](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad, [dataöverföringshastigheter](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad, [dataöverföringshastigheter](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad, [dataöverföringshastigheter](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |
| Programarkitektur |32-bitars |32-bitars |32-bitars/64-bitars |32-bitars/64-bitars |32-bitars/64-bitars |32-bitars/64-bitars |
| Webbuttag per instans<sup>7</sup> |5 |35 |350 |Unlimited |Unlimited |Unlimited |
| IP-anslutningar | 600 | 600 | Beror på instansstorlek<sup>8</sup> | Beror på instansstorlek<sup>8</sup> | Beror på instansstorlek<sup>8</sup> | 16 000 |
| Samtidiga [felsökningsanslutningar](../articles/app-service/troubleshoot-dotnet-visual-studio.md) per program |1 |1 |1 |5 |5 |5 |
| Apptjänstcertifikat per prenumeration<sup>9</sup>| Stöds inte | Stöds inte |10 |10 |10 |10 |
| Anpassade domäner per app</a> |0 (endast azurewebsites.net underdomän)|500 |500 |500 |500 |500 |
| Anpassat [SSL-stöd](../articles/app-service/configure-ssl-certificate.md) för domän |Jokerteckencertifikat för *.azurewebsites.net som standard inte stöds, jokerteckencertifikat|Jokerteckencertifikat för *.azurewebsites.net som standard inte stöds, jokerteckencertifikat|ObegränsadE SNI SSL-anslutningar |Obegränsad SNI SSL och 1 IP SSL-anslutningar ingår |Obegränsad SNI SSL och 1 IP SSL-anslutningar ingår | Obegränsad SNI SSL och 1 IP SSL-anslutningar ingår|
| Hybridanslutningar per plan | | | 5 | 25 | 200 | 200 |
| Integrerad belastningsutjämnare | |X |X |X |X |X<sup>10</sup> |
| [Alltid på](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Schemalagda säkerhetskopior](../articles/app-service/manage-backup.md) | | | | Schemalagda säkerhetskopior varannan timme, högst 12 säkerhetskopior per dag (manuell + schemalagd) | Schemalagda säkerhetskopior varje timme, högst 50 säkerhetskopior per dag (manuell + schemalagd) | Schemalagda säkerhetskopior varje timme, högst 50 säkerhetskopior per dag (manuell + schemalagd) |
| [Automatisk skalning](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Övervakning av slutpunkt](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Mellanlagringsplatser](../articles/app-service/deploy-staging-slots.md) per app| | | |5 |20 |20 |
| SLA | |  |99,95 %|99,95 %|99,95 %|99,95 %|  

<sup>1.</sup> Appar och lagringskvoter är per App Service-plan om inget annat anges.  
<sup>2.</sup> Det faktiska antalet appar som du kan vara värd för på dessa datorer beror på aktiviteterna i apparna, storleken på datorinstanserna och motsvarande resursutnyttjande.  
<sup>3.</sup> Dedikerade instanser kan vara av olika storlek. Mer information finns i [Priser för App Service](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4.</sup> Fler är tillåtna på begäran.  
<sup>5 5</sup> Lagringsgränsen är den totala innehållsstorleken för alla appar i samma App-tjänstplan. Den totala innehållsstorleken för alla appar i alla App-tjänstplaner i en enda resursgrupp och region får inte överstiga 500 GB.  
<sup>6.</sup> Dessa resurser begränsas av fysiska resurser på de dedikerade instanserna (instansstorleken och antalet instanser).  
<sup>7.</sup> Om du skalar en app på basic-nivån till två instanser har du 350 samtidiga anslutningar för var och en av de två instanserna. För standardnivå och högre finns det inga teoretiska gränser för webbuttag, men andra faktorer kan begränsa antalet webbuttag. Till exempel är högsta tillåtna samtidiga `maxConcurrentRequestsPerCpu`begäranden (definierad av): 7 500 per liten virtuell dator, 15 000 per medelstor virtuell dator (7 500 x 2 kärnor) och 75 000 per stor virtuell dator (18 750 x 4 kärnor).  
<sup>8.</sup> De maximala IP-anslutningarna är per instans och beror på instansstorleken: 1 920 per B1/S1/P1V2-instans, 3 968 per B2/S2/P2V2-instans, 8 064 per B3/S3/P3V2-instans.  
<sup>9.</sup> Kvotgränsen för App Service-certifikat per prenumeration kan ökas via en supportbegäran till en maximal gräns på 200.  
<sup>10 år</sup> App Service Isolerade SKU:er kan vara internt belastningsbalanserade (ILB) med Azure Load Balancer, så det finns ingen offentlig anslutning från internet. Därför måste vissa funktioner i en ILB-isolerad apptjänst användas från datorer som har direkt åtkomst till ILB-nätverksslutpunkten.  
<sup>11.</sup> Kör anpassade körbara filer och/eller skript på begäran, enligt ett schema eller kontinuerligt som en bakgrundsaktivitet i App Service-instansen. Alltid på krävs för kontinuerlig WebJobs-körning. Det finns ingen fördefinierad gräns för antalet WebJobs som kan köras i en App Service-instans. Det finns praktiska gränser som beror på vad programkoden försöker göra.  
