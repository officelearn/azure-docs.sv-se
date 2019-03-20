---
title: Publicera Azure Sentinel förhandsversion | Microsoft Docs
description: Lär dig mer om att samla in data i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/12/2019
ms.author: rkarlin
ms.openlocfilehash: 30f6d08594bdf2e5d78bc8c7881a135d6e57f397
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852240"
---
# <a name="on-board-azure-sentinel-preview"></a>Integrera Azure Sentinel-förhandsversion

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här snabbstarten lär du dig hur du integrera Azure Sentinel. 

Att integrera Azure Sentinel måste du först ansluta till dina datakällor. Azure Sentinel levereras med ett antal anslutningsappar för Microsoft-lösningar, tillgängliga utanför rutan och ge i realtid integration, inklusive Microsoft Threat Protection lösningar, Microsoft 365 källor, till exempel Office 365, Azure AD, Azure ATP och Microsoft Cloud App Security, och mycket mer. Det finns dessutom inbyggda anslutningar till bredare säkerhetsekosystemet för icke-Microsoft-lösningar. Du kan också använda common event format, Syslog eller REST-API för att ansluta dina datakällor med Sentinel-Azure.  

När du ansluter dina datakällor kan du välja från ett galleri med flytta skapade instrumentpaneler som lyfter fram insikter utifrån dina data. Dessa instrumentpaneler kan lätt anpassas efter dina behov.


## <a name="global-prerequisites"></a>Globala krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Log Analytics-arbetsyta. Lär dig hur du [skapa en Log Analytics-arbetsyta](../log-analytics/log-analytics-quick-create-workspace.md)

- Deltagarbehörighet till din klient för att aktivera Sentinel-Azure

- Klient som är global eller administratörsbehörigheter för säkerhet
 

## Aktivera Azure Sentinel <a name="enable"></a>

1. Gå till Azure-portalen.
2. Kontrollera att den prenumeration som Azure Sentinel skapas, har valts. 
3. Sök efter Azure Sentinel. 
   ![search](./media/quickstart-onboard/search-product.png)

1. Klicka på **+ Lägg till**.
1. Välj den arbetsyta som du vill använda eller skapa en ny. Du kan köra Azure Sentinel på fler än en arbetsyta, men data är isolerade i en enda arbetsyta.

   ![sök](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - **Arbetsytan, position** är det viktigt att förstå att alla data som strömmas till Azure Sentinel lagras i den geografiska platsen där du har valt.  
   > - Standardarbetsytor som skapats av Azure Security Center visas inte i listan. Du kan inte installera Azure Sentinel på dem.
   > - Azure Sentinel kan köras på arbetsytor som har distribuerats i någon av följande regioner:  Australien, sydöstra Australien, centrala Kanada, centrala Indien, östra USA, östra USA 2 – EUAP (Kanarieöarna), östra Japan, Sydostasien, Storbritannien, södra, västra Europa, västra USA 2.

6. Klicka på **lägga till Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Ansluta till datakällor

Azure Sentinel skapas en anslutning till tjänster och appar genom att ansluta till tjänsten och vidarebefordran av händelser och loggar till Sentinel-Azure. Du kan installera agenten Azure Sentinel som samlar in loggarna och vidarebefordrar dem till Azure Sentinel för datorer och virtuella datorer. För brandväggar och proxyservrar använder Sentinel-Azure en Linux Syslog-server. Agenten är installerad på den och från som agenten samlar in loggen filer och vidarebefordrar dem till Azure Sentinel. 
 
1. Klicka på **datainsamling**.
2. Det finns en panel för varje datakälla som du kan ansluta.<br>
Klicka till exempel **Azure Active Directory**. Om du ansluter den här datakällan kan strömma du alla loggar från Azure AD till Azure Sentinel. Du kan välja vilken typ av loggar du wan för att få - inloggning loggar och/eller granskningsloggar. <br>
Längst ned, du Azure Sentinel innehåller rekommendationer för vilka instrumentpaneler som du bör installera för varje koppling så att du kan omedelbart få intressanta insikter i dina data. <br> Följ installationsanvisningarna eller [i uppgraderingshandboken för aktuella](connect-data-sources.md) för mer information. Information om dataanslutningar finns i [ansluta Microsoft services](connect-data-sources.md).

När dina data som är anslutna källor, dina data börjar strömning i Azure Sentinel och är redo att börja arbeta med. Du kan visa loggar i den [inbyggda instrumentpaneler](quickstart-get-visibility.md) och börja skapa frågor i Log Analytics för att [undersöka data](tutorial-investigate-cases.md).




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig om att ansluta datakällor till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).
- Stream data från [vanliga fel Format installationer](connect-common-event-format.md) i Azure Sentinel.
