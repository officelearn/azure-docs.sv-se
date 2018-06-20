---
title: Granska aktivitetsrapporter i Azure Active Directory-portalen | Microsoft Docs
description: Introduktion till granskning av aktivitetsrapporter i Azure Active Directory-portalen
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 04/19/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 2565f31bdac59c3fbbb1d9d9a516b7bbf31d4d4c
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222110"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Granska aktivitetsrapporter i Azure Active Directory-portalen 

Med rapportering i Azure Active Directory (Azure AD) får du all information du behöver för att ta reda på hur din miljö klarar sig.

Rapporteringsarkitekturen i Azure AD består av följande komponenter:

- **Aktivitet** 
    - **Inloggningsaktiviteter** – Information om användningen av hanterade program och användares inloggningsaktiviteter
    - **Granskningsloggar** – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som användare, appar, grupper, roller, principer, autentiseringar och så vidare ...
- **Säkerhet** 
    - **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. Mer information finns i avsnittet om riskfyllda inloggningar.
    - **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. Mer information finns i avsnittet om användare som har flaggats för risk.

I det här ämnet får du en översikt över granskningsaktiviteterna.
 
## <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?
* Användare i rollen säkerhetsadministratör eller säkerhetsläsare
* Globala administratörer
* Enskilda användare (icke-administratörer) kan se sina egna aktiviteter


## <a name="audit-logs"></a>Granskningsloggar

Granskningsloggarna i Azure Active Directory ger dokumentation över systemaktiviteter för kontroll av överensstämmelse.  
Din startpunkt för alla granskningsdata är **Granskningsloggar** i avsnittet **Aktivitet** i **Azure Active Directory**.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/61.png "Granskningsloggar")

En granskningslogg har en standardlistvy som visar:

- datum och tid för förekomsten
- initieraren/aktören (*vem*) för en aktivitet 
- aktiviteten (*vad*) 
- målet

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/18.png "Granskningsloggar")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/19.png "Granskningsloggar")

På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/21.png "Granskningsloggar")


När du klickar på ett objekt i listvyn visas all tillgänglig information om det.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/22.png "Granskningsloggar")


## <a name="filtering-audit-logs"></a>Filtrera granskningsloggar

Om du vill begränsa de data som rapporteras till en nivå som passar dig kan du filtrera granskningsdata med hjälp av följande fält:

- Datumintervall
- Initierad av (aktör)
- Kategori
- Resurstyp för aktivitet
- Aktivitet

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/23.png "Granskningsloggar")


Med filtret för **datumintervall** kan du definiera en tidsram för de data som returneras.  
Möjliga värden:

- 1 månad
- 7 dagar
- 24 timmar
- Anpassat

När du väljer en anpassad tidsram kan du konfigurera en starttid och en sluttid.

Med filtret **initierad av** kan du definiera en aktörs namn eller dess UPN (Universal Principal Name).

Med filtret **kategori** kan du välja något av följande filter:

- Alla
- Grundläggande kategori
- Grundläggande katalog
- Lösenordshantering via självbetjäning
- Självbetjäning, grupphantering
- Kontoetablering – Automatiserad förnyelse av lösenord
- Inbjudna användare
- MIM-tjänst
- Identity Protection
- B2C

Med filtret för **aktivitsresurstyp** kan du välja något av följande filter:

- Alla 
- Grupp
- Katalog
- Användare
- Program
- Princip
- Enhet
- Annat

När du väljer **Grupp** som **aktivitetsresurstyp** får du tillgång till ytterligare en filterkategori som du kan använda för att även ange en **källa**:

- Azure AD
- O365


Filtret **aktivitet** baseras på kategorin och den aktivitetsresurstyp som du väljer. Du kan välja en specifik aktivitet som du vill visa eller välja alla. 

Du kan hämta listan över alla granskningsaktiviteter med Graph API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, där $tenantdomain = ditt domännamn eller refererar till artikeln [Granska rapporthändelser](active-directory-reporting-audit-events.md).


## <a name="audit-logs-shortcuts"></a>Genvägar till granskningsloggar

Förutom **Azure Active Directory** finns det ytterligare två ställen på Azure Portal där du kan granska data:

- Användare och grupper
- Företagsprogram

### <a name="users-and-groups-audit-logs"></a>Granskningsloggar för användare och grupper

Med användar- och gruppbaserade granskningsrapporter kan du få svar på frågor som:

- Vilka typer av uppdateringar har getts användare?

- Hur många användare ändrades?

- Hur många lösenord ändrades?

- Vad har en administratör gjort i en katalog?

- Vilka grupper har lagts till?

- Finns det grupper med ändringar i medlemskap?

- Har ägare av en grupp ändrats?

- Vilka licenser har tilldelats en grupp eller en användare?

Om du bara vill kontrollera granskningsdata relaterade till användare och grupper finns det en filtrerad vy under **Granskningsloggar** i avsnittet **Aktivitet** i **Användare och grupper**. I det här området är **Användare och grupper** den förvalda **aktivitetsresurstypen**.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/93.png "Granskningsloggar")

### <a name="enterprise-applications-audit-logs"></a>Granskningsloggar för företagsprogram

Med programbaserade granskningsrapporter kan du få svar på frågor som:

* Vilka program har lagts till eller uppdaterats?
* Vilka program har tagits bort?
* Har tjänstprincipen för ett program ändrats?
* Har namnen på program ändrats?
* Vem gav tillstånd till ett program?

Om du bara vill kontrollera granskningsdata relaterade till dina program finns det en filtrerad vy under **Granskningsloggar** i avsnittet **Aktivitet** på bladet **Företagsprogram**. I det här området är **Företagsprogram** den förvalda **aktivitetsresurstypen**.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/134.png "Granskningsloggar")

Du kan filtrera den här vyn ytterligare till bara **grupper** eller bara **användare**.

![Granskningsloggar](./media/active-directory-reporting-activity-audit-logs/25.png "Granskningsloggar")



## <a name="next-steps"></a>Nästa steg

- En översikt över rapportering finns i [Azure Active Directory-rapportering](active-directory-reporting-azure-portal.md).

- En fullständig lista med alla granskningsaktiviteter finns i [referensen för granskningsaktiviteter i Azure AD](active-directory-reporting-activity-audit-reference.md)

