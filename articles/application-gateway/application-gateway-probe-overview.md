---
title: Översikt för hälsoövervakning för Azure Application Gateway
description: Lär dig mer om övervakningsfunktionerna i Azure Application Gateway
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 8/6/2018
ms.author: victorh
ms.openlocfilehash: d0c425bcb9961fde9fb319991148c18c6a9ff57b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120558"
---
# <a name="application-gateway-health-monitoring-overview"></a>Application Gateway översikt över hälsoövervakning

Azure Application Gateway som standard övervakar hälsotillståndet för alla resurser i dess backend-poolen och att ta bort automatiskt alla resurser som anses vara felaktigt från poolen. Application Gateway fortsätter att övervaka de felaktiga instanserna och lägger till dem till Felfri backend-poolen när de blir tillgängliga och svara på hälsokontroller av slutpunkter. Programgateway skickar hälsoavsökningar med samma port som definierats i backend-HTTP-inställningarna. Den här konfigurationen garanterar att avsökningen provar på samma port som kunder skulle använda för att ansluta till serverdelen.

![Application gateway probe exempel][1]

Förutom att använda standard hälsoövervakning för avsökning kan anpassa du också hälsoavsökningen så att det passar ditt program. I den här artikeln beskrivs både standard och anpassade hälsoavsökningar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Standard-hälsoavsökning

En application gateway konfigureras automatiskt en standard-hälsoavsökning när du inte konfigurerar någon konfiguration för anpassad avsökning. Beteendet övervakning fungerar genom att göra en HTTP-begäran till IP-adresser som har konfigurerats för backend poolen. För standard-avsökningar om serverdelens http-inställningar har konfigurerats för HTTPS, använder avsökningen HTTPS samt för att kontrollera hälsotillståndet för serverdelen.

