---
title: "Granska aktivitetsrapporter i Azure Active Directory-portalen – förhandsversion | Microsoft Docs"
description: "Introduktion till granskning av aktivitetsrapporter i Azure Active Directory-portalen – förhandsversion"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 9f1fd20c77b085a66487c83d8dcd902e8cc83e6d
ms.lasthandoff: 03/17/2017


---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal---preview"></a>Granska aktivitetsrapporter i Azure Active Directory-portalen – förhandsversion

Med rapportering i Azure Active Directory-[förhandsversionen](active-directory-preview-explainer.md) får du all information du behöver för att ta reda på hur din miljö klarar sig.

Rapporteringsarkitekturen i Azure Active Directory består av följande komponenter:

- **Aktivitet** 
    - **Inloggningsaktiviteter** – Information om användningen av hanterade program och användares inloggningsaktiviteter
    - **Granskningsloggar** – Granska information om systemaktivitet för användare och grupphantering, dina hanterade program och katalogaktiviteter.
- **Säkerhet** 
    - **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. Mer information finns i avsnittet om riskfyllda inloggningar.
    - **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. Mer information finns i avsnittet om användare som har flaggats för risk.

I det här ämnet får du en översikt över granskningsaktiviteterna.
 
## <a name="audit-logs"></a>Granskningsloggar

Granskningsloggarna i Azure Active Directory ger dokumentation över systemaktiviteter för kontroll av överensstämmelse.

Det finns tre huvudkategorier för granskningsrelaterade aktiviteter i Azure-portalen:

- Användare och grupper   

- Program

- Katalog   

En fullständig lista över granskningsrapporteringsaktiviteter finns i [lista över granskningsrapporthändelser](active-directory-reporting-audit-events.md#list-of-audit-report-events).


Din startpunkt för all granskningsdata är **Granskningsloggar** i avsnittet **Aktivitet** i **Azure Active Directory**.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/61.png "Granskningsloggar")

En granskningslogg har en listvy som visar aktörer (*vem*) aktiviteter (*vad*) och målen.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/345.png "Granskningsloggar")

Genom att klicka på ett objekt i listvyn kan du få mer information.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/873.png "Granskningsloggar")


## <a name="users-and-groups-audit-logs"></a>Granskningsloggar för användare och grupper

Med användar- och gruppbaserade granskningsrapporter kan du få svar på frågor som:

- Vilka typer av uppdateringar har getts användare?

- Hur många användare ändrades?

- Hur många lösenord ändrades?

- Vad har en administratör gjort i en katalog?

- Vilka grupper har lagts till?

- Finns det grupper med ändringar i medlemskap?

- Har ägare av en grupp ändrats?

- Vilka licenser har tilldelats en grupp eller en användare?

Om du bara vill kontrollera granskningsdata som relateras till användare och grupper kan du hitta en filtrerad vy under **Granskningsloggar** i avsnittet **Aktivitet** i **Användare och grupper**.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/93.png "Granskningsloggar")

## <a name="application-audit-logs"></a>Granskningsloggar för program
Med programbaserade granskningsrapporter kan du få svar på frågor som:

* Vilka program har lagts till eller uppdaterats?
* Vilka program har tagits bort?
* Har tjänstprincipen för ett program ändrats?
* Har namnen på program ändrats?
* Vem gav tillstånd till ett program?

Om du bara vill kontrollera granskningsdata som relateras till program kan du hitta en filtrerad vy under **Granskningsloggar** i avsnittet **Aktivitet** i **Företagsprogram**.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/134.png "Granskningsloggar")

## <a name="filtering-audit-logs"></a>Filtrera granskningsloggar
Du kan filtrera inloggningar efter ett tidsintervall som begränsar mängden visad data:

- Datum och tid

- Användarens huvudnamn

- Kategori

- Resurstyp för aktivitet

- Aktivitet

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/625.png "Granskningsloggar")


Med filtret **Kategori** kan du begränsa omfattningen av granskningsrapporten baserat på följande kategorier:

- Kärnkatalog

- Hantering av lösenord för självbetjäning

- Självbetjäning, grupphantering

- Automatiserad förnyelse av lösenord 

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/626.png "Granskningsloggar")



Innehållet i **aktivitetsresurslistan** är knutet till din åtkomstpunkt för det här bladet.  
Om din startpunkt är Azure Active Directory innehåller den här listan alla möjliga aktivitetstyper:

- Katalog

- Användare

- Grupp 

- Program 

- Princip

- Enhet


![Granskning](./media/active-directory-reporting-activity-audit-logs/627.png "Granskning")

De angivna aktiviteterna omfattas av aktivitetstypen.
Om du till exempel har valt en **användare** som **aktivitetstyp** innehåller listan **aktivitet** endast gruppaktiviteter.   

![Granskning](./media/active-directory-reporting-activity-audit-logs/628.png "Granskning")

Om du väljer **grupp** som **aktivitetsresurstyp** får du ytterligare ett filtreringsalternativ som gör att du även kan filtrera efter följande **aktivitetskällor**:

- Azure AD

- O365


![Granskning](./media/active-directory-reporting-activity-audit-logs/629.png "Granskning")



En annan metod för att filtrera posterna i en granskningslogg är att söka efter specifika poster.

![Granskning](./media/active-directory-reporting-activity-audit-logs/237.png "Granskning")




## <a name="next-steps"></a>Nästa steg
Se guiden [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).


