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
ms.openlocfilehash: 5895ad1f1452b5a0c13765821c5b623472a0bb1e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447123"
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

* Användare i den **säkerhetsadministratör**, **Säkerhetsläsare** eller **Global administratör** roller
* Dessutom kan kan alla användare (icke-administratörer) Se sina egna granskningsaktiviteter

## <a name="audit-logs"></a>Granskningsloggar

Azure AD-audit-loggarna ger dokumentation över systemaktiviteter för efterlevnad av. För att komma åt granskningsrapporten, Välj **granskningsloggar** i den **aktivitet** delen av **Azure Active Directory**. Observera att granskningsloggar kan ha en fördröjning på upp till en timme, så det kan ta så lång tid för aktiviteten granskningsdata visas i portalen när du har slutfört uppgiften.

![Granskningsloggar](./media/concept-audit-logs/61.png "Granskningsloggar")

En granskningslogg har en standardlistvy som visar:

- datum och tid för förekomsten
- initieraren/aktören (*vem*) för en aktivitet 
- aktiviteten (*vad*) 
- målet

![Granskningsloggar](./media/concept-audit-logs/18.png "Granskningsloggar")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Granskningsloggar](./media/concept-audit-logs/19.png "Granskningsloggar")

På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.

![Granskningsloggar](./media/concept-audit-logs/21.png "Granskningsloggar")

Markera ett objekt i listvyn för att få mer detaljerad information.

![Granskningsloggar](./media/concept-audit-logs/22.png "Granskningsloggar")


## <a name="filtering-audit-logs"></a>Filtrera granskningsloggar

Du kan filtrera granskningsdata på följande fält:

- Datumintervall
- Initierad av (aktör)
- Kategori
- Resurstyp för aktivitet
- Aktivitet

![Granskningsloggar](./media/concept-audit-logs/23.png "Granskningsloggar")

Med filtret för **datumintervall** kan du definiera en tidsram för de data som returneras.  
Möjliga värden:

- 1 månad
- 7 dagar
- 24 timmar
- Anpassat

När du väljer en anpassad tidsram kan du konfigurera en starttid och en sluttid.

Den **initieras av** filter kan du definiera en aktörs namn eller ett universal principal name (UPN).

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


Den **aktivitet** filter baserat på kategori och aktivitet resursen typ du väljer. Du kan välja en specifik aktivitet som du vill visa eller välja alla. 

Du kan hämta listan över alla granskningsaktiviteter med Graph API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, där $tenantdomain = ditt domännamn eller refererar till artikeln [Granska rapporthändelser](reference-audit-activities.md).

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

![Granskningsloggar](./media/concept-audit-logs/93.png "Granskningsloggar")

### <a name="enterprise-applications-audit-logs"></a>Granskningsloggar för företagsprogram

Med programbaserade granskningsrapporter kan du få svar på frågor som:

* Vilka program har lagts till eller uppdaterats?
* Vilka program har tagits bort?
* Har ett huvudnamn för tjänsten för ett program ändrats?
* Har namnen på program ändrats?
* Vem gav tillstånd till ett program?

Om du vill kontrollera granskningsdata relaterade till dina program kan du hitta en filtrerad vy under **granskningsloggar** i den **aktivitet** delen av den **företagsprogram** blad. Den här registreringspunkten har **företagsprogram** förvalda som den **Aktivitetsresurstyp**.

![Granskningsloggar](./media/concept-audit-logs/134.png "Granskningsloggar")

Du kan filtrera den här vyn ned till **grupper** eller **användare**.

![Granskningsloggar](./media/concept-audit-logs/25.png "Granskningsloggar")

## <a name="office-365-activity-logs"></a>Office 365-aktivitetsloggar

Du kan visa Office 365-aktivitetsloggar från den [Office 365 Admin Center](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Även om aktivitet för Office 365 och Azure AD delar en massa directory-resurser, endast Office 365 Admin Center ger en fullständig överblick över aktivitetsloggarna i Office 365. 

Du kan också komma åt Office 365-aktivitetsloggar programmässigt med hjälp av den [API: er för Office 365-Management](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Nästa steg

- [Referens för granskningsaktiviteter i Azure AD](reference-audit-activities.md)
- [Kvarhållning referens för Azure AD-rapporter](reference-reports-data-retention.md)
- [Referera till Azure AD log svarstider](reference-reports-latencies.md)
