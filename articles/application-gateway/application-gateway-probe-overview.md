---
title: Översikt över hälsoövervakning för Azure Application Gateway
description: Azure Application Gateway övervakar hälsotillståndet för alla resurser i backend-poolen och tar automatiskt bort alla resurser som anses vara felaktiga från poolen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2020
ms.author: victorh
ms.openlocfilehash: c5a53167c6a4ca6c886b858a1608eaa173185bd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335849"
---
# <a name="application-gateway-health-monitoring-overview"></a>Översikt över hälsoövervakning av programgateway

Azure Application Gateway övervakar som standard hälsotillståndet för alla resurser i backend-poolen och tar automatiskt bort alla resurser som anses vara felaktiga från poolen. Application Gateway fortsätter att övervaka de felaktiga instanserna och lägger till dem i den felfria serverdelpoolen när de är tillgängliga och svarar på hälsokontroller. Programgateway skickar hälsoavsökningar med samma port som definieras i http-backend-inställningarna. Den här konfigurationen säkerställer att avsökningen testar samma port som kunderna skulle använda för att ansluta till serverkopplingen. 

Källans IP-adress Application Gateway använder för hälsoavsökningar beror på serverdapoolen:
 
- Om serverdelspoolen är en offentlig slutpunkt är källadressen den offentliga IP-adressen för programgateway.If the backend pool is a public endpoint, then the source address is the application gateway frontend public IP address.
- Om serverdelspoolen är en privat slutpunkt kommer käll-IP-adressen från det privata IP-adressutrymmet för programgatewayen.


![exempel på programgateway-avsökning][1]

Förutom att använda standardövervakning av hälsoavsökning kan du också anpassa hälsoavsökningen så att den passar programmets behov. I den här artikeln omfattas både standard- och anpassade hälsoavsökningar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Standardhälsoavsökning

En programgateway konfigurerar automatiskt en standardhälsoavsökning när du inte ställer in någon anpassad avsökningskonfiguration. Övervakningsbeteendet fungerar genom att göra en HTTP-begäran till IP-adresser som konfigurerats för backend-poolen. För standardavsökningar om serverd http-inställningarna är konfigurerade för HTTPS använder avsökningen HTTPS också för att testa hälsan hos serverdarna.

