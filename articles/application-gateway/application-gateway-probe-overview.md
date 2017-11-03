---
title: "Översikt för hälsoövervakning för Azure Programgateway | Microsoft Docs"
description: "Lär dig mer om övervakningsfunktionerna i Azure Programgateway"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7eeba328-bb2d-4d3e-bdac-7552e7900b7f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2016
ms.author: davidmu
ms.openlocfilehash: 83a0b1be1aba48146aa1aaedb36ad9d9d23f17d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
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

### <a name="default-health-probe-settings"></a>Standardinställningarna hälsa avsökning

| Avsökningen egenskapen | Värde | Beskrivning |
| --- | --- | --- |
| URL för webbavsökning |http://127.0.0.1:\<port\>/ |URL-sökväg |
| intervall |30 |Avsökningsintervall i sekunder |
| Timeout |30 |Avsökningen tidsgräns i sekunder |
| Tröskelvärde för ohälsosamt värde |3 |Avsökning för antal nya försök. Backend-server markeras när efterföljande avsökningen Felberäkning når tröskelvärde för ohälsosamt värde. |

> [!NOTE]
> Porten är samma port som backend-HTTP-inställningar.

Standard-avsökning kontrollerar endast http://127.0.0.1:\<port\> att fastställa hälsostatus. Om du behöver konfigurera hälsoavsökningen för att gå till en anpassad URL eller ändra andra inställningar måste du använda anpassade avsökningar enligt beskrivningen i följande steg:

## <a name="custom-health-probe"></a>Anpassade hälsoavsökningen

Anpassade avsökningar kan du ha en mer detaljerad kontroll över övervakning av hälsotillstånd. När du använder anpassade avsökningar, kan du konfigurera avsökningsintervall, URL: en och sökvägen för att testa och hur många misslyckade svar att godkänna innan du markerar backend-pool-instans som ohälsosamt.

### <a name="custom-health-probe-settings"></a>Inställningar för anpassade hälsotillstånd avsökning

Följande tabell innehåller definitioner för egenskaperna för en anpassad hälsoavsökningen.

| Avsökningen egenskapen | Beskrivning |
| --- | --- |
| Namn |Namnet på avsökningen. Det här namnet används för att referera till avsökning i backend-HTTP-inställningar. |
| Protokoll |Protokoll som används för att skicka avsökningen. Avsökningen används protokollet som definieras i backend-HTTP-inställningar |
| Värd |Värdnamn för att skicka avsökningen. Gäller endast när flera platser har konfigurerats på Application Gateway, Använd annars ”127.0.0.1”. Det här värdet skiljer sig från den virtuella datorns värdnamn. |
| Sökväg |Avsökningen relativ sökväg. Ogiltig sökväg startar från '/'. |
| intervall |Avsökning intervall i sekunder. Det här värdet är tidsintervallet mellan två på varandra följande avsökningar. |
| Timeout |Avsökning tidsgräns i sekunder. Om ett giltigt svar inte emot inom denna tidsgräns, markeras avsökningen som misslyckad.  |
| Tröskelvärde för ohälsosamt värde |Avsökning för antal nya försök. Backend-server markeras när efterföljande avsökningen Felberäkning når tröskelvärde för ohälsosamt värde. |

> [!IMPORTANT]
> Om Application Gateway har konfigurerats för en viss plats, som standard värden ska namn anges som 127.0.0.1, såvida inte annat konfigurerade i anpassade avsökning.
> För referens en anpassad avsökning skickas till \<protokollet\>://\<värden\>:\<port\>\<sökvägen\>. Den port som används ska vara samma port som definierats i backend-HTTP-inställningar.

## <a name="next-steps"></a>Nästa steg
När du lära dig mer om övervakning av hälsotillstånd för Programgateway, kan du konfigurera en [anpassade hälsoavsökningen](application-gateway-create-probe-portal.md) i Azure-portalen eller en [anpassade hälsoavsökningen](application-gateway-create-probe-ps.md) med PowerShell och Azure Resource Manager distributionsmodell.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
