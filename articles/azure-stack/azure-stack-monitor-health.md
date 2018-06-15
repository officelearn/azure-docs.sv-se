---
title: Övervaka hälsotillstånd och aviseringar i Azure-stacken | Microsoft Docs
description: Lär dig att övervaka hälsotillstånd och aviseringar i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 69901c7b-4673-4bd8-acf2-8c6bdd9d1546
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: mabrigg
ms.openlocfilehash: 446df7922422ccfcf3fbb92ecf153c6dec2f6197
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
ms.locfileid: "26640386"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Övervaka hälsotillstånd och aviseringar i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure-stacken innehåller funktioner som gör att du kan visa hälso- och aviseringar för en Azure-Stack-region för infrastrukturövervakning. Den **Region management** sida vid sida, Fäst som standard i administratörsportalen för providern standardabonnemang visas alla distribuerade områden i Azure-stacken. Panelen visar antalet aktiva aviseringar för varje region som kritiska meddelanden och varningsmeddelanden och är ingången till hälsa och avisering funktioner i Azure-stacken.

 ![Panelen Region Management](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Förstå hälsotillstånd i Azure-stacken

 Hälsa och aviseringar hanteras av resursprovidern hälsa. Azure Stack infrastrukturkomponenter registrera med resursprovidern hälsa under Azure Stack-distribution och konfiguration. Den här registreringen gör det möjligt för visning av hälsotillstånd och aviseringar för varje komponent. Hälsotillstånd i Azure-stacken är ett enkelt begrepp. Om det finns aviseringar för en registrerad instans av en komponent, visar hälsotillståndet för komponenten den sämsta active allvarlighetsgraden; varning eller kritisk.
 
 ## <a name="view-and-manage-component-health-state"></a>Visa och hantera komponentens hälsotillstånd
 
 Som operatör Azure Stack kan visa du hälsotillståndet för komponenter i administratörsportalen och via REST-API och PowerShell.
 
Om du vill visa hälsotillståndet i portalen klickar du på den region som du vill visa i den **Region management** panelen. Du kan visa hälsotillståndet för infrastrukturroller och resursproviders.

![Lista över infrastrukturroller](media/azure-stack-monitor-health/image2.png)

Du kan klicka på en resursprovidern eller infrastrukturrollen om du vill visa mer detaljerad information.

> [!WARNING]
>Om du klickar du på en infrastruktur för roll och klicka sedan på instansen, finns det alternativ för att starta, starta om eller avstängning. Använd inte dessa åtgärder när du tillämpar uppdateringar på ett integrerat system. Dessutom gör **inte** Använd alternativen i en Azure-stacken Development Kit-miljö. Dessa alternativ är utvecklade för en integrerad system-miljö där det finns fler än en instans av serverroll per infrastrukturrollen. Starta om rollinstansen (särskilt AzS-Xrp01) i development kit gör att systemet är instabilt. Felsökningshjälp efter din fråga till den [Azure Stack-forum](https://aka.ms/azurestackforum).
>
 
## <a name="view-alerts"></a>Visa aviseringar

Lista över aktiva aviseringar för varje region för Azure-stacken är tillgängliga direkt från den **Region management** bladet. Den första ikonen i standardkonfigurationen är den **aviseringar** panelen som visar en sammanfattning av de viktiga och varningar för regionen. Du kan fästa panelen aviseringar som andra panelen på det här bladet på instrumentpanelen för snabb åtkomst.   

![Aviseringar panelen som visar en varning](media/azure-stack-monitor-health/image3.png)

Genom att välja den övre delen av den **aviseringar** panelen måste du gå till listan över alla aktiva varningar för regionen. Om du väljer antingen den **kritisk** eller **varning** artikel i panelen, navigera till en filtrerad lista över aviseringar (kritiskt eller varning). 

![Filtrerade varningsaviseringar](media/azure-stack-monitor-health/image4.png)
  
Den **aviseringar** bladet stöder möjligheten att filtrera både status (aktiv eller stängd) och allvarlighetsgrad (kritiskt eller varning). Standardvyn visar alla aktiva aviseringar. Alla stängda aviseringar tas bort från systemet efter sju dagar.

![Fönster för att filtrera efter kritiskt eller Varningsstatus](media/azure-stack-monitor-health/image5.png)

Den **visa API** åtgärd visar REST-API som användes för att generera listvyn. Den här åtgärden ger ett snabbt sätt att bekanta dig med REST API-syntax som du kan använda för att fråga aviseringar. Du kan använda denna API i automation eller för integrering med ditt befintliga datacenter övervakning, rapportering och biljetter lösningar. 

![Visa API-alternativet som visar REST API](media/azure-stack-monitor-health/image6.png)

Du kan klicka på en specifik avisering om du vill visa detaljerad information om aviseringen. Varningsinformationen visar alla fält som är associerade med aviseringen och aktivera snabb navigering till den komponent som berörs och källa. Följande avisering inträffar till exempel om en av rollinstanser infrastruktur kopplas från eller är inte tillgänglig.  

![Bladet aviseringsinformation](media/azure-stack-monitor-health/image7.png)

När infrastrukturen instansen är tillbaka online, stängs automatiskt den här aviseringen. Många, men inte alla aviseringen stängs automatiskt när det underliggande problemet har lösts. Vi rekommenderar att du väljer **Stäng avisering** när du har utfört steg. Om problemet kvarstår genererar Azure Stack en ny avisering. Om du har löst problemet aviseringen förblir stängd och kräver ingen ytterligare åtgärd.

## <a name="next-steps"></a>Nästa steg

[Hantera uppdateringar i Azure-stacken](azure-stack-updates.md)

[Regionhantering av i Azure-stacken](azure-stack-region-management.md)
