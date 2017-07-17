---
title: Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen | Microsoft Docs
description: Introduktion till rapporter om inloggningsaktiviteter i Azure Active Directory-portalen
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
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: d9cf94252252094218036182c6dc08e8d0779fbe
ms.contentlocale: sv-se
ms.lasthandoff: 07/06/2017

---
# Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen
<a id="sign-in-activity-reports-in-the-azure-active-directory-portal" class="xliff"></a>

Med rapportering i Azure Active Directory (Azure AD) i [Azure Portal](https://portal.azure.com) får du all information du behöver för att ta reda på hur din miljö klarar sig.

Rapporteringsarkitekturen i Azure Active Directory består av följande komponenter:

- **Aktivitet** 
    - **Inloggningsaktiviteter** – Information om användningen av hanterade program och användares inloggningsaktiviteter
    - **Granskningsloggar** – Granska information om systemaktivitet för användare och grupphantering, dina hanterade program och katalogaktiviteter.
- **Säkerhet** 
    - **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. Mer information finns i avsnittet om riskfyllda inloggningar.
    - **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. Mer information finns i avsnittet om användare som har flaggats för risk.

I det här ämnet får du en översikt över inloggningsaktiviteterna.

## Förhandskrav
<a id="pre-requisite" class="xliff"></a>

### Vem kan komma åt dessa data?
<a id="who-can-access-the-data" class="xliff"></a>
* Användare i rollen säkerhetsadministratör eller säkerhetsläsare
* Globala administratörer
* Alla användare (icke-administratörer) kan komma åt sina egna inloggningar 

### Vilken Azure AD-licens behöver du för att komma åt inloggningsaktiviteter?
<a id="what-azure-ad-license-do-you-need-to-access-sign-in-activity" class="xliff"></a>
* Din klient måste ha en associerad Azure AD Premium-licens för att det ska gå att se alla rapporter om inloggningsaktiviteter


## Inloggningsaktiviteter
<a id="signs-in-activities" class="xliff"></a>

Med den information som tillhandahålls av rapporten över användarinloggningsaktiviteter får du svar på frågor som:

* Vilket inloggningsmönster har en användare?
* Hur många användare har en användare loggat in under en vecka?
* Vad är status för dessa inloggningar?

Din startpunkt för alla granskningsdata är **Inloggningar** i avsnittet Aktivitet i **Azure Active**.


![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/61.png "Inloggningsaktivitet")


En granskningslogg har en standardlistvy som visar:

- den relaterade användaren
- programmet som användaren har loggat in till
- inloggningsstatus
- inloggningstid

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/41.png "inloggningsaktivitet")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/19.png "Inloggningsaktivitet")

På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/42.png "Inloggningsaktivitet")

När du klickar på ett objekt i listvyn visas all tillgänglig information om det.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/43.png "Inloggningsaktivitet")


## Filtrerar inloggningsaktiviteter
<a id="filtering-sign-in-activities" class="xliff"></a>

Om du vill begränsa de data som rapporteras till en nivå som passar dig kan du filtrera inloggningsdata med hjälp av följande fält:

- Tidsintervall
- Användare
- Program
- Client
- Inloggningsstatus

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/44.png "Inloggningsaktivitet")


Med filtret för **tidsintervall** kan du definiera en tidsram för de data som returneras.  
Möjliga värden:

- 1 månad
- 7 dagar
- 24 timmar
- Anpassat

När du väljer en anpassad tidsram kan du konfigurera en starttid och en sluttid.

Med filtret **användare** kan du ange namn eller användarhuvudnamn (UPN) för den användare som intresserar dig.

Med filtret **program** kan du ange namnet på programmet som intresserar dig.

Med filtret **Klienten** kan du ange information om enheten som intresserar dig.

Med filtret **inloggningsstatus** kan du välja något av följande filter:

- Alla
- Lyckades
- Fel


## Genvägar till inloggningsaktiviteter
<a id="sign-in-activities-shortcuts" class="xliff"></a>

Förutom Azure Active Directory finns det ytterligare två ställen på Azure Portal där du kan granska data om inloggning:

- Användare och grupper
- Företagsprogram


### Inloggningsaktivitet för användare och grupper
<a id="users-and-groups-sign-ins-activities" class="xliff"></a>

Med den information som tillhandahålls av rapporten över användarinloggningsaktiviteter får du svar på frågor som:

- Vilket inloggningsmönster har en användare?
- Hur många användare har en användare loggat in under en vecka?
- Vad är status för dessa inloggningar?



Din startpunkt för denna data är användarinloggnings-diagrammet i avsnittet **Översikt** under **Användare och grupper**.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/45.png "Inloggningsaktivitet")

Diagrammet med användarinloggningar visar veckovisa sammanställningar av inloggningar för alla användare under en viss tidsperiod. Standardvärdet för tidsperioden är 30 dagar.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/46.png "Inloggningsaktivitet")

När du klickar på en dag i inloggningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna för den dagen.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/41.png "inloggningsaktivitet")

Varje rad i aktivitetslistan över inloggningar ger dig detaljerad information om den valda inloggningen som:

* Vem har loggat in?
* Vad var relaterad UPN?
* Vilket program var målet för inloggningen?
* Vilken IP-adress hade inloggningen?
* Vad var status för inloggningen?

Alternativet **Inloggningar** ger dig en fullständig översikt över alla användarinloggningar.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/51.png "Inloggningsaktivitet")



## Användning av hanterade program
<a id="usage-of-managed-applications" class="xliff"></a>

Med en programcentrerad vy över dina inloggningsuppgifter kan du få svar på frågor som:

* Vem använder mina program?
* Vilka är de tre främsta programmen i organisationen?
* Jag har nyligen distribuerat ett program. Hur går det för det?

Din startpunkt för denna data är rapporten över de tre främsta programmen i organisationen under de senaste 30 dagarna avsnittet **Översikt**, under **Företagsprogram**.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/64.png "Inloggningsaktivitet")

Diagram över programanvändning visar veckovisa sammanställning av inloggningar för dina tre främsta program under en given tidsperiod. Standardvärdet för tidsperioden är 30 dagar.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/47.png "Inloggningsaktivitet")

Om du vill kan du ange att fokusera på ett visst program.


![Rapportering](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Rapportering")

När du klickar på en dag i programanvändningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna.


![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/48.png "Inloggningsaktivitet")


Alternativet **Inloggningar** ger dig en fullständig översikt över alla inloggningshändelser för dina program.

![Inloggningsaktivitet](./media/active-directory-reporting-activity-sign-ins/49.png "Inloggningsaktivitet")



## Nästa steg
<a id="next-steps" class="xliff"></a>
Se guiden [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).


