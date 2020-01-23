---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: 17d6947efcbaf8a01aeca70bf7a88dcc7b65cd0a
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548242"
---
| Resurs | Kostnadsfri | Delad | Basic | Standard | Premium (v2) | Isolerad </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Webb-, mobil-eller API-appar](https://azure.microsoft.com/services/app-service/) per [Azure App Service plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Obegränsat<sup>2</sup> |Obegränsat<sup>2</sup> |Obegränsat<sup>2</sup> |Obegränsat<sup>2</sup>|
| [App Service-plan](../articles/app-service/overview-hosting-plans.md) |10 per region |10 per resurs grupp |100 per resurs grupp |100 per resurs grupp |100 per resurs grupp |100 per resurs grupp|
| Beräknings instans typ |Delad |Delad |Dedikerad<sup>3</sup> |Dedikerad<sup>3</sup> |Dedikerad<sup>3</sup></p> |Dedikerad<sup>3</sup>|
| [Skala ut](../articles/app-service/manage-scale-up.md) (maximalt antal instanser) |1 delad |1 delad |3 dedikerad<sup>3</sup> |10 dedikerat<sup>3</sup> |30 dedikerat<sup>3</sup>|100 dedikerat<sup>4</sup>|
| Lagring<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| PROCESSOR tid (5 minuter)<sup>6</sup> |3 minuter |3 minuter |Obegränsad, betala enligt standard [taxa](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala enligt standard [taxa](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala enligt standard [taxa](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala enligt standard [taxa](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| CPU-tid (dag)<sup>6</sup> |60 minuter |240 minuter |Obegränsad, betala enligt standard [taxa](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala enligt standard [taxa](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala enligt standard [taxa](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsad, betala enligt standard [taxa](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Minne (1 timme) |1 024 MB per App Service plan |1 024 MB per app |Gäller inte |Gäller inte |Gäller inte |Gäller inte |
| Bandbredd |165 MB |Obegränsad, [data överförings taxa](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad, [data överförings taxa](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad, [data överförings taxa](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad, [data överförings taxa](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad, [data överförings taxa](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |
| Programarkitektur |32-bitars |32-bitars |32-bitars/64-bitars |32-bitars/64-bitars |32-bitars/64-bitars |32-bitars/64-bitars |
| Web Sockets per instans<sup>7</sup> |5 |35 |350 |Unlimited |Unlimited |Unlimited |
| IP-anslutningar | 600 | 600 | Är beroende av instans storlek<sup>8</sup> | Är beroende av instans storlek<sup>8</sup> | Är beroende av instans storlek<sup>8</sup> | 16,000 |
| Samtidiga [fel söknings anslutningar](../articles/app-service/troubleshoot-dotnet-visual-studio.md) per program |1 |1 |1 |5 |5 |5 |
| App Service certifikat per prenumeration<sup>9</sup>| Stöds inte | Stöds inte |10 |10 |10 |10 |
| Anpassade domäner per app</a> |0 (endast azurewebsites.net-underdomän)|500 |500 |500 |500 |500 |
| Stöd för anpassad domän- [SSL](../articles/app-service/configure-ssl-certificate.md) |Saknar stöd för det jokertecken för *. azurewebsites.net som är tillgängligt som standard|Saknar stöd för det jokertecken för *. azurewebsites.net som är tillgängligt som standard|Obegränsade SNI SSL anslutningar |Obegränsade SNI SSL och 1 IP SSL anslutningar ingår |Obegränsade SNI SSL och 1 IP SSL anslutningar ingår | Obegränsade SNI SSL och 1 IP SSL anslutningar ingår|
| Hybrid anslutningar per plan | | | 5 | 25 | 200 | 200 |
| Integrerad belastningsutjämnare | |X |X |X |X |X<sup>10</sup> |
| [Always on](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Schemalagda säkerhets kopieringar](../articles/app-service/manage-backup.md) | | | | Schemalagda säkerhets kopieringar varje 2 timme, högst 12 säkerhets kopior per dag (manuellt + schemalagd) | Schemalagda säkerhets kopieringar varje timme, högst 50 säkerhets kopior per dag (manuellt + schemalagd) | Schemalagda säkerhets kopieringar varje timme, högst 50 säkerhets kopior per dag (manuellt + schemalagd) |
| [Automatisk skalning](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| Stöd för [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) | |X |X |X |X |X |
| [Slutpunktsövervakning](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Mellanlagrings platser](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99,95 %|99,95 %|99,95 %|99,95 %|  

<sup>1</sup> Appar och lagrings kvoter per App Service plan om inget annat anges.  
<sup>2</sup> Det faktiska antalet appar som du kan vara värd för på de här datorerna beror på aktivitetens appar, storleken på dator instanserna och motsvarande resursutnyttjande.  
<sup>3</sup> Dedikerade instanser kan ha olika storlekar. Mer information finns i [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Fler tillåts på begäran.  
<sup>5</sup> Lagrings gränsen är den totala innehålls storleken för alla appar i samma app service-plan. Den totala innehålls storleken för alla appar i alla App Service-planer i en enda resurs grupp och region får inte överstiga 500 GB.  
<sup>6</sup> Dessa resurser begränsas av fysiska resurser på dedikerade instanser (instans storlek och antal instanser).  
<sup>7</sup> Om du skalar en app på Basic-nivån till två instanser har du 350 samtidiga anslutningar för var och en av de två instanserna. För standard-nivån och ovan finns det inga teoretiska gränser för Web Sockets, men andra faktorer kan begränsa antalet Web Sockets. Till exempel är maximalt antal samtidiga begär Anden som tillåts (definieras av `maxConcurrentRequestsPerCpu`): 7 500 per liten virtuell dator, 15 000 per virtuell dator (7 500 x 2 kärnor) och 75 000 per stor virtuell dator (18 750 x 4 kärnor).  
<sup>8</sup> De högsta IP-anslutningarna är per instans och är beroende av instans storleken: 1 920 per B1/S1/P1V2-instans, 3 968 per B2/S2/P2V2-instans, 8 064 per B3/S3/P3V2-instans.  
<sup>9</sup> Gränsen för App Service Certificate kvot per prenumeration kan ökas via en supportbegäran till en maximal gräns på 200.  
<sup>10</sup> App Service Isolerad SKU: er kan vara internt belastningsutjämnade (ILB) med Azure Load Balancer, så det finns ingen offentlig anslutning från Internet. Därför måste vissa funktioner för en ILB-isolerad App Service användas från datorer som har direkt åtkomst till ILB-nätverksslutpunkten.  
<sup>11</sup> Kör anpassade körbara filer och/eller skript på begäran, enligt schema eller kontinuerligt som bakgrunds aktivitet i App Service-instansen. ”Alltid på” krävs för kontinuerlig körning av WebJobs. Azure Scheduler Kostnadsfri eller Standard krävs för schemalagda WebJobs. Det finns ingen fördefinierad gräns för antalet WebJobs som kan köras i en App Service instans. Det finns praktiska gränser som beror på vad program koden försöker göra.  
