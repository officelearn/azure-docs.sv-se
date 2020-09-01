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
ms.openlocfilehash: 01c9449a996929eb20acf35897a100dfbb722e82
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229930"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Granska aktivitetsrapporter i Azure Active Directory-portalen 

Med Azure Active Directory (Azure AD)-rapporter kan du få den information du behöver för att avgöra hur din miljö fungerar.

Rapporterings arkitekturen består av följande komponenter:

- **Aktivitet** 
    - **Inloggningar** – [inloggnings rapporten](concept-sign-ins.md) innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.
    - **Granskningsloggar** – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer.
- **Säkerhet** 
    - **Riskfyllda inloggningar** – en [riskfylld inloggning](../identity-protection/overview-identity-protection.md) är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är en legitim ägare till ett användar konto. 
    - **Användare som har flaggats för risk** – en [riskfylld användare](../identity-protection/overview-identity-protection.md) är en indikator för ett användar konto som kan ha komprometterats.

Den här artikeln ger en översikt över gransknings rapporten.
 
## <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?

* Användare i rollerna **säkerhets administratör**, **säkerhets läsare**, **rapport läsare** , **global läsare** eller **Global administratör**

## <a name="audit-logs"></a>Granskningsloggar

I gransknings loggarna i Azure AD finns register över system aktiviteter för efterlevnad. Välj **gransknings loggar** i avsnittet **övervakning** i **Azure Active Directory**för att få åtkomst till gransknings rapporten. Observera att gransknings loggar kan ha en fördröjning på upp till en timme, så det kan ta lång tid för gransknings aktivitets data att visas i portalen när du har slutfört uppgiften.



En granskningslogg har en standardlistvy som visar:

- datum och tid för förekomsten
- tjänsten som loggade förekomsten
- aktivitetens kategori och namn (*vad*) 
- aktivitetens status (lyckades eller misslyckades)
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

Du kan filtrera gransknings data i följande fält:

- Tjänst
- Kategori
- Aktivitet
- Status
- Mål
- Initierad av (aktör)
- Datumintervall

![Granskningsloggar](./media/concept-audit-logs/filter.png "Granskningsloggar")

Med **tjänst** filtret kan du välja från en listruta med följande tjänster:

- Alla
- AAD-hantering UX
- Åtkomstgranskningar
- Kontoetablering
- Programproxy
- Autentiseringsmetoder
- B2C
- Villkorlig åtkomst
- Kärnkatalog
- Hantering av rättigheter
- Hybrid autentisering
- Identity Protection
- Inbjudna användare
- MIM-tjänst
- MyApps
- PIM
- Självbetjäning, grupphantering
- Hantering av lösenord för självbetjäning
- Användningsvillkor

Med filtret **kategori** kan du välja något av följande filter:

- Alla
- AdministrativeUnit
- ApplicationManagement
- Autentisering
- Auktorisering
- Kontakt
- Enhet
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- Hantering av
- Etikett
- Övrigt
- PermissionGrantPolicy
- Policy
- ResourceManagement
- RoleManagement
- UserManagement

**Aktivitets** filtret baseras på resurs typs valet kategori och aktivitet som du gör. Du kan välja en specifik aktivitet som du vill visa eller välja alla. 

Du kan hämta listan över alla gransknings aktiviteter med hjälp av Graph API: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Med **status** filtret kan du filtrera baserat på status för en gransknings åtgärd. Statusen kan vara något av följande:

- Alla
- Klart
- Fel

Med **mål** filtret kan du söka efter ett visst mål genom att starta namnet eller User Principal Name (UPN). Mål namnet och UPN är Skift läges känsliga. 

Med filtret **initierad av** kan du definiera vad en aktörs namn eller ett universellt huvud namn (UPN) börjar med. Namnet och UPN är Skift läges känsliga.

Med filtret för **datum intervall** kan du definiera en tidsram för de data som returneras.  
Möjliga värden:

- 7 dagar
- 24 timmar
- Anpassad

När du väljer en anpassad tidsram kan du konfigurera en starttid och en sluttid.

Du kan också välja att ladda ned filtrerade data, upp till 250 000 poster, genom att välja knappen **Hämta** . Du kan hämta loggarna i antingen CSV-eller JSON-format. Antalet poster som du kan hämta begränsas av [Azure Active Directory bevarande principer för rapporter](reference-reports-data-retention.md).

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

Om du bara vill granska gransknings data som är relaterade till användare kan du hitta en filtrerad vy under **gransknings loggar** i avsnittet **övervakning** på fliken **användare** . Den här start punkten har **UserManagement** som förvalt kategori.

![Granskningsloggar](./media/concept-audit-logs/users.png "Granskningsloggar")

Om du bara vill granska gransknings data som är relaterade till grupper kan du hitta en filtrerad vy under **gransknings loggar** i avsnittet **övervakning** på fliken **grupper** . Den här start punkten har **GroupManagement** som förvalt kategori.

![Granskningsloggar](./media/concept-audit-logs/groups.png "Granskningsloggar")

### <a name="enterprise-applications-audit-logs"></a>Granskningsloggar för företagsprogram

Med programbaserade granskningsrapporter kan du få svar på frågor som:

* Vilka program har lagts till eller uppdaterats?
* Vilka program har tagits bort?
* Har ett tjänst huvud namn för ett program ändrats?
* Har namnen på program ändrats?
* Vem gav tillstånd till ett program?

Om du vill granska gransknings data som är relaterade till dina program kan du hitta en filtrerad vy under **gransknings loggar** i avsnittet **aktivitet** på bladet **företags program** . Den här start punkten har **företags program** som är förvalda som **program typ**.

![Granskningsloggar](./media/concept-audit-logs/enterpriseapplications.png "Granskningsloggar")

## <a name="office-365-activity-logs"></a>Office 365-aktivitets loggar

Du kan visa Office 365-aktivitets loggar från [Microsoft 365 administrations Center](/office365/admin/admin-overview/about-the-admin-center). Även om Office 365-aktivitet och Azure AD-aktivitets loggar delar mycket av katalog resurserna, ger endast Microsoft 365 administrations Center en fullständig översikt över aktivitets loggarna för Office 365. 

Du kan också få åtkomst till Office 365-aktivitets loggarna via programmering med hjälp av [office 365 Management-API: er](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Nästa steg

- [Referens över granskningsaktiviteter i Azure AD](reference-audit-activities.md)
- [Referens för kvarhållning av Azure AD-rapporter](reference-reports-data-retention.md)
- [Referens för fördröjning i Azure AD-logg](reference-reports-latencies.md)