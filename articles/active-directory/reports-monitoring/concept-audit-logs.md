---
title: Granska aktivitetsrapporter i Azure Active Directory-portalen | Microsoft Docs
description: Introduktion till granskning av aktivitetsrapporter i Azure Active Directory-portalen
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65bc0c0ee1ccc1e1f3da5e364582534dfbc0d425
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530713"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Granska aktivitetsrapporter i Azure Active Directory-portalen 

Med Azure Active Directory (Azure AD) rapporter får du den information du behöver för att fastställa hur din miljö.

Rapporteringsarkitekturen består av följande komponenter:

- **Aktivitet** 
    - **Inloggningar** – den [rapporten inloggningar](concept-sign-ins.md) innehåller information om användningen av hanterade program och användare logga in aktiviteter.
    - **Granskningsloggar** – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.
- **Säkerhet** 
    - **Riskfyllda inloggningar** – en [riskfyllda inloggningen](concept-risky-sign-ins.md) indikerar en inloggningsförsök som kan ha utförts av någon som inte är tillförlitligt ägare för ett användarkonto. 
    - **Användare som har flaggats för risk** – en [riskfylld användare](concept-user-at-risk.md) är en indikator för ett användarkonto som kan ha komprometterats.

Den här artikeln ger en översikt över granskningsrapporten.
 
## <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?

* Användare i den **Security administratör i fältet**, **Säkerhetsläsare**, **rapportläsare** eller **Global administratör** roller
* Dessutom kan kan alla användare (icke-administratörer) Se sina egna granskningsaktiviteter

## <a name="audit-logs"></a>Granskningsloggar

Azure AD-audit-loggarna ger dokumentation över systemaktiviteter för efterlevnad av. För att komma åt granskningsrapporten, Välj **granskningsloggar** i den **aktivitet** delen av **Azure Active Directory**. Observera att granskningsloggar kan ha en fördröjning på upp till en timme, så det kan ta så lång tid för aktiviteten granskningsdata visas i portalen när du har slutfört uppgiften.

![Granskningsloggar](./media/concept-audit-logs/61.png "Granskningsloggar")

En granskningslogg har en standardlistvy som visar:

- datum och tid för förekomsten
- den tjänst som visar
- kategori och namnet på aktiviteten (*vad*) 
- status för aktivitet (lyckade eller misslyckade)
- målet
- Initieraren / aktören (vem) för en aktivitet

![Granskningsloggar](./media/concept-audit-logs/listview.png "Granskningsloggar")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Granskningsloggar](./media/concept-audit-logs/columns.png "Granskningsloggar")

På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.

![Granskningsloggar](./media/concept-audit-logs/columnselect.png "Granskningsloggar")

Markera ett objekt i listvyn för att få mer detaljerad information.

![Granskningsloggar](./media/concept-audit-logs/details.png "Granskningsloggar")


## <a name="filtering-audit-logs"></a>Filtrera granskningsloggar

Du kan filtrera granskningsdata på följande fält:

- Tjänst
- Kategori
- Aktivitet
- Status
- Mål
- Initierad av (aktör)
- Datumintervall

![Granskningsloggar](./media/concept-audit-logs/filter.png "Granskningsloggar")

Den **Service** filter kan du välja från en listruta för följande tjänster:

- Alla
- Åtkomstgranskningar
- Kontoetablering 
- Programmet SSO
- Autentiseringsmetoder
- B2C
- Villkorlig åtkomst
- Kärnkatalog
- Hantering av behörighet
- Identity Protection
- Inbjudna användare
- PIM
- Självbetjäning, grupphantering
- Självbetjäning Passord Management
- Användningsvillkor

Den **kategori** filter kan du välja något av följande filter:

- Alla
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Auktorisering
- Kontakt
- Enhet
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Annat
- Princip
- ResourceManagement
- RoleManagement
- UserManagement

Den **aktivitet** filter baserat på kategori och aktivitet resursen typ du väljer. Du kan välja en specifik aktivitet som du vill visa eller välja alla. 

Du kan hämta listan över alla granskningsaktiviteter med Graph API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, där $tenantdomain = ditt domännamn eller refererar till artikeln [Granska rapporthändelser](reference-audit-activities.md).

Den **Status** filter kan du filtrera baserat på status för en åtgärd för granskning. Statusen kan vara något av följande:

- Alla
- Lyckades
- Fel

Den **Target** filter kan du söka efter ett visst mål efter namn eller användarens huvudnamn (UPN). Målnamn och UPN är skiftlägeskänsliga. 

Den **initieras av** filter kan du definiera en aktörs namn eller ett universal principal name (UPN). Namn- och UPN är skiftlägeskänsliga.

Den **datumintervall** filter kan du definiera en tidsram för data som returneras.  
Möjliga värden:

- 1 månad
- 7 dagar
- 24 timmar
- Anpassat

När du väljer en anpassad tidsram kan du konfigurera en starttid och en sluttid.

Du kan också välja att hämta filtrerade data, upp till 250 000 poster genom att välja den **hämta** knappen. Du kan välja att hämta loggarna i CSV eller JSON-format. Antalet poster som du kan ladda ned är begränsad av den [rapportkvarhållningsregler i Azure Active Directory](reference-reports-data-retention.md).

![Granskningsloggar](./media/concept-audit-logs/download.png "Granskningsloggar")

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

Om du bara vill kontrollera granskningsdata som relateras till användare kan du hitta en filtrerad vy under **granskningsloggar** i den **aktivitet** delen av den **användare** fliken. Den här registreringspunkten har **UserManagement** som förvalda kategori.

![Granskningsloggar](./media/concept-audit-logs/users.png "Granskningsloggar")

Om du bara vill kontrollera granskningsdata som relateras till grupper kan du hitta en filtrerad vy under **granskningsloggar** i den **aktivitet** delen av den **grupper** fliken. Den här registreringspunkten har **GroupManagement** som förvalda kategori.

![Granskningsloggar](./media/concept-audit-logs/groups.png "Granskningsloggar")

### <a name="enterprise-applications-audit-logs"></a>Granskningsloggar för företagsprogram

Med programbaserade granskningsrapporter kan du få svar på frågor som:

* Vilka program har lagts till eller uppdaterats?
* Vilka program har tagits bort?
* Har ett huvudnamn för tjänsten för ett program ändrats?
* Har namnen på program ändrats?
* Vem gav tillstånd till ett program?

Om du vill kontrollera granskningsdata relaterade till dina program kan du hitta en filtrerad vy under **granskningsloggar** i den **aktivitet** delen av den **företagsprogram** blad. Den här registreringspunkten har **företagsprogram** förvalda som den **programtyp**.

![Granskningsloggar](./media/concept-audit-logs/enterpriseapplications.png "Granskningsloggar")

## <a name="office-365-activity-logs"></a>Office 365-aktivitetsloggar

Du kan visa Office 365-aktivitetsloggar från den [Office 365 Admin Center](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Även om aktivitet för Office 365 och Azure AD delar en massa directory-resurser, endast Office 365 Admin Center ger en fullständig överblick över aktivitetsloggarna i Office 365. 

Du kan också komma åt Office 365-aktivitetsloggar programmässigt med hjälp av den [API: er för Office 365-Management](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Nästa steg

- [Referens för granskningsaktiviteter i Azure AD](reference-audit-activities.md)
- [Kvarhållning referens för Azure AD-rapporter](reference-reports-data-retention.md)
- [Referera till Azure AD log svarstider](reference-reports-latencies.md)
