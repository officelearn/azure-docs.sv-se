---
title: Granska aktivitetsrapporter i Azure Active Directory-portalen | Microsoft Docs
description: Introduktion till granskning av aktivitetsrapporter i Azure Active Directory-portalen
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad399fc24b2cdfbdc51e7feccba2c05786216b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253239"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Granska aktivitetsrapporter i Azure Active Directory-portalen 

Med Azure Active Directory (Azure AD) rapporter kan du få den information du behöver för att avgöra hur din miljö går.

Rapportarkitekturen består av följande komponenter:

- **Aktivitet** 
    - **Inloggningar** – [Inloggningsrapporten](concept-sign-ins.md) innehåller information om användningen av hanterade program och användarloggningsaktiviteter.
    - **Granskningsloggar** – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är ändringar som gjorts i alla resurser i Azure AD, till exempel lägga till eller ta bort användare, appar, grupper, roller och principer.
- **Säkerhet** 
    - **Riskfyllda inloggningar** - En [riskfylld inloggning](concept-risky-sign-ins.md) är en indikator för ett inloggningsförsök som kan ha utförts av någon som inte är den legitima ägaren av ett användarkonto. 
    - **Användare som flaggats för risk** – En [riskfylld användare](concept-user-at-risk.md) är en indikator för ett användarkonto som kan ha komprometterats.

I den här artikeln får du en översikt över granskningsrapporten.
 
## <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?

* Användare i **rollerna Säkerhetsadministratör**, **Säkerhetsläsare**, **Rapportläsare** , **Global Läsare** eller Global **Administratör**

## <a name="audit-logs"></a>Granskningsloggar

Azure AD-granskningsloggarna tillhandahåller register över systemaktiviteter för efterlevnad. Om du vill komma åt granskningsrapporten väljer du **Granskningsloggar** i avsnittet **Övervakning** i **Azure Active Directory**. Observera att granskningsloggar kan ha en svarstid på upp till en timme, så det kan ta så lång tid för granskningsaktivitetsdata att visas i portalen när du har slutfört uppgiften.



En granskningslogg har en standardlistvy som visar:

- datum och tid för förekomsten
- tjänsten som loggade förekomsten
- aktivitetens kategori och namn (*vad)* 
- aktivitetens status (framgång eller misslyckande)
- målet
- initieraren/aktören (vem) för en aktivitet

![Granskningsloggar](./media/concept-audit-logs/listview.png "Granskningsloggar")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Granskningsloggar](./media/concept-audit-logs/columns.png "Granskningsloggar")

På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.

![Granskningsloggar](./media/concept-audit-logs/columnselect.png "Granskningsloggar")

Välj ett objekt i listvyn om du vill ha mer detaljerad information.

![Granskningsloggar](./media/concept-audit-logs/details.png "Granskningsloggar")


## <a name="filtering-audit-logs"></a>Filtrera granskningsloggar

Du kan filtrera granskningsdata i följande fält:

- Tjänst
- Kategori
- Aktivitet
- Status
- Mål
- Initierad av (aktör)
- Datumintervall

![Granskningsloggar](./media/concept-audit-logs/filter.png "Granskningsloggar")

Med **filtret Service** kan du välja från en listruta med följande tjänster:

- Alla
- AAD Management UX
- Åtkomstgranskningar
- Kontoetablering
- Programproxy
- Autentiseringsmetoder
- B2C
- Villkorlig åtkomst
- Kärnkatalog
- Hantering av rättigheter
- Hybridautentisering
- Identity Protection
- Inbjudna användare
- MIM-tjänst
- MyApps
- PIM
- Självbetjäning, grupphantering
- Hantering av lösenord för självbetjäning
- Användningsvillkor

Med **filtret Kategori** kan du välja något av följande filter:

