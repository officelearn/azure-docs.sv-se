---
title: Azure Monitor-arbetsböcker för rapporter | Microsoft-dokument
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
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2e94d9f56a865999f9169650f621a6af892c27ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014368"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Så här använder du Azure Monitor-arbetsböcker för Azure Active Directory-rapporter

> [!IMPORTANT]
> För att optimera de underliggande frågorna i den här arbetsboken klickar du på "Redigera", klickar på ikonen Inställningar och väljer arbetsytan där du vill köra dessa frågor. Arbetsböcker som standard väljer alla arbetsytor där du dirigerar dina Azure AD-loggar. 

Vill du:

- Vill du förstå effekten av dina [principer för villkorlig åtkomst](../conditional-access/overview.md) på användarnas inloggningsupplevelse?

- Felsöka inloggningsfel för att få en bättre bild av organisationens inloggningshälsa och lösa problem snabbt?

- Vet du vem som använder äldre autentiseringar för att logga in på din miljö? (Genom [att blockera äldre autentisering](../conditional-access/block-legacy-authentication.md)kan du förbättra klientens skydd.)

- Måste du förstå effekten av principer för villkorlig åtkomst i din klientorganisation?

- Vill du granska möjligheten att granska: inloggningsloggfrågor, arbetsboken rapporterar hur många användare som beviljats eller nekades åtkomst, samt hur många användare som kringgås principer för villkorlig åtkomst när de fick åtkomst till resurser?

- Är du intresserad av att utveckla en djupare förståelse för: arbetsboksinformationen per villkor så att effekten av en princip kan kontextualiseras per villkor, inklusive enhetsplattform, enhetstillstånd, klientapp, inloggningsrisk, plats och program?

- Få djupare insikter i inloggningsloggfrågor, arbetsboken rapporterar hur många användare som beviljats eller nekats åtkomst, samt hur många användare som kringgås principer för villkorlig åtkomst när de fick åtkomst till resurser.

- För att hjälpa dig att lösa dessa frågor innehåller Active Directory arbetsböcker för övervakning. [Azure Monitor-arbetsböcker](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) kombinerar text, analysfrågor, mått och parametrar i avancerade interaktiva rapporter.



Den här artikeln:

