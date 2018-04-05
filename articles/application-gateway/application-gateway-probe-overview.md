---
title: Översikt för hälsoövervakning för Azure Programgateway
description: Lär dig mer om övervakningsfunktionerna i Azure Programgateway
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/30/2018
ms.author: victorh
ms.openlocfilehash: 2f62f01c1178f9529eb46051f088affccc5279a7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="application-gateway-health-monitoring-overview"></a>Gateway hälsa övervakning Programöversikt

Azure Application Gateway som standard övervakar tillståndet för alla resurser i dess backend-pool och tar automatiskt bort alla resurser som bedöms som dåligt från poolen. Programgateway fortsätter att övervaka ohälsosamt instanser och lägger till dem tillbaka till Felfri backend-poolen när de blir tillgängliga och svara på hälsoavsökningar. Programgateway skickar hälsoavsökning med samma port som har definierats i backend-HTTP-inställningar. Den här konfigurationen garanterar att sonden provar samma port som kunder använder skulle ansluta till serverdelen.

![exempel på gateway avsökning][1]

Du kan också anpassa hälsoavsökningen så att de passar din programkrav förutom att använda standard hälsoövervakning för avsökning. I den här artikeln omfattar både standard- och anpassade hälsoavsökningar.

> [!NOTE]
> Om det finns en NSG på Application Gateway-undernät, bör portintervall 65503 65534 öppnas i Application Gateway-undernät för inkommande trafik. Dessa portar är obligatoriska för backend-hälsa API för att fungera.

## <a name="default-health-probe"></a>Standard hälsoavsökningen

En Programgateway konfigureras automatiskt en standard hälsoavsökningen när du inte konfigurera eventuella anpassade avsökningen konfiguration. Beteendet övervakning fungerar genom att göra en HTTP-begäran till IP-adresser som har konfigurerats för backend-poolen. För standard-avsökningar om serverdelens http-inställningar som är konfigurerade för HTTPS, använder avsökningen HTTPS samt för att kontrollera hälsotillståndet för serverdelar.

Exempel: du konfigurerar din Programgateway att använda backend-servrar A, B och C som tar emot HTTP-nätverkstrafik på port 80. Standard-hälsoövervakning testar tre servrar med 30 sekunders mellanrum felfri HTTP-svar. En felfri HTTP-svar har en [statuskod](https://msdn.microsoft.com/library/aa287675.aspx) mellan 200 och 399.

Om standard avsökningen kontrollen misslyckas Server A programgatewayen tas den bort från dess backend-adresspool och nätverkstrafik slutar flöda till den här servern. Standard-avsökning fortsätter ändå att kontrollera för servern en var 30: e sekund. När server A svarar har en begäran från en standard hälsoavsökningen, läggs den tillbaka som felfri till backend-poolen och trafiken börjar flöda till servern igen.

### <a name="probe-matching"></a>Avsökning matchar

Som standard anses en HTTP (S)-respons med statuskod 200 felfritt. Anpassade hälsoavsökningar har dessutom stöd för två matchande villkor. Matchar villkoren kan användas för att du kan också ändra standardtolkning av vilka consititutes felfri svar.

Följande matchar villkoren: 

- **HTTP-svar status koden matchar** - avsökningen som matchar villkoret för att acceptera HTTP-svar kod eller svar kod områden angavs. Enskilda kommaseparerade statuskoder svar eller ett intervall med statuskoden stöds.
- **HTTP-svar brödtext matchar** - avsökning matchningsvillkor som ser ut på brödtext för HTTP-svar och matchar med en användare angett sträng. Observera att matchningen ser endast om användaren angett sträng i brödtext för svar och är inte en fullständig reguljärt uttryck för matchning.

Matchningsvillkor kan anges med hjälp av den `New-AzureRmApplicationGatewayProbeHealthResponseMatch` cmdlet.

Exempel:

```
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
När matchningsvillkor anges den kan kopplas till avsökning för konfiguration av en `-Match` parameter i PowerShell.

### <a name="default-health-probe-settings"></a>Standardinställningarna hälsa avsökning

| Avsökningen egenskapen | Värde | Beskrivning |
| --- | --- | --- |
| Avsökningswebbadress |http://127.0.0.1:\<port\>/ |URL-sökväg |
| Intervall |30 |Avsökningsintervall i sekunder |
| Timeout |30 |Avsökningen tidsgräns i sekunder |
| Tröskelvärde för ohälsosamt värde |3 |Avsökning för antal nya försök. Backend-server markeras när efterföljande avsökningen Felberäkning når tröskelvärde för ohälsosamt värde. |

> [!NOTE]
> Porten är samma port som backend-HTTP-inställningar.

Standard-avsökning kontrollerar endast http://127.0.0.1: \<port\> att fastställa hälsostatus. Om du behöver konfigurera hälsoavsökningen för att gå till en anpassad URL eller ändra andra inställningar måste du använda anpassade avsökningar enligt beskrivningen i följande steg:

## <a name="custom-health-probe"></a>Anpassade hälsoavsökningen

Anpassade avsökningar kan du ha en mer detaljerad kontroll över övervakning av hälsotillstånd. När du använder anpassade avsökningar, kan du konfigurera avsökningsintervall, URL: en och sökvägen för att testa och hur många misslyckade svar att godkänna innan du markerar backend-pool-instans som ohälsosamt.

### <a name="custom-health-probe-settings"></a>Inställningar för anpassade hälsotillstånd avsökning

Följande tabell innehåller definitioner för egenskaperna för en anpassad hälsoavsökningen.

| Avsökningen egenskapen | Beskrivning |
| --- | --- |
| namn |Namnet på avsökningen. Det här namnet används för att referera till avsökning i backend-HTTP-inställningar. |
| Protokoll |Protokoll som används för att skicka avsökningen. Avsökningen används protokollet som definieras i backend-HTTP-inställningar |
| Värd |Värdnamn för att skicka avsökningen. Gäller endast när flera platser har konfigurerats på Application Gateway, Använd annars ”127.0.0.1”. Det här värdet skiljer sig från den virtuella datorns värdnamn. |
| Sökväg |Avsökningen relativ sökväg. Ogiltig sökväg startar från '/'. |
| Intervall |Avsökning intervall i sekunder. Det här värdet är tidsintervallet mellan två på varandra följande avsökningar. |
| Timeout |Avsökning tidsgräns i sekunder. Om ett giltigt svar inte emot inom denna tidsgräns, markeras avsökningen som misslyckad.  |
| Tröskelvärde för ohälsosamt värde |Avsökning för antal nya försök. Backend-server markeras när efterföljande avsökningen Felberäkning når tröskelvärde för ohälsosamt värde. |

> [!IMPORTANT]
> Om Application Gateway har konfigurerats för en viss plats, som standard värden ska namn anges som 127.0.0.1, såvida inte annat konfigurerade i anpassade avsökning.
> För referens en anpassad avsökning skickas till \<protokollet\>://\<värden\>:\<port\>\<sökvägen\>. Den port som används ska vara samma port som definierats i backend-HTTP-inställningar.

## <a name="next-steps"></a>Nästa steg
När du lära dig mer om övervakning av hälsotillstånd för Programgateway, kan du konfigurera en [anpassade hälsoavsökningen](application-gateway-create-probe-portal.md) i Azure-portalen eller en [anpassade hälsoavsökningen](application-gateway-create-probe-ps.md) med PowerShell och Azure Resource Manager distributionsmodell.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