Du konfigurerar till exempel programgatewayen så att backend-servrarna A, B och C används för att ta emot HTTP-nätverkstrafik på port 80. Standardhälsoövervakningen testar de tre servrarna var 30:e sekund för ett felfritt HTTP-svar. Ett felfri HTTP-svar har en [statuskod](https://msdn.microsoft.com/library/aa287675.aspx) mellan 200 och 399.

Om standardavsökningskontrollen misslyckas för server A tar programgatewayen bort den från backend-poolen och nätverkstrafiken slutar flöda till den här servern. Standardavsökningen fortsätter att söka efter server A var 30:e sekund. När server A svarar på en begäran från en standardhälsoavsökning läggs den tillbaka som felfri i backend-poolen och trafiken börjar flöda till servern igen.

### <a name="probe-matching"></a>Avsökningsmatchning

Som standard anses ett HTTP(S)-svar med statuskod mellan 200 och 399 vara felfritt. Anpassade hälsoavsökningar stöder dessutom två matchande villkor. Matchande villkor kan användas för att eventuellt ändra standardtolkningen av vad som gör ett felfritt svar.

Följande är matchande kriterier: 

- **HTTP-svarsstatuskodmatchning** - Avsökningsmatchningsvillkor för att acceptera användarspecificerade http-svarskod eller svarskodintervall. Individuella kommaavgränsade svarsstatuskoder eller ett intervall med statuskod stöds.
- **HTTP-svarstextmatchning** - Avsökningsmatchningsvillkor som tittar på HTTP-svarstexten och matchar med en användarspecificerad sträng. Matchningen söker bara efter närvaro av användarspecificerad sträng i svarstexten och är inte en fullständig matchning för reguljära uttryck.

Matchningsvillkor kan anges `New-AzApplicationGatewayProbeHealthResponseMatch` med hjälp av cmdleten.

Ett exempel:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
När matchningsvillkoren har angetts kan det kopplas `-Match` till avsökningskonfiguration med hjälp av en parameter i PowerShell.

### <a name="default-health-probe-settings"></a>Standardinställningar för hälsoavsökning

| Egenskapen Probe | Värde | Beskrivning |
| --- | --- | --- |
| Url för avsökning |http://127.0.0.1:\<port\>/ |URL-sökväg |
| Intervall |30 |Hur lång tid i sekunder som ska vänta innan nästa hälsoavsökning skickas.|
| Timeout |30 |Den tid i sekunder som programgatewayen väntar på ett avsökningssvar innan avsökningen markeras som fel. Om en avsökning återgår som felfri markeras motsvarande serverning omedelbart som felfri.|
| Felfritt tröskelvärde |3 |Styr hur många sonder som ska skickas om det är fel på den vanliga hälsoavsökningen. Dessa ytterligare hälsoavsökningar skickas i snabb följd för att snabbt fastställa serverdelens hälsa och vänta inte på avsökningsintervallet. Denna behaivor är bara v1 SKU. När det gäller v2 SKU väntar hälsoavsökningarna intervallet. Backend-servern markeras efter att antalet avsökningsfel i följd når det felaktiga tröskelvärdet. |

> [!NOTE]
> Porten är samma port som http-inställningarna för backend.

Standardavsökningen tittar bara\/på http: /127.0.0.1:\<port\> för att fastställa hälsotillstånd. Om du behöver konfigurera hälsoavsökningen så att den går till en anpassad URL eller ändrar andra inställningar måste du använda anpassade avsökningar.

### <a name="probe-intervals"></a>Avsökningsintervall

Alla instanser av Application Gateway avsöker serverd oberoende av varandra. Samma avsökningskonfiguration gäller för varje Application Gateway-instans. Om till exempel avsökningskonfigurationen ska skicka hälsoavsökningar var 30:e sekund och programgatewayen har två instanser, skickar båda instanserna hälsoavsökningen var 30:e sekund.

Även om det finns flera lyssnare, då varje lyssnare sonder serverad oberoende av varandra. Om det till exempel finns två lyssnare som pekar på samma serverdpool på två olika portar (konfigurerade av två serverliggande http-inställningar) så avsöker varje lyssnare samma serverning oberoende av varandra. I det här fallet finns det två avsökningar från varje programgatewayinstans för de två lyssnarna. Om det finns två instanser av programgatewayen i det här scenariot, skulle serverda virtuell dator se fyra avsökningar per det konfigurerade avsökningsintervallet.

## <a name="custom-health-probe"></a>Anpassad hälsoavsökning

Anpassade avsökningar gör att du kan ha en mer detaljerad kontroll över hälsoövervakningen. När du använder anpassade avsökningar kan du konfigurera avsökningsintervallet, URL:en och sökvägen för att testa och hur många misslyckade svar som ska accepteras innan backend-poolinstansen markeras som felaktig.

### <a name="custom-health-probe-settings"></a>Inställningar för anpassad hälsoavsökning

I följande tabell finns definitioner för egenskaperna för en anpassad hälsoavsökning.

| Egenskapen Probe | Beskrivning |
| --- | --- |
| Namn |Sondens namn. Det här namnet används för att referera till avsökningen i http-inställningar för backend. |
| Protokoll |Protokoll som används för att skicka sonden. Avsökningen använder protokollet som definierats i http-inställningarna för backend |
| Värd |Värdnamn för att skicka avsökningen. Gäller endast när flera plats konfigureras på Application Gateway, annars använd "127.0.0.1". Det här värdet skiljer sig från vm-värdnamnet. |
| Sökväg |Sondens relativa sökväg. Den giltiga sökvägen börjar från '/'. |
| Intervall |Avsökningsintervallet på några sekunder. Det här värdet är tidsintervallet mellan två på varandra följande avsökningar. |
| Timeout |Sondens time-out på några sekunder. Om ett giltigt svar inte tas emot inom den här time-out-perioden markeras avsökningen som misslyckad.  |
| Felfritt tröskelvärde |Antalet försök för avsökningen. Backend-servern markeras efter att antalet avsökningsfel i följd når det felaktiga tröskelvärdet. |

> [!IMPORTANT]
> Om Application Gateway är konfigurerat för en enda plats bör värdnamnet som standard anges som "127.0.0.1", om inte annat konfigureras i anpassad avsökning.
> Som referens skickas en \<anpassad\>\<avsökning\>\<till\>protokoll:// värd\>:\<portsökväg . Porten som används kommer att vara samma port som definieras i http-inställningarna för backend.

## <a name="nsg-considerations"></a>NSG överväganden

Du måste tillåta inkommande Internet-trafik på TCP-portar 65503-65534 för Application Gateway v1 SKU och TCP-portar 65200-65535 för v2 SKU med målundernätet som **Alla** och källa som **GatewayManager-tjänsttag.** Det här portintervallet krävs för Azure-infrastrukturkommunikation.

Dessutom kan utgående Internet-anslutning inte blockeras och inkommande trafik som kommer från **AzureLoadBalancer-taggen** måste tillåtas.

Mer information finns i [konfigurationsöversikt för Programgateway](configuration-overview.md#network-security-groups-on-the-application-gateway-subnet).

## <a name="next-steps"></a>Nästa steg
När du har lärt dig mer om hälsoövervakning av Application Gateway kan du konfigurera en [anpassad hälsoavsökning](application-gateway-create-probe-portal.md) i Azure-portalen eller en [anpassad hälsoavsökning](application-gateway-create-probe-ps.md) med PowerShell och Azure Resource Manager-distributionsmodellen.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
