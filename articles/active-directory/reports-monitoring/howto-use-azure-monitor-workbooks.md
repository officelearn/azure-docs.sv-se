---
title: Använda Azure Monitor-arbetsböcker för Azure Active Directory-rapporter | Microsoft Docs
description: Lär dig hur du använder Azure Monitor-arbetsböcker för Azure Active Directory-rapporter.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 6ae14ec152975717af5d55780bcc39aa87c4b01a
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406598"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Hur du använder Azure Monitor-arbetsböcker för Azure Active Directory-rapporter

Vill du att:

- Förstå effekten av din [principer för villkorlig åtkomst](../conditional-access/overview.md) på dina användares inloggning?

- Felsöka inloggningar att få en bättre överblick över din organisations inloggning hälsotillstånd och lösa problem snabbt?

- Vet vem som använder äldre autentiseringar för att logga in på din miljö? (Av [blockera äldre](../conditional-access/block-legacy-authentication.md), du kan förbättra din klient skydd.)

För att hjälpa dig att lösa dessa frågor, innehåller Active Directory arbetsböcker för övervakning. [Azure Monitor arbetsböcker](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) kombinera text, analysfrågor, mått och parametrar i interaktiva rapporter. 

Den här artikeln:

- Förutsätter att du känner till hur du [skapa interaktiva rapporter med hjälp av övervakaren arbetsböcker](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Förklarar hur du använder Övervakaren arbetsböcker för att förstå effekten av principer för villkorlig åtkomst, felsöker inloggningar och för att identifiera äldre autentiseringar.
 


## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill använda övervakaren arbetsböcker, behöver du:

- En Active Directory-klient med en premium-licens (P1 eller P2). Lär dig hur du [en premium-licens](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- En [Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="workbook-access"></a>Arbetsboken åtkomst 

Öppna arbetsböcker:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I det vänstra navigeringsfönstret väljer **Azure Active Directory**.

3. I den **övervakning** väljer **Insights**. 

    ![Välj Insights](./media/howto-use-azure-monitor-workbooks/41.png)

4. Välj en rapport eller en mall eller i verktygsfältet väljer du **öppna**. 

    ![Välj Öppna](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Logga in analys

Åtkomst till arbetsboken logga in analys på den **användning** väljer **inloggningar**. 

Den här arbetsboken visar trender för följande loggar in:

- Alla inloggningar

- Klart

- Väntande åtgärd från användare

- Fel

Du kan filtrera varje trend av följande kategorier:

- Tidsintervall

- Appar

- Användare

![Logga in analys](./media/howto-use-azure-monitor-workbooks/43.png)


För varje trend få du en detaljerad analys av följande kategorier:

- Location

    ![Inloggningar efter plats](./media/howto-use-azure-monitor-workbooks/45.png)

- Enhet

    ![Inloggningar efter enhet](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Inloggningar med äldre autentisering 


Åtkomst till arbetsboken för inloggningar som använder [äldre](../conditional-access/block-legacy-authentication.md)i den **användning** väljer **inloggningar med hjälp av äldre**. 

Den här arbetsboken visar trender för följande loggar in:

- Alla inloggningar

- Klart


Du kan filtrera varje trend av följande kategorier:

- Tidsintervall

- Appar

- Användare

- Protokoll

![Inloggningar efter äldre autentisering](./media/howto-use-azure-monitor-workbooks/47.png)


Få en detaljerad analys av appen och protokoll för varje trend.

![Äldre autentisering inloggningar efter appen och protokoll](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Inloggningar efter villkorlig åtkomst 


Åtkomst till arbetsboken för inloggningar efter [principer för villkorlig åtkomst](../conditional-access/overview.md)i den **villkorlig åtkomst** väljer **inloggningar genom villkorlig åtkomst**. 

Den här arbetsboken visar trender för inaktiverad inloggningar. Du kan filtrera varje trend av följande kategorier:

- Tidsintervall

- Appar

- Användare

![Inloggningar med villkorlig åtkomst](./media/howto-use-azure-monitor-workbooks/49.png)


Få en detaljerad analys av statusen för villkorlig åtkomst för för inaktiverad inloggningar.

![Statusen för villkorlig åtkomst](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Inloggningar efter bevilja kontroller

Åtkomst till arbetsboken för inloggningar efter [bevilja kontroller](../conditional-access/controls.md)i den **villkorlig åtkomst** väljer **inloggningar genom att bevilja kontroller**. 

Den här arbetsboken visas följande inaktiverad inloggning trender:

- Kräv MFA
 
- Kräv användningsvillkor

- Kräv sekretesspolicy

- Annat 


Du kan filtrera varje trend av följande kategorier:

- Tidsintervall

- Appar

- Användare

![Inloggningar efter bevilja kontroller](./media/howto-use-azure-monitor-workbooks/50.png)


Få en detaljerad analys av appen och protokoll för varje trend.

![Analys av de senaste inloggningar](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Inloggningar Felanalys

Använd den **inloggningar Felanalys** arbetsboken för att felsöka fel med följande:

- Inloggningar
- Principer för villkorlig åtkomst
- Äldre autentisering 


Komma åt inloggningar genom att data för villkorlig åtkomst i den **Felsök** väljer **inloggningar med äldre autentisering**. 

Den här arbetsboken visar trender för följande loggar in:

- Alla inloggningar

- Klart

- Väntande åtgärd

- Fel


Du kan filtrera varje trend av följande kategorier:

- Tidsintervall

- Appar

- Användare

![Felsökning av inloggningar](./media/howto-use-azure-monitor-workbooks/52.png)


Azure Monitor ger en analys på detaljnivå för att hjälpa dig att felsöka inloggningar, av följande kategorier:

- Viktigaste fel

    ![Sammanfattning av viktigaste fel](./media/howto-use-azure-monitor-workbooks/53.png)

- Inloggningar användaråtgärd

    ![Sammanfattning av inloggningar användaråtgärd](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Nästa steg

[Skapa interaktiva rapporter med hjälp av övervakaren arbetsböcker](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).