---
title: 'Snabbstart: Ombord i Azure Sentinel'
description: Lär dig hur du samlar in data i Azure Sentinel genom att följa det här snabbstartsdokumentet.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: 11fecd875385d8ba044cbe44e2270eed11d61ce1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77581557"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Snabbstart: Ombord Azure Sentinel

I den här snabbstarten får du lära dig hur du är inbyggd i Azure Sentinel. 

För att kunna vara med i Azure Sentinel måste du först aktivera Azure Sentinel och sedan ansluta dina datakällor. Azure Sentinel levereras med ett antal anslutningsappar för Microsoft-lösningar, tillgängliga direkt och ger integrering i realtid, inklusive Microsoft Threat Protection-lösningar, Microsoft 365-källor, inklusive Office 365, Azure AD, Azure ATP och Microsoft Cloud App Security med mera. Dessutom finns det inbyggda kopplingar till det bredare säkerhetsekosystemet för lösningar som inte kommer från Microsoft. Du kan också använda vanligt händelseformat, Syslog eller REST-API för att ansluta dina datakällor till Azure Sentinel.  

När du har anslutit dina datakällor väljer du från ett galleri med sakkunnigt skapade arbetsböcker som visar insikter baserat på dina data. Dessa arbetsböcker kan enkelt anpassas efter dina behov.

>[!IMPORTANT] 
> Information om de avgifter som uppkommer när du använder Azure Sentinel finns i [Azure Sentinel-priser](https://azure.microsoft.com/pricing/details/azure-sentinel/).
  

## <a name="global-prerequisites"></a>Globala förutsättningar

- Active Azure-prenumeration, om du inte har en, skapa ett [gratis konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Logga Analytics-arbetsyta. Läs om hur du [skapar en Log Analytics-arbetsyta](../log-analytics/log-analytics-quick-create-workspace.md). Mer information om Log Analytics-arbetsytor finns i [Designa distributionen av Azure Monitor Logs](../azure-monitor/platform/design-logs-deployment.md).

- För att aktivera Azure Sentinel behöver du deltagarbehörigheter till prenumerationen där Azure Sentinel-arbetsytan finns. 
- Om du vill använda Azure Sentinel behöver du antingen deltagar- eller läsarbehörigheter för resursgruppen som arbetsytan tillhör.
- Ytterligare behörigheter kan behövas för att ansluta specifika datakällor.
- Azure Sentinel är en betald tjänst. Prisinformation finns i [Om Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## <a name="enable-azure-sentinel"></a>Aktivera Azure Sentinel<a name="enable"></a>

1. Logga in på Azure Portal. Kontrollera att prenumerationen där Azure Sentinel skapas har valts.

1. Sök efter och välj **Azure Sentinel**.

   ![sök](./media/quickstart-onboard/search-product.png)

1. Välj **Lägg till**.

1. Markera den arbetsyta som du vill använda eller skapa en ny. Du kan köra Azure Sentinel på mer än en arbetsyta, men data är isolerade till en enda arbetsyta.

   ![sök](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Standardarbetsytor som skapats av Azure Security Center visas inte i listan. Du kan inte installera Azure Sentinel på dem.
   > - Azure Sentinel kan köras på arbetsytor i alla [GA-regioner i Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) utom regionerna Kina, Tyskland och Azure Government. Data som genereras av Azure Sentinel (till exempel incidenter, bokmärken och varningsregler, som kan innehålla vissa kunddata som kommer från dessa arbetsytor) sparas antingen i Västeuropa (för arbetsytor i Europa) eller östra USA (för alla USA-baserade arbetsytor, samt någon annan region utom Europa).

1. Välj **Lägg till Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Ansluta till datakällor

Azure Sentinel skapar anslutningen till tjänster och appar genom att ansluta till tjänsten och vidarebefordra händelser och loggar till Azure Sentinel. För datorer och virtuella datorer kan du installera Azure Sentinel-agenten som samlar loggarna och vidarebefordrar dem till Azure Sentinel. För brandväggar och proxyservrar använder Azure Sentinel en Linux Syslog-server. Agenten är installerad på den och från vilken agenten samlar in loggfilerna och vidarebefordrar dem till Azure Sentinel. 
 
1. Klicka på **Datainsamling**.
2. Det finns en panel för varje datakälla som du kan ansluta.<br>
Klicka till exempel på **Azure Active Directory**. Om du ansluter den här datakällan skickar du alla loggar från Azure AD till Azure Sentinel. Du kan välja vilken typ av loggar du wan att få - inloggningsloggar och / eller granskningsloggar. <br>
Längst ned ger Azure Sentinel rekommendationer för vilka arbetsböcker du bör installera för varje anslutningsapp så att du omedelbart kan få intressanta insikter över dina data. <br> Följ installationsanvisningarna eller [se relevant anslutningsguide](connect-data-sources.md) för mer information. Information om dataanslutningar finns i [Ansluta Microsoft-tjänster](connect-data-sources.md).

När dina datakällor är anslutna börjar dina data strömma till Azure Sentinel och är redo för dig att börja arbeta med. Du kan visa loggarna i de [inbyggda instrumentpanelerna](quickstart-get-visibility.md) och börja skapa frågor i Log Analytics för att [undersöka data](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Nästa steg
I det här dokumentet fick du lära dig mer om hur du ansluter datakällor till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- Strömma data från [vanliga händelseformatinstallationer](connect-common-event-format.md) till Azure Sentinel.