Exempel: Du kan konfigurera din application gateway om du vill använda backendservrar A, B och C för att ta emot HTTP-trafik på port 80. Standard hälsoövervakning testar tre servrar med 30 sekunders mellanrum för ett felfritt HTTP-svar. En felfri HTTP-svar har en [statuskod](https://msdn.microsoft.com/library/aa287675.aspx) mellan 200 och 399.

Om kontrollen för standard-avsökningen misslyckas Server A application gateway tas den bort från dess backend-poolen och nätverkstrafik slutar flöda till den här servern. Standard-avsökningen fortsätter ändå att söka efter servern en med 30 sekunders mellanrum. När server A svarar har en begäran från en standard-hälsoavsökning, läggs det tillbaka som felfria till backend poolen och trafik börjar flöda till servern igen.

### <a name="probe-matching"></a>Avsökning för matchning

Som standard anses vara ett HTTP (S)-svar med statuskod mellan 200 och 399 felfritt. Anpassade hälsoavsökningar har dessutom stöd för två matchande kriterier. Matchar villkoren kan användas för att ändra standard tolkningen av vad som utgör ett felfritt svar.

Följande matchar villkoren: 

- **HTTP-svar status kod matchning** - avsökningen som matchar villkoret för att acceptera användarspecificerade HTTP-svar kod eller ett svar kod intervall. Enskilda kommaavgränsad svarsstatuskoder eller ett intervall med statuskod stöds.
- **HTTP-svar brödtext matchar** - avsökningen som matchar villkoret som ser ut på HTTP-svarstext och matchningar med en användare angav sträng. Matcha endast söker efter förekomst av användare som anges strängen i svarstexten och är inte en fullständig vanlig uttrycksmatchning.

Matchar de villkor som kan anges med hjälp av den `New-AzApplicationGatewayProbeHealthResponseMatch` cmdlet.

Exempel:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
När de matchar de villkor som anges, den kan kopplas för att avsöka konfiguration med hjälp av en `-Match` parameter i PowerShell.

### <a name="default-health-probe-settings"></a>Standardinställningar för health-avsökning

| Avsökningen egenskapen | Värde | Beskrivning |
| --- | --- | --- |
| Avsökningswebbadress |http://127.0.0.1:\<port\>/ |URL-sökväg |
| Intervall |30 |Hur lång tid i sekunder som ska förflyta innan nästa hälsoavsökningen skickas.|
| Time-out |30 |Hur lång tid i sekunder application gateway probe svar inväntas innan du markerar avsökningen som skadad. Om en avsökning returnerar felfri, markeras direkt motsvarande serverdelen som felfritt.|
| Tröskelvärde för Ej felfri |3 |Styr hur många avsökningar för att skicka om det uppstår ett fel av regelbundna hälsoavsökningen. Dessa ytterligare hälsotillståndsavsökningar skickas i snabb följd att fastställa hälsotillståndet för serverdelen snabbt och väntar inte tills avsökningsintervallet. Backend-server markeras när antalet upprepade fel når tröskelvärde för ej felfri. |

> [!NOTE]
> Porten är samma port som backend-HTTP-inställningarna.

Standard-avsökningen tittar bara på http:\//127.0.0.1:\<port\> fastställa hälsostatus. Om du vill konfigurera hälsoavsökningen för att gå till en anpassad URL eller ändra andra inställningar kan använda du anpassade avsökningar.

### <a name="probe-intervals"></a>Avsökningsintervall

Alla instanser av Application Gateway probe serverdelen oberoende av varandra. Samma avsökningskonfigurationen gäller för varje Application Gateway-instans. Till exempel om avsökningskonfigurationen är att skicka hälsokontroller av slutpunkter med 30 sekunders mellanrum och application gateway har två instanser, skicka sedan båda instanserna hälsoavsökningen med 30 sekunders mellanrum.

Även om det finns flera lyssnare, avsökningar sedan varje lyssnare serverdelen oberoende av varandra. Till exempel om det finns två lyssnare som pekar på samma backend-pool på två olika portar (konfigureras med två serverdelens http-inställningar) avsökningar sedan varje lyssnare samma serverdel oberoende av varandra. I det här fallet finns det två avsökningar från varje application gateway-instans för två lyssnare. Om det finns två instanser av application gateway i det här scenariot, visas den virtuella datorn på serversidan fyra avsökningar per konfigurerade avsökningsintervallet.

## <a name="custom-health-probe"></a>Anpassade hälsoavsökning

Anpassade avsökningar kan du ha en mer detaljerad kontroll över övervakning av hälsotillstånd. När du använder anpassade avsökningar kan konfigurera du avsökningsintervallet, URL: en och sökväg för att testa och hur många misslyckade svar att godkänna innan du markerar backend-poolen-instans som skadad.

### <a name="custom-health-probe-settings"></a>Inställningar för anpassade hälsotillstånd avsökning

Följande tabell innehåller definitioner för egenskaperna för en anpassad hälsoavsökning.

| Avsökningen egenskapen | Beskrivning |
| --- | --- |
| Namn |Namnet på avsökningen. Det här namnet används för att referera till avsökning i backend-HTTP-inställningar. |
| Protokoll |Protokoll som används för att skicka avsökningen. Avsökningen använder protokollet som definieras i backend-HTTP-inställningar |
| Värd |Värdnamn för att skicka avsökningen. Gäller endast när flera platser har konfigurerats på Application Gateway, annars använda ”127.0.0.1”. Det här värdet skiljer sig från den virtuella datorns värdnamn. |
| Sökväg |Relativa sökvägen för avsökningen. Giltig sökväg som börjar med ”/”. |
| Intervall |Avsökningsintervall i sekunder. Det här värdet är tidsintervallet mellan två på varandra följande avsökningar. |
| Time-out |Avsökning tidsgräns i sekunder. Om ett giltigt svar inte tas emot inom denna tidsgräns, markeras avsökningen som misslyckat.  |
| Tröskelvärde för Ej felfri |Avsökning för antal nya försök. Backend-server markeras när antalet upprepade fel når tröskelvärde för ej felfri. |

> [!IMPORTANT]
> Om Application Gateway har konfigurerats för en enda plats, som standard värden anges namnet som 127.0.0.1, såvida inte annat har konfigurerats i anpassad avsökning.
> För referens för en anpassad avsökning skickas till \<protokollet\>://\<värden\>:\<port\>\<sökvägen\>. Den port som används kommer att samma port som definierats i backend-HTTP-inställningarna.

## <a name="nsg-considerations"></a>NSG-överväganden

Om det finns en nätverkssäkerhetsgrupp (NSG) i programgatewayundernät, måste portintervall 65503 65534 vara öppna på application gateway-undernätet för inkommande trafik. Dessa portar är obligatoriska för serverdelens hälsotillstånd API som fungerar.

Dessutom går inte att blockera utgående Internet-anslutning och inkommande trafik som kommer från taggen AzureLoadBalancer måste tillåtas.

## <a name="next-steps"></a>Nästa steg
När du läst om Application Gateway hälsoövervakning kan du konfigurera en [anpassade hälsoavsökning](application-gateway-create-probe-portal.md) i Azure-portalen eller en [anpassade hälsoavsökning](application-gateway-create-probe-ps.md) med PowerShell och Azure Resource Manager distributionsmodell.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
