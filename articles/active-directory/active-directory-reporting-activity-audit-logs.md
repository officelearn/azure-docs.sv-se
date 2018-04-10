---
title: Granska aktivitetsrapporter i Azure Active Directory-portalen | Microsoft Docs
description: Introduktion till granskning av aktivitetsrapporter i Azure Active Directory-portalen
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/31/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 97ea32a1e0f8815accff6201251771ab8c088859
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Granska aktivitetsrapporter i Azure Active Directory-portalen 

Med rapportering i Azure Active Directory (Azure AD) får du all information du behöver för att ta reda på hur din miljö klarar sig.

Rapporteringsarkitekturen i Azure AD består av följande komponenter:

- **Aktivitet** 
    - **Inloggningsaktiviteter** – Information om användningen av hanterade program och användares inloggningsaktiviteter
    - **Granskningsloggar** – Granska information om systemaktivitet för användare och grupphantering, dina hanterade program och katalogaktiviteter.
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



## <a name="azure-ad-audit-activity-list"></a>Azure AD-granskningsaktivitetslista

I det här avsnittet finns en lista över alla aktiviteter som kan loggas. 


|Tjänstnamn|Granskningskategori|Aktivitetsresurstyp|Aktivitet|
|---|---|---|---|
|Kontoetablering|Programhantering|Program|Administration|
|Kontoetablering|Programhantering|Program|Katalogåtgärd|
|Kontoetablering|Programhantering|Program|Exportera|
|Kontoetablering|Programhantering|Program|Importera|
|Kontoetablering|Programhantering|Program|Annat|
|Kontoetablering|Programhantering|Program|Processdeponering|
|Kontoetablering|Programhantering|Program|Synkroniseringsregelåtgärd|
|Programproxy|Programhantering|Program|Lägga till ett program|
|Programproxy|Resurs|Resurs|Lägg till SSL-certifikat för program|
|Programproxy|Resurs|Resurs|Ta bort SSL-bindning|
|Programproxy|Programhantering|Program|Ta bort program|
|Programproxy|Kataloghantering|Katalog|Inaktivera enkel inloggning för skrivbord|
|Programproxy|Kataloghantering|Katalog|Inaktivera skrivbords-Sso för en specifik domän|
|Programproxy|Kataloghantering|Katalog|Inaktivera programproxy|
|Programproxy|Kataloghantering|Katalog|Inaktivera direktautentisering|
|Programproxy|Kataloghantering|Katalog|Aktivera enkel inloggning för skrivbord|
|Programproxy|Kataloghantering|Katalog|Aktivera skrivbords-Sso för en specifik domän|
|Programproxy|Kataloghantering|Katalog|Aktivera Application Proxy|
|Programproxy|Kataloghantering|Katalog|Aktivera direktautentisering|
|Programproxy|Resurs|Resurs|Registrera anslutningsapp|
|Programproxy|Programhantering|Program|Uppdatera programmet|
|Programproxy|Programhantering|Program|Uppdatera läge för enkel inloggning för program|
|Automatiserad förnyelse av lösenord|Programhantering|Program|Automatiserad förnyelse av lösenord|
|B2C|Programhantering|Program|Lägga till V2-programbehörigheter|
|B2C|Auktorisering|Auktorisering|Lägga till V2-programbehörigheter|
|B2C|Nyckel|Nyckel|Lägga till en nyckel som baseras på en ASCII-hemlighet till en CPIM-nyckelbehållare|
|B2C|Auktorisering|Auktorisering|Lägg till en nyckel som baseras på en ASCII-hemlighet till en CPIM-nyckelbehållare|
|B2C|Nyckel|Nyckel|Lägg till en nyckel till en CPIM-nyckelbehållare|
|B2C|Auktorisering|Auktorisering|Lägg till en nyckel till en CPIM-nyckelbehållare|
|B2C|Resurs|Resurs|AdminPolicyDatas-RemoveResources|
|B2C|Auktorisering|Auktorisering|AdminPolicyDatas-SetResources|
|B2C|Resurs|Resurs|AdminPolicyDatas-SetResources|
|B2C|Resurs|Resurs|AdminUserJourneys-GetResources|
|B2C|Auktorisering|Auktorisering|AdminUserJourneys-GetResources|
|B2C|Auktorisering|Auktorisering|AdminUserJourneys-RemoveResources|
|B2C|Resurs|Resurs|AdminUserJourneys-RemoveResources|
|B2C|Resurs|Resurs|AdminUserJourneys-SetResources|
|B2C|Auktorisering|Auktorisering|AdminUserJourneys-SetResources|
|B2C|Auktorisering|Auktorisering|Skapa IdentityProvider|
|B2C|Resurs|Resurs|Skapa IdentityProvider|
|B2C|Auktorisering|Auktorisering|Skapa V1-program|
|B2C|Programhantering|Program|Skapa V1-program|
|B2C|Programhantering|Program|Skapa V2-program|
|B2C|Auktorisering|Auktorisering|Skapa V2-program|
|B2C|Kataloghantering|Katalog|Skapa en anpassad domän i klienten|
|B2C|Auktorisering|Auktorisering|Skapa en anpassad domän i klienten|
|B2C|Auktorisering|Auktorisering|Skapa ny AdminUserJourney|
|B2C|Resurs|Resurs|Skapa ny AdminUserJourney|
|B2C|Resurs|Resurs|Skapa json för lokaliserad resurs|
|B2C|Auktorisering|Auktorisering|Skapa json för lokaliserad resurs|
|B2C|Auktorisering|Auktorisering|Skapa ny anpassad IDP|
|B2C|Resurs|Resurs|Skapa ny anpassad IDP|
|B2C|Resurs|Resurs|Skapa ny IDP|
|B2C|Auktorisering|Auktorisering|Skapa ny IDP|
|B2C|Auktorisering|Auktorisering|Skapa eller uppdatera en B2C-katalogresurs|
|B2C|Resurs|Resurs|Skapa eller uppdatera en B2C-katalogresurs|
|B2C|Resurs|Resurs|Skapa princip|
|B2C|Auktorisering|Auktorisering|Skapa princip|
|B2C|Auktorisering|Auktorisering|Skapa trustFramework-princip|
|B2C|Resurs|Resurs|Skapa trustFramework-princip|
|B2C|Auktorisering|Auktorisering|Skapa trustFramework-princip med konfigurerbart prefix|
|B2C|Resurs|Resurs|Skapa trustFramework-princip med konfigurerbart prefix|
|B2C|Resurs|Resurs|Skapa användarattribut|
|B2C|Auktorisering|Auktorisering|Skapa användarattribut|
|B2C|Auktorisering|Auktorisering|CreateTrustFrameworkPolicy|
|B2C|Resurs|Resurs|CreateTrustFrameworkPolicy|
|B2C|Auktorisering|Auktorisering|Skapar eller uppdaterar en ny AdminUserJourney|
|B2C|Resurs|Resurs|Ta bort IDP|
|B2C|Auktorisering|Auktorisering|Ta bort IDP|
|B2C|Resurs|Resurs|Ta bort IdentityProvider|
|B2C|Auktorisering|Auktorisering|Ta bort IdentityProvider|
|B2C|Programhantering|Program|Ta bort V1-program|
|B2C|Auktorisering|Auktorisering|Ta bort V1-program|
|B2C|Programhantering|Program|Ta bort V2-program|
|B2C|Auktorisering|Auktorisering|Ta bort V2-program|
|B2C|Auktorisering|Auktorisering|Ta bort behörighetsbeviljande för V2-program|
|B2C|Programhantering|Program|Ta bort behörighetsbeviljande för V2-program|
|B2C|Resurs|Resurs|Ta bort en B2C-katalogresurs|
|B2C|Auktorisering|Auktorisering|Ta bort en B2C-katalogresurs|
|B2C|Nyckel|Nyckel|Ta bort en CPIM-nyckelbehållare|
|B2C|Auktorisering|Auktorisering|Ta bort en CPIM-nyckelbehållare|
|B2C|Nyckel|Nyckel|Ta bort nyckelbehållare|
|B2C|Resurs|Resurs|Ta bort trustFramework-princip|
|B2C|Auktorisering|Auktorisering|Ta bort trustFramework-princip|
|B2C|Resurs|Resurs|Ta bort användarattribut|
|B2C|Auktorisering|Auktorisering|Ta bort användarattribut|
|B2C|Auktorisering|Auktorisering|Aktivera B2C-funktion|
|B2C|Kataloghantering|Katalog|Aktivera B2C-funktion|
|B2C|Resurs|Resurs|Hämta B2C-katalogresurser i en resursgrupp|
|B2C|Resurs|Resurs|Hämta B2C-katalogresurser i en prenumeration|
|B2C|Auktorisering|Auktorisering|Hämta B2C-katalogresurser i en prenumeration|
|B2C|Auktorisering|Auktorisering|Hämta anpassad IDP|
|B2C|Resurs|Resurs|Hämta anpassad IDP|
|B2C|Resurs|Resurs|Hämta IDP|
|B2C|Auktorisering|Auktorisering|Hämta IDP|
|B2C|Auktorisering|Auktorisering|Hämta V1- och V2-program|
|B2C|Programhantering|Program|Hämta V1- och V2-program|
|B2C|Auktorisering|Auktorisering|Hämta V1-program|
|B2C|Programhantering|Program|Hämta V1-program|
|B2C|Auktorisering|Auktorisering|Hämta V1-program|
|B2C|Programhantering|Program|Hämta V1-program|
|B2C|Programhantering|Program|Hämta V2-program|
|B2C|Auktorisering|Auktorisering|Hämta V2-program|
|B2C|Programhantering|Program|Hämta V2-program|
|B2C|Auktorisering|Auktorisering|Hämta V2-program|
|B2C|Resurs|Resurs|Hämta en B2C-katalogresurs|
|B2C|Auktorisering|Auktorisering|Hämta en B2C-katalogresurs|
|B2C|Auktorisering|Auktorisering|Skapa en lista över anpassade domäner i klienten|
|B2C|Kataloghantering|Katalog|Skapa en lista över anpassade domäner i klienten|
|B2C|Auktorisering|Auktorisering|Hämta en användarresa|
|B2C|Resurs|Resurs|Hämta en användarresa|
|B2C|Resurs|Resurs|Hämta tillåtna programanspråk för användarresa|
|B2C|Auktorisering|Auktorisering|Hämta tillåtna programanspråk för användarresa|
|B2C|Resurs|Resurs|Hämta tillåtna självkontrollerade anspråk för användarresa|
|B2C|Auktorisering|Auktorisering|Hämta tillåtna självkontrollerade anspråk för användarresa|
|B2C|Resurs|Resurs|Hämta tillåtna självkontrollerade anspråk för princip|
|B2C|Auktorisering|Auktorisering|Hämta tillåtna självkontrollerade anspråk för princip|
|B2C|Resurs|Resurs|Hämta tillgänglig lista över utdataanspråk|
|B2C|Auktorisering|Auktorisering|Hämta tillgänglig lista över utdataanspråk|
|B2C|Resurs|Resurs|Hämta innehållsdefinitioner för användarresa|
|B2C|Auktorisering|Auktorisering|Hämta innehållsdefinitioner för användarresa|
|B2C|Auktorisering|Auktorisering|Hämta idp:er för ett specifikt administratörsflöde|
|B2C|Resurs|Resurs|Hämta idp:er för ett specifikt administratörsflöde|
|B2C|Nyckel|Nyckel|Hämta nyckelbehållare för metadata för aktiv nyckel i JWK|
|B2C|Auktorisering|Auktorisering|Hämta nyckelbehållare för metadata för aktiv nyckel i JWK|
|B2C|Nyckel|Nyckel|Hämta metadata för nyckelbehållare|
|B2C|Resurs|Resurs|Hämta lista över alla administratörsflöden|
|B2C|Auktorisering|Auktorisering|Hämta lista över alla administratörsflöden|
|B2C|Auktorisering|Auktorisering|Hämta lista över taggar för alla administratörsflöden för alla användare|
|B2C|Resurs|Resurs|Hämta lista över taggar för alla administratörsflöden för alla användare|
|B2C|Resurs|Resurs|Hämta lista över klienter för en användare|
|B2C|Auktorisering|Auktorisering|Hämta lista över klienter för en användare|
|B2C|Resurs|Resurs|Hämta lokala kontos självkontrollerade anspråk|
|B2C|Auktorisering|Auktorisering|Hämta självkontrollerade anspråk för lokala konton|
|B2C|Resurs|Resurs|Hämta json för lokaliserad resurs|
|B2C|Auktorisering|Auktorisering|Hämta json för lokaliserad resurs|
|B2C|Auktorisering|Auktorisering|Hämta åtgärder för resursprovidern Microsoft.AzureActiveDirectory|
|B2C|Resurs|Resurs|Hämta åtgärder för resursprovidern Microsoft.AzureActiveDirectory|
|B2C|Resurs|Resurs|Hämta principer|
|B2C|Auktorisering|Auktorisering|Hämta principer|
|B2C|Resurs|Resurs|Hämta princip|
|B2C|Auktorisering|Auktorisering|Hämta princip|
|B2C|Kataloghantering|Katalog|Hämta resursegenskaper för en klient|
|B2C|Auktorisering|Auktorisering|Hämta resursegenskaper för en klient|
|B2C|Resurs|Resurs|Hämta IDP-lista som stöds|
|B2C|Auktorisering|Auktorisering|Hämta IDP-lista som stöds|
|B2C|Resurs|Resurs|Hämta IDP-lista som stöds för användarresa|
|B2C|Auktorisering|Auktorisering|Hämta IDP-lista som stöds för användarresa|
|B2C|Kataloghantering|Katalog|Hämta klientInfo|
|B2C|Auktorisering|Auktorisering|Hämta klientinfo|
|B2C|Kataloghantering|Katalog|Hämta klienttillåtna funktioner|
|B2C|Auktorisering|Auktorisering|Hämta klienttillåtna funktioner|
|B2C|Auktorisering|Auktorisering|Hämta klientdefinierad anpassad IDP-lista|
|B2C|Resurs|Resurs|Hämta klientdefinierad anpassad IDP-lista|
|B2C|Resurs|Resurs|Hämta klientdefinierad IDP-lista|
|B2C|Auktorisering|Auktorisering|Hämta klientdefinierad IDP-lista|
|B2C|Resurs|Resurs|Hämta klientdefinierad lokal IDP-lista|
|B2C|Auktorisering|Auktorisering|Hämta klientdefinierad lokal IDP-lista|
|B2C|Resurs|Resurs|Hämta klientinformation för en användare för att skapa en resurs|
|B2C|Auktorisering|Auktorisering|Hämta klientinformation för en användare för att skapa en resurs|
|B2C|Auktorisering|Auktorisering|Hämta klientlista|
|B2C|Auktorisering|Auktorisering|Hämta tenantDomains|
|B2C|Kataloghantering|Katalog|Hämta tenantDomains|
|B2C|Resurs|Resurs|Hämta standardkultur som stöds för CPIM|
|B2C|Auktorisering|Auktorisering|Hämta standardkultur som stöds för CPIM|
|B2C|Resurs|Resurs|Hämta information om ett administratörsflöde|
|B2C|Auktorisering|Auktorisering|Hämta information om ett administratörsflöde|
|B2C|Auktorisering|Auktorisering|Hämta listan över UserJourneys för den här klienten|
|B2C|Resurs|Resurs|Hämta listan över UserJourneys för den här klienten|
|B2C|Auktorisering|Auktorisering|Hämta uppsättningen med tillgängliga kulturer som stöds för CPIM|
|B2C|Resurs|Resurs|Hämta uppsättningen med tillgängliga kulturer som stöds för CPIM|
|B2C|Auktorisering|Auktorisering|Hämta trustFramework-princip|
|B2C|Resurs|Resurs|Hämta trustFramework-princip|
|B2C|Auktorisering|Auktorisering|Hämta trustFramework-princip som xml|
|B2C|Resurs|Resurs|Hämta trustFramework-princip som xml|
|B2C|Resurs|Resurs|Hämta användarattribut|
|B2C|Auktorisering|Auktorisering|Hämta användarattribut|
|B2C|Auktorisering|Auktorisering|Hämta användarattribut|
|B2C|Resurs|Resurs|Hämta användarattribut|
|B2C|Auktorisering|Auktorisering|Hämta lista för användarresa|
|B2C|Resurs|Resurs|Hämta lista för användarresa|
|B2C|Auktorisering|Auktorisering|GetIEFPolicies|
|B2C|Resurs|Resurs|GetIEFPolicies|
|B2C|Auktorisering|Auktorisering|GetIdentityProviders|
|B2C|Resurs|Resurs|GetIdentityProviders|
|B2C|Resurs|Resurs|GetTrustFrameworkPolicy|
|B2C|Auktorisering|Auktorisering|GetTrustFrameworkPolicy|
|B2C|Nyckel|Nyckel|Hämtar en CPIM-nyckelbehållare i jwk-format|
|B2C|Auktorisering|Auktorisering|Hämtar en CPIM-nyckelbehållare i jwk-format|
|B2C|Nyckel|Nyckel|Hämtar lista över nyckelbehållare i klienten|
|B2C|Auktorisering|Auktorisering|Hämtar lista över nyckelbehållare i klienten|
|B2C|Auktorisering|Auktorisering|Hämtar typ av klient|
|B2C|Kataloghantering|Katalog|Hämtar typ av klient|
|B2C|Autentisering|Autentisering|Utfärda ett åtkomsttoken till programmet|
|B2C|Autentisering|Autentisering|Utfärda en auktoriseringskod till programmet|
|B2C|Annat|Annat|Utfärda en auktoriseringskod till programmet|
|B2C|Autentisering|Autentisering|Utfärda ett id_token för programmet|
|B2C|Annat|Annat|Utfärda ett id_token för programmet|
|B2C|Auktorisering|Auktorisering|MigrateTenantMetadata|
|B2C|Resurs|Resurs|MigrateTenantMetadata|
|B2C|Resurs|Resurs|Flytta resurser|
|B2C|Auktorisering|Auktorisering|Korrigera IdentityProvider|
|B2C|Resurs|Resurs|Korrigera IdentityProvider|
|B2C|Resurs|Resurs|PutTrustFrameworkPolicy|
|B2C|Auktorisering|Auktorisering|PutTrustFrameworkPolicy|
|B2C|Auktorisering|Auktorisering|PutTrustFrameworkpolicy|
|B2C|Resurs|Resurs|PutTrustFrameworkpolicy|
|B2C|Resurs|Resurs|Ta bort en användarresa|
|B2C|Auktorisering|Auktorisering|Ta bort en användarresa|
|B2C|Auktorisering|Auktorisering|Återställa en säkerhetskopia av CPIM-nyckelbehållare|
|B2C|Nyckel|Nyckel|Återställa en säkerhetskopia av CPIM-nyckelbehållare|
|B2C|Programhantering|Program|Hämta beviljanden av V2-programbehörighet|
|B2C|Auktorisering|Auktorisering|Hämta beviljanden av V2-programbehörighet|
|B2C|Programhantering|Program|Hämta tjänstens huvudnamn för V2-program i aktuell klient|
|B2C|Auktorisering|Auktorisering|Hämta tjänstens huvudnamn för V2-program i aktuell klient|
|B2C|Nyckel|Nyckel|Spara nyckelbehållare|
|B2C|Auktorisering|Auktorisering|Uppdatera anpassad IDP|
|B2C|Resurs|Resurs|Uppdatera anpassad IDP|
|B2C|Resurs|Resurs|Uppdatera IDP|
|B2C|Auktorisering|Auktorisering|Uppdatera IDP|
|B2C|Resurs|Resurs|Uppdatera den lokala IdP:n|
|B2C|Auktorisering|Auktorisering|Uppdatera den lokala IdP:n|
|B2C|Programhantering|Program|Uppdatera V1-program|
|B2C|Auktorisering|Auktorisering|Uppdatera V1-program|
|B2C|Programhantering|Program|Uppdatera V2-program|
|B2C|Auktorisering|Auktorisering|Uppdatera V2-program|
|B2C|Programhantering|Program|Uppdatera behörighetsbeviljande för V2-program|
|B2C|Auktorisering|Auktorisering|Uppdatera behörighetsbeviljande för V2-program|
|B2C|Resurs|Resurs|Uppdatera en B2C-katalogresurs|
|B2C|Resurs|Resurs|Uppdatera princip|
|B2C|Auktorisering|Auktorisering|Uppdatera princip|
|B2C|Resurs|Resurs|Uppdatera prenumerationsstatus|
|B2C|Resurs|Resurs|Uppdatera användarattribut|
|B2C|Auktorisering|Auktorisering|Uppdatera användarattribut|
|B2C|Nyckel|Nyckel|Ladda upp en CPIM-krypterad nyckel|
|B2C|Auktorisering|Auktorisering|Ladda upp en CPIM-krypterad nyckel|
|B2C|Auktorisering|Auktorisering|Användarautentisering: API är inaktiverat för klient-featureset|
|B2C|Auktorisering|Auktorisering|Användarautentisering: Användaren beviljas åtkomst som ”innehavaradministratör”|
|B2C|Auktorisering|Auktorisering|Användarautentisering: Användare beviljas behörighet för åtkomst som ”behöriga användare”|
|B2C|Autentisering|Autentisering|Verifiera autentiseringsuppgifterna för lokalt konto|
|B2C|Resurs|Resurs|Verifiera flytta resurser|
|B2C|Autentisering|Autentisering|Verifiera användarautentisering|
|B2C|Kataloghantering|Katalog|Kontrollera om B2C-funktionen är aktiverad|
|B2C|Auktorisering|Auktorisering|Kontrollera om B2C-funktionen är aktiverad|
|B2C|Auktorisering|Auktorisering|Kontrollera om funktionen är aktiverad|
|B2C|Kataloghantering|Katalog|Kontrollera om funktionen är aktiverad|
|Kärnkatalog|Programhantering|Program|Lägg till OAuth2PermissionGrant|
|Kärnkatalog|Hantering av administrativ enhet|AdministrativeUnit|Lägg till administrativ enhet|
|Kärnkatalog|Användarhantering|Användare|Lägg till approlltilldelning som användare beviljats|
|Kärnkatalog|Grupphantering|Grupp|Lägg till approlltilldelning till grupp|
|Kärnkatalog|Programhantering|Program|Lägg till approlltilldelning till tjänstens huvudnamn|
|Kärnkatalog|Programhantering|Program|Lägga till ett program|
|Kärnkatalog|Resurs|Resurs|Lägg till enhet|
|Kärnkatalog|Resurs|Resurs|Lägg till enhetskonfiguration|
|Kärnkatalog|Rollhantering|Roll|Lägg till berättigad medlem i rollen|
|Kärnkatalog|Grupphantering|Grupp|Lägg till grupp|
|Kärnkatalog|Hantering av administrativ enhet|AdministrativeUnit|Lägg till medlem till administrativ enhet|
|Kärnkatalog|Grupphantering|Grupp|Lägg till medlem i grupp|
|Kärnkatalog|Rollhantering|Roll|Lägg till medlem till roll|
|Kärnkatalog|Programhantering|Program|Lägg till ägare till program|
|Kärnkatalog|Grupphantering|Grupp|Lägg till ägare till grupp|
|Kärnkatalog|Principhantering|Princip|Lägg till ägare till princip|
|Kärnkatalog|Programhantering|Program|Lägg till ägare till tjänstens huvudnamn|
|Kärnkatalog|Kataloghantering|Katalog|Lägg till partner till företag|
|Kärnkatalog|Principhantering|Princip|Lägg till princip|
|Kärnkatalog|Programhantering|Program|Lägg till princip till tjänstens huvudnamn|
|Kärnkatalog|Resurs|Resurs|Lägg till registrerad ägare till enhet|
|Kärnkatalog|Resurs|Resurs|Lägg till registrerade användare till enhet|
|Kärnkatalog|Rollhantering|Roll|Lägg till rolltilldelning till rolldefinition|
|Kärnkatalog|Rollhantering|Roll|Lägg till roll från mall|
|Kärnkatalog|Rollhantering|Roll|Lägg till begränsad medlem till roll|
|Kärnkatalog|Programhantering|Program|Lägg till tjänstens huvudnamn|
|Kärnkatalog|Programhantering|Program|Lägg till autentiseringsuppgifter för tjänstens huvudnamn|
|Kärnkatalog|Kataloghantering|Katalog|Lägg till overifierad domän|
|Kärnkatalog|Användarhantering|Användare|Lägga till användare|
|Kärnkatalog|Användarhantering|Användare|Lägg till information för användare med telefonapp med stark autentisering|
|Kärnkatalog|Kataloghantering|Katalog|Lägg till verifierad domän|
|Kärnkatalog|Användarhantering|Användare|Ändra användarlicens|
|Kärnkatalog|Användarhantering|Användare|Ändra användarlösenord|
|Kärnkatalog|Programhantering|Program|Samtyck till program|
|Kärnkatalog|Användarhantering|Användare|Konvertera federerad användare till hanterad|
|Kärnkatalog|Användarhantering|Användare|Skapa programlösenord för användare|
|Kärnkatalog|Kataloghantering|Katalog|Skapa företag|
|Kärnkatalog|Kataloghantering|Katalog|Skapa företagsinställningar|
|Kärnkatalog|Grupphantering|Grupp|Skapa gruppinställningar|
|Kärnkatalog|Hantering av administrativ enhet|AdministrativeUnit|Ta bort administrativ enhet|
|Kärnkatalog|Programhantering|Program|Ta bort program|
|Kärnkatalog|Användarhantering|Användare|Ta bort programlösenord för användare|
|Kärnkatalog|Kataloghantering|Katalog|Ta bort företagsinställningar|
|Kärnkatalog|Resurs|Resurs|Ta bort enhet|
|Kärnkatalog|Resurs|Resurs|Ta bort enhetskonfiguration|
|Kärnkatalog|Grupphantering|Grupp|Ta bort grupp|
|Kärnkatalog|Grupphantering|Grupp|Ta bort gruppinställningar|
|Kärnkatalog|Principhantering|Princip|Ta bort princip|
|Kärnkatalog|Användarhantering|Användare|Ta bort användare|
|Kärnkatalog|Kataloghantering|Katalog|Flytta ned partner|
|Kärnkatalog|Resurs|Resurs|Enheten är inte längre kompatibel|
|Kärnkatalog|Resurs|Resurs|Enheten hanteras inte längre|
|Kärnkatalog|Kataloghantering|Katalog|Katalog borttagen|
|Kärnkatalog|Kataloghantering|Katalog|Katalog borttagen permanent|
|Kärnkatalog|Kataloghantering|Katalog|Katalog schemalagd för borttagning|
|Kärnkatalog|Användarhantering|Användare|Avaktivera konto|
|Kärnkatalog|Användarhantering|Användare|Aktivera stark autentisering|
|Kärnkatalog|Grupphantering|Grupp|Slutför tillämpning av gruppbaserad licens för användare|
|Kärnkatalog|Programhantering|Program|Ta bort program permanent|
|Kärnkatalog|Grupphantering|Grupp|Ta bort grupp permanent|
|Kärnkatalog|Användarhantering|Användare|Ta bort användare permanent|
|Kärnkatalog|Kataloghantering|Katalog|Flytta upp företag till partner|
|Kärnkatalog|Kataloghantering|Katalog|Rensa egenskaper för Rights Management|
|Kärnkatalog|Programhantering|Program|Ta bort OAuth2PermissionGrant|
|Kärnkatalog|Grupphantering|Grupp|Ta bort approlltilldelning från grupp|
|Kärnkatalog|Programhantering|Program|Ta bort approlltilldelning från tjänstens huvudnamn|
|Kärnkatalog|Användarhantering|Användare|Ta bort approlltilldelning från användare|
|Kärnkatalog|Rollhantering|Roll|Ta bort behörig medlem från roll|
|Kärnkatalog|Hantering av administrativ enhet|AdministrativeUnit|Ta bort medlem från administrativ enhet|
|Kärnkatalog|Grupphantering|Grupp|Ta bort medlem från grupp|
|Kärnkatalog|Rollhantering|Roll|Ta bort medlem från roll|
|Kärnkatalog|Programhantering|Program|Ta bort ägare från program|
|Kärnkatalog|Grupphantering|Grupp|Ta bort ägare från grupp|
|Kärnkatalog|Programhantering|Program|Ta bort ägare från tjänstens huvudnamn|
|Kärnkatalog|Kataloghantering|Katalog|Ta bort partner från företag|
|Kärnkatalog|Principhantering|Princip|Ta bort autentiseringsuppgifter för princip|
|Kärnkatalog|Programhantering|Program|Ta bort princip från tjänstens huvudnamn|
|Kärnkatalog|Resurs|Resurs|Ta bort registrerad ägare från enhet|
|Kärnkatalog|Resurs|Resurs|Ta bort registrerade användare från enhet|
|Kärnkatalog|Rollhantering|Roll|Ta bort rolltilldelning från rolldefinition|
|Kärnkatalog|Rollhantering|Roll|Ta bort begränsad medlem från roll|
|Kärnkatalog|Programhantering|Program|Ta bort tjänstens huvudnamn|
|Kärnkatalog|Programhantering|Program|Ta bort autentiseringsuppgifter för tjänstens huvudnamn|
|Kärnkatalog|Kataloghantering|Katalog|Ta bort overifierad domän|
|Kärnkatalog|Användarhantering|Användare|Ta bort information för användare med telefonapp med stark autentisering|
|Kärnkatalog|Kataloghantering|Katalog|Ta bort verifierad domän|
|Kärnkatalog|Användarhantering|Användare|Återställ användarlösenord|
|Kärnkatalog|Grupphantering|Grupp|Återställ grupp|
|Kärnkatalog|Programhantering|Program|Återställ program|
|Kärnkatalog|Användarhantering|Användare|Återställ användare|
|Kärnkatalog|Programhantering|Program|Återkalla medgivande|
|Kärnkatalog|Kataloghantering|Katalog|Ange företagsinformation|
|Kärnkatalog|Kataloghantering|Katalog|Ange Dirsync-funktion|
|Kärnkatalog|Kataloghantering|Katalog|Ange flaggan Dirsyncenabled|
|Kärnkatalog|Kataloghantering|Katalog|Ange partnerskap|
|Kärnkatalog|Kataloghantering|Katalog|Ange tröskelvärde för oavsiktlig borttagning|
|Kärnkatalog|Kataloghantering|Katalog|Ange företagstillåten dataplats|
|Kärnkatalog|Kataloghantering|Katalog|Ange företagsfunktionen Multinationell som aktiverad|
|Kärnkatalog|Kataloghantering|Katalog|Ange katalogfunktion för klientorganisation|
|Kärnkatalog|Kataloghantering|Katalog|Ange domänautentisering|
|Kärnkatalog|Kataloghantering|Katalog|Ange federationsinställningar för domän|
|Kärnkatalog|Användarhantering|Användare|Genomför ändring av användarlösenord|
|Kärnkatalog|Grupphantering|Grupp|Ange grupplicens|
|Kärnkatalog|Grupphantering|Grupp|Ange grupp som ska hanteras av användare|
|Kärnkatalog|Kataloghantering|Katalog|Ange lösenordsprincip|
|Kärnkatalog|Kataloghantering|Katalog|Ange egenskaper för Rights Management|
|Kärnkatalog|Användarhantering|Användare|Ange användarhanterare|
|Kärnkatalog|Användarhantering|Användare|Aktivera användares OATH-tokenmetadata|
|Kärnkatalog|Grupphantering|Grupp|Börja tillämpa gruppbaserad licens för användare|
|Kärnkatalog|Grupphantering|Grupp|Utlös omberäkning för grupplicens|
|Kärnkatalog|Användarhantering|Användare|Uppdatera StsRefreshTokenValidFrom-tidsstämpel|
|Kärnkatalog|Hantering av administrativ enhet|AdministrativeUnit|Uppdatera administrativa enheter|
|Kärnkatalog|Programhantering|Program|Uppdatera programmet|
|Kärnkatalog|Kataloghantering|Katalog|Uppdatera företag|
|Kärnkatalog|Kataloghantering|Katalog|Uppdatera företagsinställningar|
|Kärnkatalog|Resurs|Resurs|Uppdatera enhet|
|Kärnkatalog|Resurs|Resurs|Uppdatera enhetskonfiguration|
|Kärnkatalog|Kataloghantering|Katalog|Uppdatera domän|
|Kärnkatalog|Användarhantering|Användare|Uppdatera externa hemligheter|
|Kärnkatalog|Programhantering|Program|Uppdatera externa hemligheter|
|Kärnkatalog|Grupphantering|Grupp|Uppdatera grupp|
|Kärnkatalog|Grupphantering|Grupp|Uppdatera gruppinställningar|
|Kärnkatalog|Principhantering|Princip|Uppdatera princip|
|Kärnkatalog|Rollhantering|Roll|Uppdatera roll|
|Kärnkatalog|Programhantering|Program|Uppdatera tjänstens huvudnamn|
|Kärnkatalog|Användarhantering|Användare|Uppdatera användare|
|Kärnkatalog|Kataloghantering|Katalog|Verifiera domän|
|Kärnkatalog|Kataloghantering|Katalog|Verifiera e-postverifierad domän|
|Identity Protection|Användarhantering|Användare|Administratören skapar ett tillfälligt lösenord|
|Identity Protection|Användarhantering|Användare|Administratörer kräver att användaren återställer sitt lösenord|
|Identity Protection|Annat|Annat|Ladda ned en enskild riskhändelsetyp|
|Identity Protection|Annat|Annat|Ladda ned administratörer och status för sammandrag av veckan|
|Identity Protection|Annat|Annat|Ladda ned alla riskhändelsetyper|
|Identity Protection|Annat|Annat|Ladda ned kostnadsfria användarriskhändelser|
|Identity Protection|Annat|Annat|Ladda ned användare som har flaggats för risk|
|Identity Protection|Kataloghantering|Katalog|Publicering|
|Identity Protection|Principhantering|Princip|Ställ in MFA-registreingsprincip|
|Identity Protection|Principhantering|Princip|Ange princip för inloggningsrisk|
|Identity Protection|Principhantering|Princip|Ange princip för användarrisk|
|Identity Protection|Kataloghantering|Katalog|Uppdatera aviseringsinställningar|
|Identity Protection|Kataloghantering|Katalog|Uppdatera inställningar för sammandrag av veckan|
|Inbjudna användare|Användarhantering|Användare|Tilldela extern användare till program|
|Inbjudna användare|Annat|Annat|Bearbetade batchinbjudningar|
|Inbjudna användare|Annat|Annat|Överförda batchinbjudningar|
|Inbjudna användare|Användarhantering|Användare|E-postmeddelandet skickades inte, användaren har avbrutit prenumerationen|
|Inbjudna användare|Användarhantering|Användare|Bjud in extern användare|
|Inbjudna användare|Användarhantering|Användare|Lös in inbjudan för extern användare|
|Inbjudna användare|Användarhantering|Användare|Generering av viral klientorganisation|
|Inbjudna användare|Användarhantering|Användare|Generering av viral användare|
|Microsoft Identity Manager (MIM)|Grupphantering|Grupp|Lägg till medlem|
|Microsoft Identity Manager (MIM)|Grupphantering|Grupp|Skapa grupp|
|Microsoft Identity Manager (MIM)|Grupphantering|Grupp|Ta bort grupp|
|Microsoft Identity Manager (MIM)|Grupphantering|Grupp|Ta bort medlem|
|Microsoft Identity Manager (MIM)|Grupphantering|Grupp|Uppdatera grupp|
|Microsoft Identity Manager (MIM)|Användarhantering|Användare|Användarlösenordsregistrering|
|Microsoft Identity Manager (MIM)|Användarhantering|Användare|Återställning av användarlösenord|
|Privileged Identity Management|Rollhantering|Roll|AccessReview_Review|
|Privileged Identity Management|Rollhantering|Roll|AccessReview_Update|
|Privileged Identity Management|Rollhantering|Roll|ActivationAborted|
|Privileged Identity Management|Rollhantering|Roll|ActivationAborted|
|Privileged Identity Management|Rollhantering|Roll|ActivationAborted|
|Privileged Identity Management|Rollhantering|Roll|ActivationRequested|
|Privileged Identity Management|Rollhantering|Roll|Tillagt|
|Privileged Identity Management|Rollhantering|Roll|Tilldela|
|Privileged Identity Management|Rollhantering|Roll|Upphöj|
|Privileged Identity Management|Rollhantering|Roll|Borttaget|
|Privileged Identity Management|Rollhantering|Roll|Ändringar av rollinställningar|
|Privileged Identity Management|Rollhantering|Roll|ScanAlertsNow|
|Privileged Identity Management|Rollhantering|Roll|Registrera|
|Privileged Identity Management|Rollhantering|Roll|Ta bort behörighet|
|Privileged Identity Management|Rollhantering|Roll|UpdateAlertSettings|
|Privileged Identity Management|Rollhantering|Roll|UpdateCurrentState|
|Självbetjäning, grupphantering|Grupphantering|Grupp|Godkänn en väntande begäran om att gå med i en grupp|
|Självbetjäning, grupphantering|Grupphantering|Grupp|Avbryt en väntande begäran om att gå med i en grupp|
|Självbetjäning, grupphantering|Grupphantering|Grupp|Skapa princip för livscykelhantering|
|Självbetjäning, grupphantering|Grupphantering|Grupp|Ta bort en väntande begäran om att gå med i en grupp|
|Självbetjäning, grupphantering|Grupphantering|Grupp|Avvisa en väntande begäran om att gå med i en grupp|
|Självbetjäning, grupphantering|Grupphantering|Grupp|Förnya grupp|
|Självbetjäning, grupphantering|Grupphantering|Grupp|Begäran om att gå med i en grupp|
|Självbetjäning, grupphantering|Grupphantering|Grupp|Ange dynamiska gruppegenskaper|
|Självbetjäning, grupphantering|Grupphantering|Grupp|Uppdatera princip för livscykelhantering|
|Hantering av lösenord för självbetjäning|Användarhantering|Användare|Blockerad från lösenordsåterställning via självbetjäning|
|Hantering av lösenord för självbetjäning|Användarhantering|Användare|Ändra lösenord (självbetjäning)|
|Hantering av lösenord för självbetjäning|Kataloghantering|Katalog|Inaktivera lösenordstillbakaskrivning för katalog|
|Hantering av lösenord för självbetjäning|Kataloghantering|Katalog|Aktivera lösenordstillbakaskrivning för katalog|
|Hantering av lösenord för självbetjäning|Användarhantering|Användare|Återställ lösenord (av administratör)|
|Hantering av lösenord för självbetjäning|Användarhantering|Användare|Återställ lösenord (självbetjäning)|
|Hantering av lösenord för självbetjäning|Användarhantering|Användare|Aktivitetsförlopp för lösenordsåterställningsflöde via självbetjäning|
|Hantering av lösenord för självbetjäning|Användarhantering|Användare|Aktivitetsförlopp för lösenordsåterställningsflöde via självbetjäning|
|Hantering av lösenord för självbetjäning|Användarhantering|Användare|Lås upp användarkonto (självbetjäning)|
|Hantering av lösenord för självbetjäning|Användarhantering|Användare|Användare registrerad för lösenordsåterställning via självbetjäning|
|Användningsvillkor|Principhantering|Princip|Godkänn användningsvillkoren|
|Användningsvillkor|Principhantering|Princip|Skapa användningsvillkor|
|Användningsvillkor|Principhantering|Princip|Ta bort användningsvillkor|
|Användningsvillkor|Principhantering|Princip|Ta bort användningsvillkor|
|Användningsvillkor|Principhantering|Princip|Redigera användningsvillkoren|
|Användningsvillkor|Principhantering|Princip|Publicera användningsvillkoren|
|Användningsvillkor|Principhantering|Princip|Avpublicera användningsvillkoren|




## <a name="next-steps"></a>Nästa steg

En översikt över rapportering finns i [Azure Active Directory-rapportering](active-directory-reporting-azure-portal.md).

