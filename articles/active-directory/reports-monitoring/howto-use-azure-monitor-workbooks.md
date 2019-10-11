---
title: Använda Azure Monitor arbets böcker för Azure Active Directorys rapporter | Microsoft Docs
description: Lär dig hur du använder Azure Monitor-arbetsböcker för Azure Active Directory rapporter.
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: 9bea8da4f0d694be3a39a8f5dfaca8e54ce2773d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255660"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Använda Azure Monitor-arbetsböcker för Azure Active Directory rapporter

Vill du:

- Förstå resultatet av dina [principer för villkorlig åtkomst](../conditional-access/overview.md) på användarnas inloggnings upplevelser?

- Felsök inloggnings fel för att få en bättre översikt över din organisations inloggnings hälsa och för att snabbt lösa problem?

- Vet vem som använder äldre autentiseringar för att logga in i din miljö? (Genom att [blockera äldre autentisering](../conditional-access/block-legacy-authentication.md)kan du förbättra din klients skydd.)

För att hjälpa dig att åtgärda de här frågorna innehåller Active Directory arbets böcker för övervakning. [Azure Monitor arbets böcker](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) kombinerar text, analys frågor, mått och parametrar till omfattande interaktiva rapporter. 

Den här artikeln:

- Förutsätter att du är bekant med hur du [skapar interaktiva rapporter med hjälp av övervaka arbets böcker](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Förklarar hur du använder övervaknings arbets böcker för att förstå effekterna av dina principer för villkorlig åtkomst, för att felsöka inloggnings fel och för att identifiera äldre autentiseringar.
 


## <a name="prerequisites"></a>Krav

Om du vill använda övervaknings arbets böcker behöver du:

- En Active Directory klient med en Premium-licens (P1 eller P2). Lär dig hur du [skaffar en Premium-licens](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- En [Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Åtkomst](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) till Log Analytics-arbetsytan
- Följande roller i Azure Active Directory (om du får åtkomst till Log Analytics via Azure Active Directory Portal)
    - Säkerhetsadministratör
    - Säkerhets läsare
    - Rapport läsare
    - Global administratör

## <a name="roles"></a>Roller
Du måste vara i någon av följande roller och ha [åtkomst till underliggande Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) arbets yta för att hantera arbets böckerna:
-   Global administratör
-   Säkerhetsadministratör
-   Säkerhets läsare
-   Rapport läsare
-   Program administratör


## <a name="workbook-access"></a>Arbets boks åtkomst 

För att få åtkomst till arbets böcker:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. I det vänstra navigerings fönstret väljer du **Azure Active Directory**.

3. I avsnittet **övervakning** väljer du **arbets böcker**. 

    ![Välj insikter](./media/howto-use-azure-monitor-workbooks/41.png)

4. Välj en rapport eller mall eller Välj **Öppna**i verktygsfältet. 

    ![Välj öppna](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Inloggnings analys

För att få åtkomst till arbets boken för inloggnings analys går du till avsnittet **användning** och väljer **inloggnings program**. 

I den här arbets boken visas följande trender:

- Alla inloggningar

- Lyckades

- Väntande användar åtgärd

- Fel

Du kan filtrera varje trend enligt följande kategorier:

- Tidsintervall

- Appar

- Användare

![Inloggnings analys](./media/howto-use-azure-monitor-workbooks/43.png)


För varje trend får du en uppdelning med följande kategorier:

- Plats

    ![Inloggningar efter plats](./media/howto-use-azure-monitor-workbooks/45.png)

- Enhet

    ![Inloggningar per enhet](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Inloggningar med äldre autentisering 


För att få åtkomst till arbets boken för inloggningar som använder [äldre autentisering](../conditional-access/block-legacy-authentication.md), i avsnittet **användning** väljer du **inloggningar med äldre autentisering**. 

I den här arbets boken visas följande trender:

- Alla inloggningar

- Lyckades


Du kan filtrera varje trend enligt följande kategorier:

- Tidsintervall

- Appar

- Användare

- Protokoll

![Inloggningar från äldre autentisering](./media/howto-use-azure-monitor-workbooks/47.png)


För varje trend får du en uppdelning efter app och protokoll.

![Bakåtkompatibla inloggningar via app och protokoll](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Inloggningar efter villkorlig åtkomst 


Om du vill komma åt arbets boken för inloggningar via [principer för villkorlig åtkomst](../conditional-access/overview.md)i avsnittet **villkorlig åtkomst** väljer du **inloggningar med villkorlig åtkomst**. 

I den här arbets boken visas trender för inaktiverade inloggningar. Du kan filtrera varje trend enligt följande kategorier:

- Tidsintervall

- Appar

- Användare

![Inloggningar med villkorlig åtkomst](./media/howto-use-azure-monitor-workbooks/49.png)


För inaktiverade inloggningar får du en uppdelning av status för villkorlig åtkomst.

![Status för villkorlig åtkomst](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Inloggningar genom att tilldela kontroller

Om du vill komma åt arbets boken för inloggningar genom att [bevilja kontroller](../conditional-access/controls.md)i avsnittet **villkorlig åtkomst** väljer du **inloggningar genom att bevilja kontroller**. 

Den här arbets boken visar följande inaktiverade inloggnings trender:

- Kräv MFA
 
- Kräva användningsvillkor

- Kräv sekretess policy

- Övrigt


Du kan filtrera varje trend enligt följande kategorier:

- Tidsintervall

- Appar

- Användare

![Inloggningar genom att tilldela kontroller](./media/howto-use-azure-monitor-workbooks/50.png)


För varje trend får du en uppdelning efter app och protokoll.

![Uppdelning av de senaste inloggnings programmen](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analys av inloggnings problem

Använd analys arbets boken för **inloggnings fel** för att felsöka fel med följande:

- Inloggningar
- Principer för villkorlig åtkomst
- Äldre autentisering 


Om du vill komma åt inloggnings uppgifterna med hjälp av villkorlig åtkomst i avsnittet **fel sökning** väljer du **inloggningar med äldre autentisering**. 

I den här arbets boken visas följande trender:

- Alla inloggningar

- Lyckades

- Väntande åtgärd

- Fel


Du kan filtrera varje trend enligt följande kategorier:

- Tidsintervall

- Appar

- Användare

![Felsöka inloggningar](./media/howto-use-azure-monitor-workbooks/52.png)


För att hjälpa dig att felsöka inloggningar ger Azure Monitor en uppdelning efter följande kategorier:

- Vanligaste felen

    ![Sammanfattning av de vanligaste felen](./media/howto-use-azure-monitor-workbooks/53.png)

- Inloggningar väntar på användar åtgärd

    ![Sammanfattning av inloggningar väntar på användar åtgärd](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Nästa steg

[Skapa interaktiva rapporter med hjälp av övervaka arbets böcker](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
