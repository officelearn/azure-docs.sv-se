---
title: Använda Azure Monitor-arbetsböcker för Azure Active Directory-rapporter | Microsoft Docs
description: Lär dig hur du använder Azure Monitor-arbetsböcker för Azure Active Directory-rapporter
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
ms.openlocfilehash: 2c9b3d0ef110fea0629af345a71d0d7b7cce7313
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014440"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Anvisningar: Använda Azure Monitor-arbetsböcker för Azure Active Directory-rapporter

Vill du att:

- Förstå effekten av din [principer för villkorlig åtkomst](../conditional-access/overview.md) på dina användares inloggning?

- Felsöka inloggningar att få en bättre överblick över din organisation loggar in health och Lös problem snabbt?

- Vet vem som använder äldre autentiseringar för att logga in på din miljö? Genom att [blockera äldre](../conditional-access/block-legacy-authentication.md), du kan förbättra din klient skydd.


[Azure Monitor arbetsböcker](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) kombinera text Analytics frågor, Azure-mått och parametrar i interaktiva rapporter. Azure Active Directory ger dig arbetsböcker för övervakning som hjälper dig att hitta svar på frågor som ovan.

Den här artikeln:

- Förutsätts att du vet hur du [skapa interaktiva rapporter med Azure Monitor arbetsböcker](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Beskriver hur du kan använda Azure Monitor arbetsböckerna om övervakning för att besvara frågor ovan.
 


## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill använda den här funktionen behöver du:

- En Azure Active Directory-klient, med en premiumlicens (P1/P2). Lär dig hur du [en premium-licens](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- En [Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="access-workbooks"></a>Åtkomst-arbetsböcker 

Öppna arbetsböcker:

1. Logga in på din [Azure-portalen](https://portal.azure.com).

2. I det vänstra navigeringsfältet, klickar du på **Azure Active Directory**.

3. I den **övervakning** klickar du på **Insights**. 

    ![Insikter](./media/howto-use-azure-monitor-workbooks/41.png)

4. En rapport eller en mall eller klicka på **öppna** i verktygsfältet. 

    ![Galleri](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Logga in analys

För att komma åt arbetsboken logga in analys, klickar du på **inloggningar** i den **användning** avsnittet. 

Den här arbetsboken visar trender för följande loggar in:

- Alla inloggningar

- Lyckades

- Väntande åtgärd från användare

- Fel

Du kan filtrera varje trend efter:

- Tidsintervall

- Program

- Användare

![Galleri](./media/howto-use-azure-monitor-workbooks/43.png)


För varje trend få du en detaljerad analys:

- Location

    ![Galleri](./media/howto-use-azure-monitor-workbooks/45.png)

- Enhet

    ![Galleri](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Inloggningar med äldre autentisering 


Att inloggningar med [äldre](../conditional-access/block-legacy-authentication.md) arbetsboken, klickar du på **inloggningar med hjälp av äldre** i den **användning** avsnittet. 

Den här arbetsboken visar trender för följande loggar in:

- Alla inloggningar

- Lyckades


Du kan filtrera varje trend efter:

- Tidsintervall

- Program

- Användare

- Protokoll 

![Galleri](./media/howto-use-azure-monitor-workbooks/47.png)


Få en detaljerad analys av appen och protokoll för varje trend.

![Galleri](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Inloggningar efter villkorlig åtkomst 


Komma åt inloggningar efter [principer för villkorlig åtkomst](../conditional-access/overview.md) arbetsboken, klickar du på **inloggningar genom villkorlig åtkomst** i den **villkorlig åtkomst** avsnittet. 

Den här arbetsboken visar trenden för inaktiverad inloggningar.

Du kan filtrera varje trend efter:

- Tidsintervall

- Program

- Användare

![Galleri](./media/howto-use-azure-monitor-workbooks/49.png)


För de inaktiverade inloggningarna få en detaljerad analys av statusen för villkorlig åtkomst.

![Statusen för villkorlig åtkomst](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Inloggningar efter bevilja kontroller

Komma åt inloggningar efter [bevilja kontroller](../conditional-access/controls.md) arbetsboken, klickar du på **inloggningar genom att bevilja kontroller** i den **villkorlig åtkomst** avsnittet. 

Den här arbetsboken visas följande inaktiverad inloggning trender:

- Kräv MFA
 
- Kräv användningsvillkor

- Kräv sekretesspolicy

- Annat


Du kan filtrera varje trend efter:

- Tidsintervall

- Program

- Användare

![Galleri](./media/howto-use-azure-monitor-workbooks/50.png)


Få en detaljerad analys av appen och protokoll för varje trend.

![Galleri](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Inloggningar Felanalys

Använd den **inloggningar Felanalys** arbetsboken för att felsöka fel med:

- Inloggningar
- Principer för villkorsstyrd åtkomst
- Äldre autentisering. 


Om du vill komma åt inloggningar genom villkorlig åtkomst till data, klickar du på **inloggningar med äldre autentisering** i den **Felsök** avsnittet. 

Den här arbetsboken visar trender för följande loggar in:

- Alla inloggningar

- Lyckades

- Väntande åtgärd

- Fel


Du kan filtrera varje trend efter:

- Tidsintervall

- Program

- Användare

![Galleri](./media/howto-use-azure-monitor-workbooks/52.png)


Om du vill felsöka inloggningar få en detaljerad analys:

- Viktigaste fel

    ![Galleri](./media/howto-use-azure-monitor-workbooks/53.png)

- Inloggningar användaråtgärd

    ![Galleri](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Nästa steg

* [Skapa interaktiva rapporter med Azure Monitor-arbetsböcker](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)