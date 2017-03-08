---
title: "Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen – förhandsversion | Microsoft Docs"
description: "Introduktion till rapporter om inloggningsaktiviteter i Azure Active Directory-portalen – förhandsversion"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 78617dc7e3a4b6eb4fc32d32b6850b8c0832d6d8
ms.openlocfilehash: 9819c5f6a3aea53664d86e3a23b25946c0f2b731
ms.lasthandoff: 02/22/2017


---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal---preview"></a>Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen – förhandsversion

Med rapportering i Azure Active Directory-[förhandsversionen](active-directory-preview-explainer.md) får du all information du behöver för att ta reda på hur din miljö klarar sig.

Rapporteringsarkitekturen i Azure Active Directory består av följande komponenter:

- **Aktivitet** 
    - **Inloggningsaktiviteter** – Information om användningen av hanterade program och användares inloggningsaktiviteter
    - **Granskningsloggar** – Granska information om systemaktivitet för användare och grupphantering, dina hanterade program och katalogaktiviteter.
- **Säkerhet** 
    - **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. Mer information finns i avsnittet om riskfyllda inloggningar.
    - **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. Mer information finns i avsnittet om användare som har flaggats för risk.

I det här ämnet får du en översikt över inloggningsaktiviteterna.

## <a name="signs-in-activities"></a>Inloggningsaktiviteter

Med den information som tillhandahålls av rapporten över användarinloggningsaktiviteter får du svar på frågor som:

* Vilket inloggningsmönster har en användare?
* Hur många användare har en användare loggat in under en vecka?
* Vad är status för dessa inloggningar?

Din startpunkt för denna data är användarinloggnings-diagrammet i avsnittet **Översikt** under **Användare och grupper**.

 ![Rapportering](./media/active-directory-reporting-activity-sign-ins/05.png "Rapportering")

Diagrammet med användarinloggningar visar veckovisa sammanställningar av inloggningar för alla användare under en viss tidsperiod. Standardvärdet för tidsperioden är 30 dagar.

![Rapportering](./media/active-directory-reporting-activity-sign-ins/02.png "Rapportering")

När du klickar på en dag i inloggningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna.

![Rapportering](./media/active-directory-reporting-activity-sign-ins/03.png "Rapportering")

Varje rad i aktivitetslistan över inloggningar ger dig detaljerad information om den valda inloggningen som:

* Vem har loggat in?
* Vad var relaterad UPN?
* Vilket program var målet för inloggningen?
* Vilken IP-adress hade inloggningen?
* Vad var status för inloggningen?

## <a name="usage-of-managed-applications"></a>Användning av hanterade program

Med en programcentrerad vy över dina inloggningsuppgifter kan du få svar på frågor som:

* Vem använder mina program?
* Vilka är de tre främsta programmen i organisationen?
* Jag har nyligen distribuerat ett program. Hur går det för det?

Din startpunkt för denna data är rapporten över de tre främsta programmen i organisationen under de senaste 30 dagarna avsnittet **Översikt**, under **Företagsprogram**.

 ![Rapportering](./media/active-directory-reporting-activity-sign-ins/06.png "Rapportering")

Diagram över programanvändning visar veckovisa sammanställning av inloggningar för dina tre främsta program under en given tidsperiod. Standardvärdet för tidsperioden är 30 dagar.

![Rapportering](./media/active-directory-reporting-activity-sign-ins/78.png "Rapportering")

Om du vill kan du ange att fokusera på ett visst program.

![Rapportering](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Rapportering")

När du klickar på en dag i programanvändningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna.

![Rapportering](./media/active-directory-reporting-activity-sign-ins/top_app_sign_ins.png "Rapportering")

Alternativet **Inloggningar** ger dig en fullständig översikt över alla inloggningshändelser för dina program.

![Rapportering](./media/active-directory-reporting-activity-sign-ins/85.png "Rapportering")

Med hjälp av kolumnväljaren kan du välja datafält som du vill visa.

![Rapportering](./media/active-directory-reporting-activity-sign-ins/column_chooser.png "Rapportering")

## <a name="filtering-sign-ins"></a>Filtrera inloggningar
Du kan filtrera inloggningar efter ett tidsintervall som begränsar mängden visad data:

* Datum och tid 
* Användarens huvudnamn
* Programnamn
* Klientnamn
* Inloggningsstatus

![Rapportering](./media/active-directory-reporting-activity-sign-ins/293.png "Rapportering")

En annan metod för att filtrera inloggningsaktivitetsposterna är att söka efter specifika poster.
Sökmetoden gör att du kan definiera omfattningen av din inloggningar runt specifika **Användare**, **Grupper** eller **Program**.

![Rapportering](./media/active-directory-reporting-activity-sign-ins/84.png "Rapportering")


## <a name="next-steps"></a>Nästa steg
Se guiden [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).


