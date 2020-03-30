---
title: Azure CDN HTTP-råloggar
description: I den här artikeln beskrivs Azure CDN HTTP RAW-loggarna.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371637"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP-råloggar
Råloggar ger omfattande information om åtgärder och fel som är viktiga för granskning och felsökning. Råloggar skiljer sig från aktivitetsloggar. Aktivitetsloggar ger insyn i de åtgärder som utförs på Azure-resurser. Råloggar ger en post för resursens åtgärder.

> [!IMPORTANT]
> FUNKTIONEN HTTP-råloggar är tillgänglig för Azure CDN från Microsoft.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration"></a>Konfiguration

Så här konfigurerar du Raw-loggar för din Azure CDN från Microsoft-profilen: 

1. På Portal-menyn i Azure väljer du **Alla resurser** >> **\<>. **

2. Under **Övervakning** väljer du **Diagnostikinställningar**.

3. Välj **+ Lägg till diagnostikinställning**.

    ![Cdn-diagnostikinställning](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Råloggar är endast tillgängliga på profilnivå medan aggregerade http-statuskodsloggar är tillgängliga på slutpunktsnivå.

4. Under **Diagnostikinställningar**anger du ett namn för diagnostikinställningen under **Diagnostikinställningars namn**.

5. Markera **loggen** och ange kvarhållningen i dagar.

6. Välj **målinformation**. Målalternativen är:
    * **Skicka till Log Analytics**
        * Välj **arbetsytan Prenumeration** och **Logganalys**.
    * **Arkivera till ett lagringskonto**
        * Välj **prenumeration** och **lagringskonto**.
    * **Strömma till ett händelsenav**
        * Välj namnområde **för prenumeration,** **händelsehubb**, **händelsenavnamn (valfritt)** och **principnamn för händelsenav**.

    ![Cdn-diagnostikinställning](./media/cdn-raw-logs/raw-logs-02.png)

7. Välj **Spara**.

## <a name="raw-logs-properties"></a>Egenskaper för råloggar

Azure CDN från Microsoft Service tillhandahåller för närvarande Raw-loggar. Råloggar tillhandahåller enskilda API-begäranden med varje post med följande schema: 

| Egenskap              | Beskrivning                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Den unika referenssträngen som identifierar en begäran som betjänas av Ytterdörren, som också skickas som X-Azure-Ref-huvud till klienten. Krävs för att söka efter information i åtkomstloggarna för en viss begäran. |
| Mer från HttpMethod            | HTTP-metod som används av begäran.                                                                                                                                                                     |
| HttpVersion (på ett sätt)           | Typ av begäran eller anslutning.                                                                                                                                                                   |
| RequestUri (förfråganuri)            | URI för den mottagna begäran.                                                                                                                                                                         |
| RequestBytes          | Storleken på HTTP-begäran meddelandet i byte, inklusive begäran rubriker och begäran kroppen.                                                                                                   |
| ResponseBytes         | Byte som skickas av serverdservern som svar.                                                                                                                                                    |
| Useragent             | Den webbläsartyp som klienten använde.                                                                                                                                                               |
| ClientIp              | IP-adressen för klienten som gjorde begäran.                                                                                                                                                  |
| Tidskörd             | Den tid som åtgärden vidtog, i millisekunder.                                                                                                                                            |
| SäkerhetProtocol      | TLS/SSL-protokollversionen som används av begäran eller null om ingen kryptering.                                                                                                                           |
| Slutpunkt              | CDN-slutpunktsvärden har konfigurerats under den överordnade CDN-profilen.                                                                                                                                   |
| Värdnamn för namn på bakåtsträvning     | Namnet på den serverdvärd eller ursprung där begäranden skickas.                                                                                                                                |
| Skickas till ursprung sköld | Om sant, betyder det att begäran besvarades från ursprung sköld cache i stället för kanten pop. Origin shield är en överordnad cache som används för att förbättra cache träffförhållande.                                       |
| HttpStatusCode (HttpStatusCode)        | HTTP-statuskoden returnerades från proxyn.                                                                                                                                                        |
| HttpStatusDetails     | Resulterande status på begäran. Innebörden av det här strängvärdet finns i en statusreferenstabell.                                                                                              |
| Pop                   | Kant pop, som svarade på användarens begäran. PO: s förkortningar är flygplatskoder för deras respektive tunnelbanor.                                                                                   |
| Cachestatus          | Anger om objektet returnerades från cacheminnet eller kom från ursprunget.                                                                                                             |
> [!IMPORTANT]
> Funktionen HTTP Raw-loggar är automatiskt tillgänglig för alla profiler som skapas eller uppdateras efter **den 25 februari 2020**. För CDN-profiler som skapats tidigare bör man uppdatera CDN-slutpunkten efter att ha konfigurerat loggning. Man kan till exempel navigera till geofiltrering under CDN-slutpunkter och blockera alla länder som inte är relevanta för deras arbetsbelastning och träffar spara. 

> [!NOTE]
> Loggarna kan visas under din Log Analytics-profil genom att köra en fråga. En exempelfråga skulle se ut som AzureDiagnostics | där kategori == "AzureCdnAccessLog"

## <a name="next-steps"></a>Efterföljande moment
I den här artikeln har du aktiverat HTTP-råloggar för Microsoft CDN-tjänsten.

Mer information om Azure CDN och andra Azure-tjänster som nämns i den här artikeln finns i:

* [Analysera](cdn-log-analysis.md) Azure CDN-användningsmönster.

* Läs mer om [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Konfigurera [logganalys i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
