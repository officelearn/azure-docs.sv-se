---
title: Översikt över hälso övervakning för Azure Application Gateway
description: Azure Application Gateway övervakar hälsan för alla resurser i sin backend-pool och tar automatiskt bort alla resurser som betraktas som felaktiga från poolen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 01/28/2020
ms.author: victorh
ms.openlocfilehash: 5c25f591d1011d2efd66851cafd67ceef8b56637
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766830"
---
# <a name="application-gateway-health-monitoring-overview"></a>Översikt över Application Gateway Health Monitoring

Azure Application Gateway övervakar som standard hälsan för alla resurser i sin backend-pool och tar automatiskt bort alla resurser som betraktas som felaktiga från poolen. Application Gateway fortsätter att övervaka skadade instanser och lägger tillbaka dem till den felfria backend-poolen när de blir tillgängliga och svarar på hälso avsökningar. Application Gateway skickar hälso avsökningar med samma port som definieras i Server delens HTTP-inställningar. Den här konfigurationen säkerställer att avsökningen testar samma port som kunderna skulle använda för att ansluta till Server delen. 

Käll-IP-adressen Application Gateway använder för hälso avsökningar beror på backend-poolen:
 
- Om backend-poolen är en offentlig slut punkt är käll adressen den offentliga IP-adressen för Application Gateway-frontend.
- Om backend-poolen är en privat slut punkt kommer käll-IP-adressen från det privata IP-adressutrymmet i Application Gateway-undernätet.


![exempel på avsökning av Application Gateway][1]

Förutom att använda övervakning av standard hälso avsökning kan du också anpassa hälso avsökningen så att den passar ditt programs krav. I den här artikeln omfattas både standard-och anpassade hälso avsökningar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Standard hälso avsökning

En Programgateway konfigurerar automatiskt en standard hälso avsökning när du inte konfigurerar någon anpassad avsöknings konfiguration. Övervaknings beteendet fungerar genom att göra en HTTP-begäran till IP-adresserna som har kon figurer ATS för backend-poolen. För standard avsökningar om http-inställningarna för Server delen har kon figurer ATS för HTTPS, använder avsökningen HTTPS och testar hälso tillståndet för Server delen.