- Förutsätter att du är bekant med hur du [skapar interaktiva rapporter med hjälp av Övervaka arbetsböcker](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- I artikeln beskrivs hur du anvÃ¤nder Monitor-arbetsböcker för att förstå effekten av principerna för villkorlig åtkomst, felsÃ¶kning av inloggningsfel och identifiera äldre autentiseringar.
 


## <a name="prerequisites"></a>Krav

Om du vill använda Övervaka arbetsböcker behöver du:

- En Active Directory-klient med en premiumlicens (P1 eller P2). Läs om hur du [skaffar en premiumlicens.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)

- En [log analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Tillgång](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) till arbetsytan för logganalys
- Följande roller i Azure Active Directory (om du använder Logganalys via Azure Active Directory-portalen)
    - Säkerhetsadministratör
    - Säkerhetsläsare
    - Rapportläsare
    - Global administratör

## <a name="roles"></a>Roller
Du måste vara i någon av följande roller samt ha [åtkomst till underliggande Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) för att hantera arbetsböckerna:
-   Global administratör
-   Säkerhetsadministratör
-   Säkerhetsläsare
-   Rapportläsare
-   Programadministratör

## <a name="workbook-access"></a>Åtkomst till arbetsbok 

Så här kommer du åt arbetsböcker:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Navigera till **Azure Active Directory** > **Monitoring** > **Workbooks**. 

1. Markera en rapport eller mall eller välj **Öppna**i verktygsfältet . 

![Hitta Azure Monitor-arbetsböckerna i Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Inloggningsanalys

Om du vill komma åt inloggningsanalysarbetsboken väljer du **Inloggningar i**avsnittet **Användning** . 

I den här arbetsboken visas följande inloggningstrender:

- Alla inloggningar

- Lyckades

- Väntande användaråtgärd

- Fel

Du kan filtrera varje trend efter följande kategorier:

- Tidsintervall

- Program

- Användare

![Inloggningsanalys](./media/howto-use-azure-monitor-workbooks/43.png)


För varje trend får du en uppdelning efter följande kategorier:

- Location

    ![Inloggningar efter plats](./media/howto-use-azure-monitor-workbooks/45.png)

- Enhet

    ![Inloggningar per enhet](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Inloggningar med äldre autentisering 


Om du vill komma åt arbetsboken för inloggningar som använder [äldre autentisering](../conditional-access/block-legacy-authentication.md)väljer du **Inloggningar med äldre autentisering i**avsnittet **Användning** . 

I den här arbetsboken visas följande inloggningstrender:

- Alla inloggningar

- Lyckades


Du kan filtrera varje trend efter följande kategorier:

- Tidsintervall

- Program

- Användare

- Protokoll

![Inloggningar efter äldre autentisering](./media/howto-use-azure-monitor-workbooks/47.png)


För varje trend får du en uppdelning efter app och protokoll.

![Inloggningar med äldre autentisering via app och protokoll](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Inloggningar med villkorlig åtkomst 


Om du vill komma åt arbetsboken för inloggningar efter principer för [villkorlig åtkomst](../conditional-access/overview.md)väljer du **Inloggningar med villkorlig åtkomst i**avsnittet Villkorlig **åtkomst** . 

I den här arbetsboken visas trender för inaktiverade inloggningar. Du kan filtrera varje trend efter följande kategorier:

- Tidsintervall

- Program

- Användare

![Inloggningar med villkorlig åtkomst](./media/howto-use-azure-monitor-workbooks/49.png)


För inaktiverade inloggningar får du en uppdelning efter statusen Villkorlig åtkomst.

![Status för villkorlig åtkomst](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Conditional Access Insights

### <a name="overview"></a>Översikt

Arbetsböcker innehåller inloggningsloggfrågor som kan hjälpa IT-administratörer att övervaka effekten av principer för villkorlig åtkomst i sin klientorganisation. Du kan rapportera om hur många användare som skulle ha beviljats eller nekats åtkomst. Arbetsboken innehåller insikter om hur många användare som skulle ha kringgått principer för villkorlig åtkomst baserat på dessa användares attribut vid tidpunkten för inloggningen. Den innehåller information per villkor så att effekten av en princip kan kontextualiseras per villkor, inklusive enhetsplattform, enhetstillstånd, klientapp, inloggningsrisk, plats och program.

### <a name="instructions"></a>Instruktioner 
Om du vill komma åt arbetsboken för statistik för villkorlig åtkomst väljer du arbetsboken **För villkorlig åtkomstinsikter** i avsnittet Villkorlig åtkomst. Den här arbetsboken visar den förväntade effekten av varje princip för villkorlig åtkomst i din klientorganisation. Välj en eller flera principer för villkorlig åtkomst i listrutan och begränsa arbetsbokens omfattning genom att använda följande filter: 

- **Tidsintervall**

- **Användare**

- **Appar**

- **Datavy**

![Status för villkorlig åtkomst](./media/howto-use-azure-monitor-workbooks/access-insights.png)


Konsekvenssammanfattningen visar antalet användare eller inloggningar för vilka de valda principerna hade ett visst resultat. Totalt är antalet användare eller inloggningar för vilka de valda principerna utvärderades i det valda tidsintervallet. Klicka på en panel om du vill filtrera data i arbetsboken efter den resultattypen. 

![Status för villkorlig åtkomst](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Den här arbetsboken visar också effekten av de valda principerna uppdelade efter vart och ett av de sex villkoren: 
- **Enhetstillstånd**
- **Enhetsplattform**
- **Klientappar**
- **Inanmälningsrisk**
- **Location**
- **Program**

![Status för villkorlig åtkomst](./media/howto-use-azure-monitor-workbooks/device-platform.png)

Du kan också undersöka enskilda inloggningar, filtrerade efter de parametrar som valts i arbetsboken. Sök efter enskilda användare, sorterad efter inloggningsfrekvens och visa motsvarande inloggningshändelser. 

![Status för villkorlig åtkomst](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>Inloggningar med bidragskontroller

Om du vill komma åt arbetsboken för inloggningar med [bidragskontroller](../conditional-access/controls.md)väljer du **Inloggningar av bidragskontroller i**avsnittet **Villkorlig åtkomst** . 

I den här arbetsboken visas följande trender för inaktiverad inloggning:

- Kräv MFA
 
- Kräva användningsvillkor

- Kräv sekretesspolicy

- Annat


Du kan filtrera varje trend efter följande kategorier:

- Tidsintervall

- Program

- Användare

![Inloggningar med bidragskontroller](./media/howto-use-azure-monitor-workbooks/50.png)


För varje trend får du en uppdelning efter app och protokoll.

![Uppdelning av de senaste inloggningarna](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analys av inloggningar

Använd arbetsboken för analys **av inloggningar för** att felsöka fel med:

- Inloggningar
- Principer för villkorlig åtkomst
- Äldre autentisering 


Om du vill komma åt inloggningarna med villkorsstyrd åtkomst väljer du **Inloggningar med äldre autentisering i**avsnittet **FelsÃ¶k.** 

I den här arbetsboken visas följande inloggningstrender:

- Alla inloggningar

- Lyckades

- Väntande åtgärd

- Fel


Du kan filtrera varje trend efter följande kategorier:

- Tidsintervall

- Program

- Användare

![Felsöka inloggningar](./media/howto-use-azure-monitor-workbooks/52.png)


För att hjälpa dig att felsöka inloggningar ger Azure Monitor dig en uppdelning efter följande kategorier:

- De vanligaste felen

    ![Sammanfattning av de vanligaste felen](./media/howto-use-azure-monitor-workbooks/53.png)

- Inloggningar väntar på användaråtgärd

    ![Sammanfattning av inloggningar som väntar på användaråtgärd](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Nästa steg

[Skapa interaktiva rapporter med hjälp av Övervaka arbetsböcker](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