- Alla
- AdministrativeUnit
- ProgramManagement
- Autentisering
- Auktorisering
- Kontakt
- Enhet
- DeviceConfiguration
- KatalogHanagement
- Rättshantagement
- GruppHantverk
- KerberosDomain (1999)
- KeyManagement (KeyManage)
- Label (Etikett)
- Annat
- BehörighetGrantPolicy
- Princip
- ResursHantering
- RollHanagement
- UserManagement Användare

**Aktivitetsfiltret** baseras på den kategori- och aktivitetsresurstypval du gör. Du kan välja en specifik aktivitet som du vill visa eller välja alla. 

Du kan få en lista över alla granskningsaktiviteter med graph-API:et:`https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Med **filtret Status** kan du filtrera baserat på status för en granskningsåtgärd. Statusen kan vara något av följande:

- Alla
- Lyckades
- Fel

Med filtret **Mål** kan du söka efter ett visst mål genom att starta namnet eller användarnamnet (UPN). Målnamnet och UPN är skiftlägeskänsliga. 

**Med filtret Initierad av** kan du definiera vad en aktörs namn eller ett universellt huvudnamn (UPN) börjar med. Namnet och UPN är skiftlägeskänsliga.

Med filtret **Datumintervall** kan du definiera en tidsram för returnerade data.  
Möjliga värden:

- 7 dagar
- 24 timmar
- Anpassat

När du väljer en anpassad tidsram kan du konfigurera en starttid och en sluttid.

Du kan också välja att hämta filtrerade data, upp till 250 000 poster, genom att välja **knappen Hämta.** Du kan ladda ner loggarna i antingen CSV eller JSON-format. Antalet poster som du kan hämta begränsas av [Azure Active Directory-rapportlagringsprinciperna](reference-reports-data-retention.md).

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

Om du bara vill granska granskningsdata som är relaterade till användare kan du hitta en filtrerad vy under **Granskningsloggar** i avsnittet **Övervakning** på fliken **Användare.** Den här startpunkten har **UserManagement** som förvald kategori.

![Granskningsloggar](./media/concept-audit-logs/users.png "Granskningsloggar")

Om du bara vill granska granskningsdata som är relaterade till grupper kan du hitta en filtrerad vy under **Granskningsloggar** i avsnittet **Övervakning** på fliken **Grupper.** Den här startpunkten har **GroupManagement** som förvald kategori.

![Granskningsloggar](./media/concept-audit-logs/groups.png "Granskningsloggar")

### <a name="enterprise-applications-audit-logs"></a>Granskningsloggar för företagsprogram

Med programbaserade granskningsrapporter kan du få svar på frågor som:

* Vilka program har lagts till eller uppdaterats?
* Vilka program har tagits bort?
* Har ett tjänsthuvudnamn för ett program ändrats?
* Har namnen på program ändrats?
* Vem gav tillstånd till ett program?

Om du vill granska granskningsdata som är relaterade till dina program kan du hitta en filtrerad vy under **Granskningsloggar** i avsnittet **Aktivitet** i **bladet Företagsprogram.** Den här startpunkten har **företagsprogram** förvalda som **programtyp**.

![Granskningsloggar](./media/concept-audit-logs/enterpriseapplications.png "Granskningsloggar")

## <a name="office-365-activity-logs"></a>Aktivitetsloggar för Office 365

Du kan visa Aktivitetsloggar för Office 365 från [administrationscentret för Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Även om Office 365-aktivitets- och Azure AD-aktivitetsloggar delar en hel del katalogresurser är det bara Microsoft 365-administrationscentret som ger en fullständig bild av office 365-aktivitetsloggarna. 

Du kan också komma åt Office 365-aktivitetsloggarna programmässigt med hjälp av [Api:erna för Office 365-hantering](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Nästa steg

- [Referens över granskningsaktiviteter i Azure AD](reference-audit-activities.md)
- [Bevarandereferens för Azure AD-rapporter](reference-reports-data-retention.md)
- [Referens för Azure AD-loggtvådligheter](reference-reports-latencies.md)
