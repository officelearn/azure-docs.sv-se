---
title: Azure CDN HTTP RAW-loggar
description: I den här artikeln beskrivs Azure CDN HTTP RAW-loggar.
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: c6e8570746ae3dd0051dbec084c89d90580d28b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80371637"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP RAW-loggar
Obehandlade loggar innehåller omfattande information om åtgärder och fel som är viktiga för granskning och fel sökning. Obehandlade loggar skiljer sig från aktivitets loggar. Aktivitets loggarna ger insyn i de åtgärder som utförs på Azure-resurser. Obehandlade loggar innehåller en post med åtgärder för din resurs.

> [!IMPORTANT]
> Funktionen HTTP RAW-loggar är tillgänglig för Azure CDN från Microsoft.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration"></a>Konfiguration

Så här konfigurerar du obehandlade loggar för din Azure CDN från Microsoft-profilen: 

1. Från Azure Portal-menyn väljer du **alla resurser** >> **\<din-CDN-profil>**.

2. Under **Övervakning** väljer du **Diagnostikinställningar**.

3. Välj **+ Lägg till diagnostisk inställning**.

    ![Inställning av CDN-diagnostik](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Råa loggar är bara tillgängliga på profil nivå medan sammanställda HTTP-statuskod loggar är tillgängliga på slut punkts nivå.

4. Under **diagnostikinställningar**anger du ett namn för den diagnostiska inställningen under **diagnostiska**inställnings namn.

5. Välj **loggen** och ange kvarhållning i dagar.

6. Välj **mål information**. Mål alternativen är:
    * **Skicka till Log Analytics**
        * Välj **prenumerationen** och **Log Analytics arbets ytan**.
    * **Arkivera till ett lagrings konto**
        * Välj **prenumerationen** och **lagrings kontot**.
    * **Strömma till en Event Hub**
        * Välj **prenumeration**, **Event Hub-namnrymd**, **Event Hub-namn (valfritt)** och **princip namn för Event Hub**.

    ![Inställning av CDN-diagnostik](./media/cdn-raw-logs/raw-logs-02.png)

7. Välj **Spara**.

## <a name="raw-logs-properties"></a>Egenskaper för RAW-loggar

Azure CDN från Microsoft-tjänsten tillhandahåller för närvarande obehandlade loggar. Obehandlade loggar innehåller enskilda API-begäranden med varje post med följande schema: 

| Egenskap              | Beskrivning                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Den unika referens strängen som identifierar en begäran som betjänas av en front dörr, som också skickas som X-Azure-ref-huvud till klienten. Krävs för att söka efter information i åtkomst loggarna för en speciell begäran. |
| HttpMethod            | HTTP-metod som används av begäran.                                                                                                                                                                     |
| HttpVersion           | Typ av begäran eller anslutning.                                                                                                                                                                   |
| RequestUri            | URI för den mottagna begäran.                                                                                                                                                                         |
| RequestBytes          | Storleken på HTTP Request-meddelandet i byte, inklusive begärandehuvuden och begär ande texten.                                                                                                   |
| ResponseBytes         | Byte som skickats av backend-servern som svar.                                                                                                                                                    |
| UserAgent             | Webbläsarens typ som används av klienten.                                                                                                                                                               |
| ClientIp              | IP-adressen för klienten som gjorde begäran.                                                                                                                                                  |
| TimeTaken             | Hur lång tid åtgärden tog, i millisekunder.                                                                                                                                            |
| SecurityProtocol      | TLS/SSL-protokollets version som används av begäran eller null om ingen kryptering.                                                                                                                           |
| Slutpunkt              | CDN-slutpunktens värd har kon figurer ATS under den överordnade CDN-profilen.                                                                                                                                   |
| Server dels värd namn     | Namnet på backend-värden eller ursprunget där förfrågningar skickas.                                                                                                                                |
| Skickas till ursprungs sköld | Om värdet är true innebär det att begäran besvarades från ursprungs sköldens cacheminne i stället för Edge-pop. Ursprungs sköld är ett överordnat cacheminne som används för att förbättra förhållandet mellan cacheträffar.                                       |
| HttpStatusCode        | HTTP-statuskod som returnerades från proxyservern.                                                                                                                                                        |
| HttpStatusDetails     | Resulterande status för begäran. Innebörd av detta sträng värde finns i en status referens tabell.                                                                                              |
| Popup                   | Edge-pop, som svarade på användarens begäran. Pop-förkortningar är flyg plats koder för deras respektive Metros.                                                                                   |
| Cache-status          | Indikerar om objektet returnerades från cachen eller kom från ursprunget.                                                                                                             |
> [!IMPORTANT]
> Funktionen HTTP RAW-loggar är tillgänglig automatiskt för profiler som skapats eller uppdaterats efter **25 februari 2020**. För CDN-profiler som skapats tidigare bör du uppdatera CDN-slutpunkten när du har konfigurerat loggning. Till exempel kan en navigera till geo-filtrering under CDN-slutpunkter och blockera alla länder som inte är relevanta för deras arbets belastning och tryck på Spara. 

> [!NOTE]
> Loggarna kan visas under din Log Analytics profil genom att köra en fråga. En exempel fråga skulle se ut som AzureDiagnostics | där Category = = "AzureCdnAccessLog"

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du aktiverat HTTP RAW-loggar för Microsoft CDN-tjänsten.

Mer information om Azure CDN och de andra Azure-tjänsterna som nämns i den här artikeln finns i:

* [Analysera](cdn-log-analysis.md) Azure CDN användnings mönster.

* Läs mer om [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Konfigurera [Log Analytics i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