Exempel: du konfigurerar din Programgateway att använda backend-servrar A, B och C för att ta emot HTTP-nätverkstrafik på port 80. Standard hälso övervakningen testar de tre servrarna var 30: e sekund för ett felfritt HTTP-svar. Ett felfritt HTTP-svar har en [status kod](https://msdn.microsoft.com/library/aa287675.aspx) mellan 200 och 399.

Om standard avsöknings kontrollen Miss lyckas för Server A, tar programgatewayen bort den från sin backend-pool och nätverks trafiken slutar flöda till den här servern. Standard avsökningen fortsätter fortfarande att söka efter servern var 30: e sekund. När servern svarar på en begäran från en standard hälso avsökning, läggs den tillbaka som felfritt till backend-poolen och trafik börjar flöda till servern igen.

### <a name="probe-matching"></a>Avsöknings matchning

Som standard betraktas ett HTTP (S)-svar med status kod mellan 200 och 399 som felfri. Anpassade hälso avsökningar stöder även två matchnings villkor. Matchnings villkor kan användas för att välja att ändra standard tolkningen av vad som gör ett felfritt svar.

Följande är matchnings villkor: 

- **Http-svar status kod matchning** -matchnings villkor för avsökning för att acceptera användarens angivna HTTP-svarskod eller svars kod intervall. Enskilda kommaavgränsade svars status koder eller ett intervall med status koder stöds.
- **Svars text matchning i HTTP-svar** – matchnings villkor för avsökning som söker efter http-svars text och matchningar med en användardefinierad sträng. Matchningen söker bara efter en användardefinierad sträng i svars texten och är inte en fullständig matchning av reguljära uttryck.

Matchnings villkor kan anges med hjälp av `New-AzApplicationGatewayProbeHealthResponseMatch` cmdlet.

Ett exempel:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
När matchnings villkoren har angetts kan den kopplas till avsöknings konfigurationen med hjälp av en `-Match`-parameter i PowerShell.

### <a name="default-health-probe-settings"></a>Inställningar för standard hälso avsökning

| Egenskapen avsökning | Värde | Beskrivning |
| --- | --- | --- |
| Avsöknings-URL |http://127.0.0.1:\<port\>/ |URL-sökväg |
| Intervall |30 |Vänte tiden i sekunder innan nästa hälso avsökning skickas.|
| Timeout |30 |Hur lång tid i sekunder som Application Gateway väntar på ett avsöknings svar innan avsökningen markeras som ohälsosam. Om en avsökning returneras som felfri markeras motsvarande Server del omedelbart som felfri.|
| Tröskelvärde för ej felfri |3 |Reglerar hur många avsökningar som ska skickas om det uppstår ett fel i den normala hälso avsökningen. Dessa ytterligare hälso avsökningar skickas i snabb följd för att snabbt fastställa hälso tillståndet för Server delen och inte vänta på avsöknings intervallet. Backend-servern är markerad när det efterföljande antalet avsöknings fel uppnår tröskelvärdet. |

> [!NOTE]
> Porten är samma port som server delens HTTP-inställningar.

Standard avsökningen ser bara ut på http:\//127.0.0.1:\<port\> för att fastställa hälso status. Om du behöver konfigurera hälso avsökningen för att gå till en anpassad URL eller ändra andra inställningar måste du använda anpassade avsökningar.

### <a name="probe-intervals"></a>Avsöknings intervall

Alla instanser av Application Gateway avsöker Server delen oberoende av varandra. Samma avsöknings konfiguration gäller för varje Application Gateway instans. Om avsöknings konfigurationen till exempel skickar hälso avsökningar var 30: e sekund och programgatewayen har två instanser, skickar båda instanserna hälso avsökningen var 30: e sekund.

Även om det finns flera lyssnare avsöks varje lyssnare av Server delen oberoende av varandra. Om det till exempel finns två lyssnare som pekar på samma backend-pool på två olika portar (konfigurerade av två http-inställningar för Server delen), avsöker varje lyssnare samma server del oberoende av varandra. I det här fallet finns det två avsökningar från varje Application Gateway-instans för de två lyssnarna. Om det finns två instanser av Application Gateway i det här scenariot visas fyra avsökningar per det konfigurerade avsöknings intervallet i den virtuella datorns Server del.

## <a name="custom-health-probe"></a>Anpassad hälso avsökning

Med anpassade avsökningar kan du få en mer detaljerad kontroll över hälso övervakningen. När du använder anpassade avsökningar kan du konfigurera avsöknings intervallet, URL: en och sökvägen som ska testas och hur många misslyckade svar som ska accepteras innan du markerar backend-poolens instans som ohälsosam.

### <a name="custom-health-probe-settings"></a>Inställningar för anpassad hälso avsökning

Följande tabell innehåller definitioner av egenskaperna för en anpassad hälso avsökning.

| Egenskapen avsökning | Beskrivning |
| --- | --- |
| Namn |Namn på avsökningen. Det här namnet används för att referera till avsökningen i HTTP-inställningarna på backend-sidan. |
| Protokoll |Protokoll som används för att skicka avsökningen. Avsökningen använder protokollet som definierats i Server delens HTTP-inställningar |
| Värd |Värdnamn för att skicka avsökningen. Använd annars 127.0.0.1 om flera platser har kon figurer ATS på Application Gateway. Det här värdet skiljer sig från värd namnet för den virtuella datorn. |
| Sökväg |Den relativa sökvägen för avsökningen. Den giltiga sökvägen börjar från/. |
| Intervall |Avsöknings intervall i sekunder. Det här värdet är tidsintervallet mellan två på varandra följande avsökningar. |
| Timeout |Timeout för avsökning i sekunder. Om ett giltigt svar inte tas emot inom den här tids perioden markeras avsökningen som misslyckad.  |
| Tröskelvärde för ej felfri |Antal nya försök för avsökning. Backend-servern är markerad när det efterföljande antalet avsöknings fel uppnår tröskelvärdet. |

> [!IMPORTANT]
> Om Application Gateway har kon figurer ATS för en enda plats, som standard, ska värd namnet anges som 127.0.0.1, om inget annat anges i anpassad avsökning.
> För att referera till en anpassad avsökning skickas till \<protokoll\>://\<Host\>:\<port\>\<sökväg\>. Porten som används är samma port som definieras i Server delens HTTP-inställningar.

## <a name="nsg-considerations"></a>NSG överväganden

Om det finns en nätverks säkerhets grupp (NSG) i ett Application Gateway-undernät måste port intervall 65503-65534 öppnas i Application Gateway-undernätet för inkommande trafik. Dessa portar krävs för att Server delens hälso-API ska fungera.

Dessutom kan utgående Internet-anslutning inte blockeras och inkommande trafik från AzureLoadBalancer-taggen måste tillåtas.

## <a name="next-steps"></a>Nästa steg
När du har lärt dig om Application Gateway hälso övervakning kan du konfigurera en [anpassad hälso avsökning](application-gateway-create-probe-portal.md) i Azure Portal eller en [anpassad hälso avsökning](application-gateway-create-probe-ps.md) med hjälp av PowerShell och Azure Resource Manager distributions modellen.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
