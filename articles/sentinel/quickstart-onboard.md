---
title: Publicera i Azure Sentinel | Microsoft Docs
description: Lär dig hur du samlar in data i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 7f042cfe10bd8ca57d9a2dae511a13a82f053a67
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316815"
---
# <a name="on-board-azure-sentinel"></a>Fordonsbaserad Azure Sentinel



I den här snabb starten får du lära dig hur du kan använda Azure Sentinel på kort. 

För att kunna använda Azure Sentinel måste du först aktivera Azure Sentinel och sedan ansluta dina data källor. Azure Sentinel levereras med ett antal anslutningar för Microsoft-lösningar, som är tillgängliga direkt och ger real tids integrering, inklusive Microsoft Threat Protection-lösningar, Microsoft 365 källor, inklusive Office 365, Azure AD, Azure ATP och Microsoft Cloud App Security med mera. Dessutom finns det inbyggda anslutnings program till det bredare säkerhets eko systemet för lösningar som inte kommer från Microsoft. Du kan också använda vanliga händelse format, syslog eller REST-API för att ansluta dina data källor med Azure Sentinel.  

När du har anslutit dina data källor väljer du från ett galleri med arbets böcker som skapats av experten som är baserade på dina data. Dessa arbets böcker kan enkelt anpassas efter dina behov.


## <a name="global-prerequisites"></a>Globala krav

- Aktiv Azure-prenumeration, om du inte har en, kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Log Analytics arbets yta. Lär dig hur du [skapar en arbets yta för Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Mer information om Log Analytics-arbetsytor finns i [utforma distributioner av Azure Monitors loggar](../azure-monitor/platform/design-logs-deployment.md).

-  Om du vill aktivera Azure Sentinel måste du ha deltagar behörighet till den prenumeration där Azure Sentinel-arbetsytan finns. 
- Om du vill använda Azure Sentinel behöver du antingen deltagar-eller läsar behörighet för resurs gruppen som arbets ytan tillhör.
- Ytterligare behörigheter kan krävas för att ansluta till vissa data källor.
- Azure Sentinel är en betald tjänst. Pris information finns i [om Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## Aktivera Azure Sentinel<a name="enable"></a>

1. Gå till Azure Portal.
2. Se till att den prenumeration där Azure Sentinel har skapats har valts. 
3. Sök efter Azure Sentinel. 
   ![Sök](./media/quickstart-onboard/search-product.png)

1. Klicka på **+ Lägg till**.
1. Välj den arbets yta som du vill använda eller skapa en ny. Du kan köra Azure Sentinel på fler än en arbets yta, men data isoleras till en enda arbets yta.

   ![sök](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Standard arbets ytor som skapats av Azure Security Center visas inte i listan. Du kan inte installera Azure Sentinel på dem.
   > - Azure Sentinel kan köras på arbets ytor i alla [ga-regioner med Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) förutom regionerna Kina, tyskland och Azure Government. Data som genereras av Azure Sentinel (t. ex. incidenter, bok märken och varnings regler som kan innehålla vissa kund uppgifter från dessa arbets ytor) sparas antingen i Västeuropa (för arbets ytor i Europa) eller östra USA (för alla USA-baserade arbets ytor, samt alla andra regioner utom Europa).

6. Klicka på **Lägg till Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Ansluta till datakällor

Azure Sentinel skapar anslutningen till tjänster och appar genom att ansluta till tjänsten och vidarebefordra händelser och loggar till Azure Sentinel. För datorer och virtuella datorer kan du installera Azure Sentinel-agenten som samlar in loggarna och vidarebefordrar dem till Azure Sentinel. För brand väggar och proxyservrar använder Azure Sentinel en Linux Syslog-server. Agenten installeras på den och där agenten samlar in loggfilerna och vidarebefordrar dem till Azure Sentinel. 
 
1. Klicka på **data insamling**.
2. Det finns en panel för varje data källa som du kan ansluta.<br>
Klicka till exempel på **Azure Active Directory**. Om du ansluter den här data källan strömmar du alla loggar från Azure AD till Azure Sentinel. Du kan välja vilken typ av loggar du vill använda för att få inloggnings loggar och/eller gransknings loggar. <br>
I det nedre avsnittet ger Azure Sentinel rekommendationer för vilka arbets böcker du bör installera för varje koppling så att du snabbt kan få intressanta insikter över dina data. <br> Följ installations anvisningarna eller [Läs den relevanta anslutnings guiden](connect-data-sources.md) för mer information. Information om data anslutningar finns i [ansluta Microsoft-tjänster](connect-data-sources.md).

När dina data källor är anslutna börjar dina data strömma till Azure Sentinel och är redo att börja arbeta med. Du kan visa loggarna på de [inbyggda instrument panelerna](quickstart-get-visibility.md) och börja skapa frågor i Log Analytics för att [undersöka data](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter data källor till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- Strömma data från [vanliga fel formats enheter](connect-common-event-format.md) till Azure Sentinel.
