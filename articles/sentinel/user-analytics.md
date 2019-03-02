---
title: Undersök användare med användaren analytics i förhandsversionen av Azure Sentinel | Microsoft Docs
description: Läs mer om hur du undersöker användare med användaren analytics i Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5b06aef0-16be-4ca0-83e4-a33795a82353
ms.service: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ee871f90838b32be3c950ab23a0f01568c309269
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246329"
---
# <a name="investigate-users-with-user-analytics"></a>Undersök användare med användaren analytics

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Användare är enheter som ska övervakas noggrant. För att hjälpa dig att få insikter om dina användare, integreras Azure Sentinel sömlöst med Azure Advanced Threat Protection. Den här integrationen kan du analysera användarbeteenden och prioritera vilka användare som du bör undersöka först, baserat på deras aviseringar och mönster för misstänkt aktivitet i Azure Sentinel och Microsoft 365.

Azure Sentinel berikar användardata med analys från Microsoft 365 att aktivera omfattande analyser och risk Användaranalys för alla användare i Azure Active Directory. 

## <a name="how-it-works"></a>Hur det fungerar

1. Utifrån den analytiska regler, när en matchning identifieras, Azure Sentinel skickar aviseringar till Azure ATP säkerheten.
1. Azure ATP kontrollerar vilken användarentiteter är relaterade till aviseringar och beräknar undersökning prioritet för dessa användare.
3. Azure ATP beräknar sedan om poängen för användarna när det är utökat med data från dina analytics-regler för Azure Sentinel.


## <a name="prerequisites"></a>Förutsättningar

- En Azure Advanced Threat Protection-licens 
- Om du vill aktivera funktionen behöver du globala administratörsbehörigheter för innehavaren som du har installerat Azure Sentinel

> [!NOTE]
> - Du kan ansluta en Sentinel-Azure-instans för varje Azure ATP-klient.
> - Användaren analytics är för närvarande endast tillgänglig för Azure Active Directory-användare. 

## <a name="enable-user-analytics"></a>Aktivera användare analytics

1. Att aktivera användaren analytics i Azure Sentinel i i portalen går du till den **användaren analytics** och klicka på **aktivera**. Detta skickar information om arbetsytan till Azure ATP.

1. Sedan tar det dig till Azure ATP. Under **säkerhetstillägg** i den **Microsoft Azure Sentinel** klickar du på den **+ plus** att lägga till och välj sedan arbetsytan. 
1. Klicka på **Anslut**.

## <a name="investigate-a-user"></a>Undersöka en användare

1. På menyn Azure Sentinel under **användaren analytics**, granska listan över användare enligt deras undersökning prioritet. Poängen baseras på Azure Sentinel aviseringar och Microsoft 365 aviseringar.

2. Sök efter en användare som du vill undersöka. Klicka på användare att komma till sidan. Granska användarens aktiviteter och aviseringar över tid på tidslinjen. Du kan se alla aktiviteter från Microsoft 365 och Azure Sentinel. Du kan också nå sidan genom att gå nedåt i användare i ett ärende.
      
    ![Användare](./media/user-analytics/user-investigation.png)

 
3. Detta ska fungera måste du har korrekt ställa in den [anpassad aviseringsregel](tutorial-detect-threats.md) att mappa rätt användar-ID: n till den **konto** entitet.

    - Windows-händelser, mappa **konto** till den **SID**
    - Azure AD-data (som finns i många loggar inklusive aktivitet för Azure) eller Office 365-data, mappa den **konto** egenskap enligt den **GUID**, eller **User Principal Name**. 

   ![Söka i data](./media/user-analytics/query-window.png)



Exempel: 
> [!NOTE]
> I aktivitetsloggarna för Azure-aktivitet innehåller entiteten anroparen UPN.

1. Den här frågan söker efter skapandet av ett avvikande antal resurser eller distribution aktiviteter i Azure-aktivitetsloggen.

        AzureActivity      
        | where TimeGenerated >= startofday(ago(7d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(7d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope > 0.2        
        | join kind=leftsemi (        
        // Last day's activity is anomalous        
        AzureActivity        
        | where TimeGenerated >= startofday(ago(1d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(1d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope >0.2        
        ) on Caller        
        | extend AccountCustomEntity = Caller
 
2. I anpassad aviseringsregel, mappa den **konto** egenskap **anroparen**.
   
   ![Söka i data](./media/user-analytics/query-window.png)

3. Undersök användaren i fönstret användaren undersökning. Råd om hur du undersöker användare, se [självstudien: Undersöka en användare](https://docs.microsoft.com/azure-advanced-threat-protection/investigate-a-user).



## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter din Hotinformation-provider till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar.

- Om du vill komma igång med Azure Sentinel, behöver du en prenumeration på Microsoft Azure. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
- Lär dig hur du [publicera dina data till Azure Sentinel](quickstart-onboard.md), och [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
