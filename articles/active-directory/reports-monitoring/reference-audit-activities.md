---
title: Referens för granskningsaktiviteter i Azure Active Directory (Azure AD) | Microsoft Docs
description: Få en översikt över vilka granskningsaktiviteter som kan loggas in dina granskningsloggar i Azure Active Directory (AD Azure).
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56a94ef3f6ea5737b629efe44bd3e1cc1f36a693
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127129"
---
# <a name="azure-ad-audit-activity-reference"></a>Referens över granskningsaktiviteter i Azure AD

Med Azure Active Directory (Azure AD)-rapporter kan du få den information du behöver för att avgöra hur din miljö fungerar.

Rapporteringsarkitekturen i Azure AD består av följande komponenter:

- **Aktivitetsrapporter** 
    - [Inloggningar](concept-sign-ins.md) – ger information om användningen av hanterade program och användar inloggnings aktiviteter
    - [Granskningsloggar](concept-audit-logs.md) – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. 
    
- **Säkerhetsrapporter** 
    - [Riskfyllda inloggningar](concept-risky-sign-ins.md) – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. 
    - [Användare som har flaggats för risk](concept-user-at-risk.md) – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. 

I den här artikeln står vilka granskningsaktiviteter som kan loggas in dina granskningsloggar.

## <a name="access-reviews"></a>Åtkomstgranskningar

|Granskningskategori|Aktivitet|
|---|---|
|Åtkomstgranskningar|Åtkomstgranskning avslutades|
|Åtkomstgranskningar|Lägg till godkännare för begäran|
|Åtkomstgranskningar|Lägg till granskare i åtkomstgranskning|
|Åtkomstgranskningar|Tillämpa åtkomstgranskning|
|Åtkomstgranskningar|Skapa åtkomstgranskning|
|Åtkomstgranskningar|Skapa program|
|Åtkomstgranskningar|Skapa godkännande av begäran|
|Åtkomstgranskningar|Ta bort åtkomstgranskning|
|Åtkomstgranskningar|Ta bort program|
|Åtkomstgranskningar|Länka programkontroll|
|Åtkomstgranskningar|Publicera till Azure AD-åtkomstgranskningar|
|Åtkomstgranskningar|Ta bort granskare från åtkomstgranskning|
|Åtkomstgranskningar|Begär granskningsstopp|
|Åtkomstgranskningar|Begära tillämpa granskningsresultat|
|Åtkomstgranskningar|Granska medlemskap i Rbac-roller|
|Åtkomstgranskningar|Granska apptilldelning|
|Åtkomstgranskningar|Granska gruppmedlemskap|
|Åtkomstgranskningar|Granska begäran om godkännande|
|Åtkomstgranskningar|Ta bort länk till programkontroll|
|Åtkomstgranskningar|Uppdatera åtkomstgranskning|
|Åtkomstgranskningar|Uppdatera registrerings status för Azure AD åtkomst granskningar|
|Åtkomstgranskningar|Uppdatera inställningar för e-postavisering om åtkomstgranskning|
|Åtkomstgranskningar|Uppdatera inställningen för upprepnings antal för åtkomst granskning|
|Åtkomstgranskningar|Uppdatera upprepnings varaktighet för åtkomst granskning i dagar|
|Åtkomstgranskningar|Uppdatera inställning för upprepnings slut typ för åtkomst granskning|
|Åtkomstgranskningar|Uppdatera inställningen för upprepnings typ för åtkomst granskning|
|Åtkomstgranskningar|Uppdatera inställningarna för åtkomstgranskningpåminnelse|
|Åtkomstgranskningar|Uppdatera program|
|Åtkomstgranskningar|Uppdatera godkännande av begäran|
|Åtkomstgranskningar|Användaren är inaktive rad|

## <a name="account-provisioning"></a>Kontoetablering

|Granskningskategori|Aktivitet|
|---|---|
|Programhantering|Hämta beviljanden av V2-programbehörighet|
|Programhantering|Hämta tjänstens huvudnamn för V2-program i aktuell klient|
|Programhantering|Uppdatera V1-program|
|Programhantering|Uppdatera V2-program|
|Programhantering|Uppdatera behörighetsbeviljande för V2-program|
|Programhantering|Lägg till OAuth2PermissionGrant|
|Programhantering|Lägg till approlltilldelning till tjänstens huvudnamn|

## <a name="application-proxy"></a>Programproxy

|Granskningskategori|Aktivitet|
|---|---|
|Programhantering|Lägg till program|
|Programhantering|Lägg till ägare till program|
|Programhantering|Lägg till ägare till tjänstens huvudnamn|
|Programhantering|Lägg till princip till tjänstens huvudnamn|
|Kataloghantering|Lägg till tjänstens huvudnamn|
|Kataloghantering|Lägg till autentiseringsuppgifter för tjänstens huvudnamn|
|Kataloghantering|Samtyck till program|
|Kataloghantering|Ta bort program|
|Kataloghantering|Ta bort program permanent|
|Kataloghantering|Ta bort OAuth2PermissionGrant|
|Kataloghantering|Ta bort approlltilldelning från tjänstens huvudnamn|
|Kataloghantering|Ta bort ägare från program|
|Resource|Ta bort ägare från tjänstens huvudnamn|
|Resource|Ta bort princip från tjänstens huvudnamn|
|Resource|Ta bort tjänstens huvudnamn|


## <a name="automated-password-rollover"></a>Automatiserad lösenordsförnyelse

|Granskningskategori|Aktivitet|
|---|---|
|Programhantering|Ta bort autentiseringsuppgifter för tjänstens huvudnamn|


## <a name="b2c"></a>B2C

|Granskningskategori|Aktivitet|
|---|---|
|Programhantering|Återställ program|
|Programhantering|Återkalla medgivande|
|Programhantering|Uppdatera programmet|
|Programhantering|Uppdatera externa hemligheter|
|Programhantering|Uppdatera tjänstens huvudnamn|
|Programhantering|Utfärda ett åtkomsttoken till programmet|
|Programhantering|Utfärda en auktoriseringskod till programmet|
|Programhantering|Utfärda ett id_token för programmet|
|Programhantering|Verifiera autentiseringsuppgifterna för lokalt konto|
|Programhantering|Verifiera användarautentisering|
|Programhantering|Lägga till V2-programbehörigheter|
|Programhantering|Lägga till en nyckel som baseras på en ASCII-hemlighet till en CPIM-nyckelcontainer|
|Programhantering|Lägg till en nyckel till en CPIM-nyckelcontainer|
|Programhantering|AdminPolicyDatas-SetResources|
|Programhantering|AdminUserJourneys-GetResources|
|Programhantering|AdminUserJourneys-RemoveResources|
|Authentication|AdminUserJourneys-SetResources|
|Authentication|Skapa IdentityProvider|
|Authentication|Skapa V1-program|
|Authentication|Skapa V2-program|
|Authentication|Skapa en anpassad domän i klienten|
|Authorization|Skapa ny AdminUserJourney|
|Authorization|Skapa json för lokaliserad resurs|
|Authorization|Skapa ny anpassad IDP|
|Authorization|Skapa ny IDP|
|Authorization|Skapa eller uppdatera en B2C-katalogresurs|
|Authorization|Skapa princip|
|Authorization|Skapa trustFramework-princip|
|Authorization|Skapa trustFramework-princip med konfigurerbart prefix|
|Authorization|Skapa användarattribut|
|Authorization|CreateTrustFrameworkPolicy|
|Authorization|Skapar eller uppdaterar en ny AdminUserJourney|
|Authorization|Ta bort IDP|
|Authorization|Ta bort IdentityProvider|
|Authorization|Ta bort V1-program|
|Authorization|Ta bort V2-program|
|Authorization|Ta bort behörighetsbeviljande för V2-program|
|Authorization|Ta bort en B2C-katalogresurs|
|Authorization|Ta bort en CPIM-nyckelcontainer|
|Authorization|Ta bort trustFramework-princip|
|Authorization|Ta bort användarattribut|
|Authorization|Aktivera B2C-funktion|
|Authorization|Hämta B2C-katalogresurser i en prenumeration|
|Authorization|Hämta anpassad IDP|
|Authorization|Hämta IDP|
|Authorization|Hämta V1- och V2-program|
|Authorization|Hämta V1-program|
|Authorization|Hämta V1-program|
|Authorization|Hämta V2-program|
|Authorization|Hämta V2-program|
|Authorization|Hämta en B2C Directory-resurs|
|Authorization|Skapa en lista över anpassade domäner i klienten|
|Authorization|Hämta en användarresa|
|Authorization|Hämta tillåtna programanspråk för användarresa|
|Authorization|Hämta tillåtna självkontrollerade anspråk för användarresa|
|Authorization|Hämta tillåtna självkontrollerade anspråk för princip|
|Authorization|Hämta tillgänglig lista över utdataanspråk|
|Authorization|Hämta innehållsdefinitioner för användarresa|
|Authorization|Hämta idp:er för ett specifikt administratörsflöde|
|Authorization|Hämta nyckelcontainer för metadata för aktiv nyckel i JWK|
|Authorization|Hämta lista över alla administratörsflöden|
|Authorization|Hämta lista över taggar för alla administratörsflöden för alla användare|
|Authorization|Hämta lista över klienter för en användare|
|Authorization|Hämta självkontrollerade anspråk för lokala konton|
|Authorization|Hämta json för lokaliserad resurs|
|Authorization|Hämta åtgärder för resursprovidern Microsoft.AzureActiveDirectory|
|Authorization|Hämta principer|
|Authorization|Hämta princip|
|Authorization|Hämta resursegenskaper för en klient|
|Authorization|Hämta IDP-lista som stöds|
|Authorization|Hämta IDP-lista som stöds för användarresa|
|Authorization|Hämta klientinfo|
|Authorization|Hämta klienttillåtna funktioner|
|Authorization|Hämta klientdefinierad anpassad IDP-lista|
|Authorization|Hämta klientdefinierad IDP-lista|
|Authorization|Hämta klientdefinierad lokal IDP-lista|
|Authorization|Hämta klientinformation för en användare för att skapa en resurs|
|Authorization|Hämta klientlista|
|Authorization|Hämta tenantDomains|
|Authorization|Hämta standardkultur som stöds för CPIM|
|Authorization|Hämta information om ett administratörsflöde|
|Authorization|Hämta listan över UserJourneys för den här klienten|
|Authorization|Hämta uppsättningen med tillgängliga kulturer som stöds för CPIM|
|Authorization|Hämta trustFramework-princip|
|Authorization|Hämta trustFramework-princip som xml|
|Authorization|Hämta användarattribut|
|Authorization|Hämta användarattribut|
|Authorization|Hämta lista för användarresa|
|Authorization|GetIEFPolicies|
|Authorization|GetIdentityProviders|
|Authorization|GetTrustFrameworkPolicy|
|Authorization|Hämtar en CPIM-nyckelcontainer i jwk-format|
|Authorization|Hämtar lista över nyckelcontainrar i klienten|
|Authorization|Hämtar typ av klient|
|Authorization|MigrateTenantMetadata|
|Authorization|Korrigera IdentityProvider|
|Authorization|PutTrustFrameworkPolicy|
|Authorization|PutTrustFrameworkpolicy|
|Authorization|Ta bort en användarresa|
|Authorization|Återställa en säkerhetskopia av CPIM-nyckelcontainer|
|Authorization|Hämta beviljanden av V2-programbehörighet|
|Authorization|Hämta tjänstens huvudnamn för V2-program i aktuell klient|
|Authorization|Uppdatera anpassad IDP|
|Authorization|Uppdatera IDP|
|Authorization|Uppdatera den lokala IdP:n|
|Authorization|Uppdatera V1-program|
|Authorization|Uppdatera V2-program|
|Authorization|Uppdatera behörighetsbeviljande för V2-program|
|Authorization|Uppdatera principen|
|Authorization|Uppdatera användarattribut|
|Authorization|Ladda upp en CPIM-krypterad nyckel|
|Authorization|Användarauktorisering: API har inaktiverats för klient featureset|
|Authorization|Användarauktorisering: Användare beviljad åtkomst som "klient organisation admin|
|Authorization|Användarauktorisering: Användaren beviljades åtkomst rättigheter för autentiserade användare|
|Authorization|Kontrollera om B2C-funktionen är aktiverad|
|Authorization|Verifiera om funktionen är aktive rad|
|Authorization|Skapa program|
|Authorization|Ta bort program|
|Authorization|Länka programkontroll|
|Authorization|Publicera till Azure AD-åtkomstgranskningar|
|Authorization|Ta bort länk till programkontroll|
|Authorization|Uppdatera program|
|Authorization|Inaktivera enkel inloggning för skrivbord|
|Authorization|Inaktivera skrivbords-Sso för en specifik domän|
|Authorization|Inaktivera programproxy|
|Authorization|Inaktivera direktautentisering|
|Authorization|Aktivera enkel inloggning för skrivbord|
|Kataloghantering|Aktivera skrivbords-Sso för en specifik domän|
|Kataloghantering|Aktivera programproxy|
|Kataloghantering|Aktivera direktautentisering|
|Kataloghantering|Skapa en anpassad domän i klienten|
|Kataloghantering|Aktivera B2C-funktion|
|Kataloghantering|Skapa en lista över anpassade domäner i klienten|
|Kataloghantering|Hämta resursegenskaper för en klient|
|Kataloghantering|Hämta klientinfo|
|Kataloghantering|Hämta klienttillåtna funktioner|
|Kataloghantering|Hämta tenantDomains|
|Nyckel|Hämtar typ av klient|
|Nyckel|Kontrollera om B2C-funktionen är aktiverad|
|Nyckel|Verifiera om funktionen är aktive rad|
|Nyckel|Lägg till partner till företag|
|Nyckel|Lägg till overifierad domän|
|Nyckel|Lägg till verifierad domän|
|Nyckel|Skapa företag|
|Nyckel|Skapa företagsinställningar|
|Nyckel|Ta bort företagsinställningar|
|Nyckel|Flytta ned partner|
|Nyckel|Katalog borttagen|
|Annat|Katalog borttagen permanent|
|Annat|Katalog schemalagd för borttagning|
|Resource|Flytta upp företag till partner|
|Resource|Rensa egenskaper för Rights Management|
|Resource|Ta bort partner från företag|
|Resource|Ta bort overifierad domän|
|Resource|Ta bort verifierad domän|
|Resource|Ange företagsinformation|
|Resource|Ange Dirsync-funktion|
|Resource|Ange flaggan Dirsyncenabled|
|Resource|Ange partnerskap|
|Resource|Ange tröskelvärde för oavsiktlig borttagning|
|Resource|Ange företagstillåten dataplats|
|Resource|Ange företagsfunktionen Multinationell som aktiverad|
|Resource|Ange katalogfunktion för klientorganisation|
|Resource|Ange domänautentisering|
|Resource|Ange federationsinställningar för domän|
|Resource|Ange lösenordsprincip|
|Resource|Ange egenskaper för Rights Management|
|Resource|Uppdatera företag|
|Resource|Uppdatera företagsinställningar|
|Resource|Uppdatera domän|
|Resource|Verifiera domän|
|Resource|Verifiera e-postverifierad domän|
|Resource|Registrera|
|Resource|Uppdatera aviseringsinställningar|
|Resource|Uppdatera inställningar för sammandrag av veckan|
|Resource|Inaktivera lösenordstillbakaskrivning för katalog|
|Resource|Aktivera lösenordstillbakaskrivning för katalog|
|Resource|Lägg till approlltilldelning till grupp|
|Resource|Lägg till grupp|
|Resource|Lägg till en medlem i gruppen|
|Resource|Lägg till ägare till grupp|
|Resource|Skapa gruppinställningar|
|Resource|Ta bort grupp|
|Resource|Ta bort gruppinställningar|
|Resource|Slutför tillämpning av gruppbaserad licens för användare|
|Resource|Ta bort grupp permanent|
|Resource|Ta bort approlltilldelning från grupp|
|Resource|Ta bort medlemmen från gruppen|
|Resource|Ta bort ägare från grupp|
|Resource|Återställ grupp|
|Resource|Ange grupplicens|
|Resource|Ange grupp som ska hanteras av användare|
|Resource|Börja tillämpa gruppbaserad licens för användare|
|Resource|Utlös omberäkning för grupplicens|
|Resource|Uppdatera grupp|
|Resource|Uppdatera gruppinställningar|
|Resource|Lägg till medlem|
|Resource|Skapa grupp|
|Resource|ta bort grupp|
|Resource|Ta bort medlem|
|Resource|Uppdatera grupp|
|Resource|Godkänn en väntande begäran om att gå med i en grupp|
|Resource|Avbryt en väntande begäran om att gå med i en grupp|
|Resource|Skapa princip för livscykelhantering|
|Resource|Ta bort en väntande begäran om att gå med i en grupp|
|Resource|Avvisa en väntande begäran om att gå med i en grupp|
|Resource|Förnya grupp|
|Resource|Begäran om att gå med i en grupp|
|Resource|Ange dynamiska gruppegenskaper|
|Resource|Uppdatera princip för livscykelhantering|
|Resource|Lägga till en nyckel som baseras på en ASCII-hemlighet till en CPIM-nyckelcontainer|
|Resource|Lägg till en nyckel till en CPIM-nyckelcontainer|
|Resource|Ta bort en CPIM-nyckelcontainer|
|Resource|Ta bort nyckelcontainer|
|Resource|Hämta nyckelcontainer för metadata för aktiv nyckel i JWK|
|Resource|Hämta metadata för nyckelcontainer|
|Resource|Hämtar en CPIM-nyckelcontainer i jwk-format|
|Resource|Hämtar lista över nyckelcontainrar i klienten|
|Resource|Återställa en säkerhetskopia av CPIM-nyckelcontainer|
|Resource|Spara nyckelcontainer|
|Resource|Ladda upp en CPIM-krypterad nyckel|
|Resource|Utfärda en auktoriseringskod till programmet|
|Resource|Utfärda ett id_token för programmet|


## <a name="core-directory"></a>Grundläggande katalog

|Granskningskategori|Aktivitet|
|---|---|
|Hantering av administrativ enhet|Hämta en identifierings typ för enskild risk|
|Hantering av administrativ enhet|Ladda ned administratörer och status för sammandrag av veckan|
|Hantering av administrativ enhet|Hämta alla typer av risk identifiering|
|Hantering av administrativ enhet|Ladda ned kostnads fria identifieringar av användar risker|
|Hantering av administrativ enhet|Ladda ned användare som har flaggats för risk|
|Programhantering|Bearbetade batchinbjudningar|
|Programhantering|Överförda batchinbjudningar|
|Programhantering|Lägg till ägare till princip|
|Programhantering|Lägg till princip|
|Programhantering|Ta bort princip|
|Programhantering|Ta bort autentiseringsuppgifter för princip|
|Programhantering|Uppdatera principen|
|Programhantering|Ställ in MFA-registreingsprincip|
|Programhantering|Ange princip för inloggningsrisk|
|Programhantering|Ange princip för användarrisk|
|Programhantering|Godkänn användningsvillkoren|
|Programhantering|Skapa användningsvillkor|
|Programhantering|Ta bort användningsvillkor|
|Programhantering|Ta bort användningsvillkor|
|Programhantering|Redigera användningsvillkoren|
|Programhantering|Publicera användningsvillkoren|
|Programhantering|Avpublicera användningsvillkoren|
|Programhantering|Lägg till SSL-certifikat för program|
|Programhantering|Ta bort SSL-bindning|
|Programhantering|Registrera anslutningsapp|
|Programhantering|AdminPolicyDatas-RemoveResources|
|Programhantering|AdminPolicyDatas-SetResources|
|Programhantering|AdminUserJourneys-GetResources|
|Kataloghantering|AdminUserJourneys-RemoveResources|
|Kataloghantering|AdminUserJourneys-SetResources|
|Kataloghantering|Skapa IdentityProvider|
|Kataloghantering|Skapa ny AdminUserJourney|
|Kataloghantering|Skapa json för lokaliserad resurs|
|Kataloghantering|Skapa ny anpassad IDP|
|Kataloghantering|Skapa ny IDP|
|Kataloghantering|Skapa eller uppdatera en B2C-katalogresurs|
|Kataloghantering|Skapa princip|
|Kataloghantering|Skapa trustFramework-princip|
|Kataloghantering|Skapa trustFramework-princip med konfigurerbart prefix|
|Kataloghantering|Skapa användarattribut|
|Kataloghantering|CreateTrustFrameworkPolicy|
|Kataloghantering|Ta bort IDP|
|Kataloghantering|Ta bort IdentityProvider|
|Kataloghantering|Ta bort en B2C-katalogresurs|
|Kataloghantering|Ta bort trustFramework-princip|
|Kataloghantering|Ta bort användarattribut|
|Kataloghantering|Hämta B2C-katalogresurser i en resursgrupp|
|Kataloghantering|Hämta B2C-katalogresurser i en prenumeration|
|Kataloghantering|Hämta anpassad IDP|
|Kataloghantering|Hämta IDP|
|Kataloghantering|Hämta en B2C Directory-resurs|
|Kataloghantering|Hämta en användarresa|
|Kataloghantering|Hämta tillåtna programanspråk för användarresa|
|Kataloghantering|Hämta tillåtna självkontrollerade anspråk för användarresa|
|Kataloghantering|Hämta tillåtna självkontrollerade anspråk för princip|
|Kataloghantering|Hämta tillgänglig lista över utdataanspråk|
|Kataloghantering|Hämta innehållsdefinitioner för användarresa|
|Kataloghantering|Hämta idp:er för ett specifikt administratörsflöde|
|Kataloghantering|Hämta lista över alla administratörsflöden|
|Kataloghantering|Hämta lista över taggar för alla administratörsflöden för alla användare|
|Grupphantering|Hämta lista över klienter för en användare|
|Grupphantering|Hämta självkontrollerade anspråk för lokala konton|
|Grupphantering|Hämta json för lokaliserad resurs|
|Grupphantering|Hämta åtgärder för resursprovidern Microsoft.AzureActiveDirectory|
|Grupphantering|Hämta principer|
|Grupphantering|Hämta princip|
|Grupphantering|Hämta IDP-lista som stöds|
|Grupphantering|Hämta IDP-lista som stöds för användarresa|
|Grupphantering|Hämta klientdefinierad anpassad IDP-lista|
|Grupphantering|Hämta klientdefinierad IDP-lista|
|Grupphantering|Hämta klientdefinierad lokal IDP-lista|
|Grupphantering|Hämta klientinformation för en användare för att skapa en resurs|
|Grupphantering|Hämta standardkultur som stöds för CPIM|
|Grupphantering|Hämta information om ett administratörsflöde|
|Grupphantering|Hämta listan över UserJourneys för den här klienten|
|Grupphantering|Hämta uppsättningen med tillgängliga kulturer som stöds för CPIM|
|Grupphantering|Hämta trustFramework-princip|
|Grupphantering|Hämta trustFramework-princip som xml|
|Grupphantering|Hämta användarattribut|
|Principhantering|Hämta användarattribut|
|Principhantering|Hämta lista för användarresa|
|Principhantering|GetIEFPolicies|
|Principhantering|GetIdentityProviders|
|Principhantering|GetTrustFrameworkPolicy|
|Resource|MigrateTenantMetadata|
|Resource|Flytta resurser|
|Resource|Korrigera IdentityProvider|
|Resource|PutTrustFrameworkPolicy|
|Resource|PutTrustFrameworkpolicy|
|Resource|Ta bort en användarresa|
|Resource|Uppdatera anpassad IDP|
|Resource|Uppdatera IDP|
|Resource|Uppdatera den lokala IdP:n|
|Resource|Uppdatera en B2C-katalogresurs|
|Resource|Uppdatera principen|
|Resource|Uppdatera prenumerationsstatus|
|Rollhantering|Uppdatera användarattribut|
|Rollhantering|Verifiera flytta resurser|
|Rollhantering|Lägg till enhet|
|Rollhantering|Lägg till enhetskonfiguration|
|Rollhantering|Lägg till registrerad ägare till enhet|
|Rollhantering|Lägg till registrerade användare till enhet|
|Rollhantering|Ta bort enhet|
|Rollhantering|Ta bort enhetskonfiguration|
|Rollhantering|Enheten är inte längre kompatibel|
|Rollhantering|Enheten hanteras inte längre|
|Användarhantering|Ta bort registrerad ägare från enhet|
|Användarhantering|Ta bort registrerade användare från enhet|
|Användarhantering|Uppdatera enhet|
|Användarhantering|Uppdatera enhetskonfiguration|
|Användarhantering|Lägg till berättigad medlem i rollen|
|Användarhantering|Lägg till medlem till roll|
|Användarhantering|Lägg till rolltilldelning till rolldefinition|
|Användarhantering|Lägg till roll från mall|
|Användarhantering|Lägg till begränsad medlem till roll|
|Användarhantering|Ta bort behörig medlem från roll|
|Användarhantering|Ta bort medlemmen från rollen|
|Användarhantering|Ta bort rolltilldelning från rolldefinition|
|Användarhantering|Ta bort begränsad medlem från roll|
|Användarhantering|Uppdatera roll|
|Användarhantering|AccessReview_Review|
|Användarhantering|AccessReview_Update|
|Användarhantering|ActivationAborted|
|Användarhantering|ActivationAborted|
|Användarhantering|ActivationAborted|
|Användarhantering|ActivationRequested|
|Användarhantering|Har lagts till|
|Användarhantering|Tilldela|


## <a name="identity-protection"></a>Identitetsskydd

|Granskningskategori|Aktivitet|
|---|---|
|Kataloghantering|Upphöj|
|Kataloghantering|Borttaget|
|Kataloghantering|Ändringar av rollinställningar|
|Annat|ScanAlertsNow|
|Annat|Registrering|
|Annat|Ta bort behörighet|
|Annat|UpdateAlertSettings|
|Annat|UpdateCurrentState|
|Principhantering|Åtkomstgranskning avslutades|
|Principhantering|Lägg till godkännare för begäran|
|Principhantering|Lägg till granskare i åtkomstgranskning|
|Användarhantering|Tillämpa åtkomstgranskning|
|Användarhantering|Skapa åtkomstgranskning|


## <a name="invited-users"></a>Inbjudna användare

|Granskningskategori|Aktivitet|
|---|---|
|Annat|Skapa godkännande av begäran|
|Annat|Ta bort åtkomstgranskning|
|Användarhantering|Ta bort granskare från åtkomstgranskning|
|Användarhantering|Begära tillämpa granskningsresultat|
|Användarhantering|Begär granskningsstopp|
|Användarhantering|Granska apptilldelning|
|Användarhantering|Granska gruppmedlemskap|
|Användarhantering|Granska medlemskap i Rbac-roller|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Granskningskategori|Aktivitet|
|---|---|
|Grupphantering|Granska begäran om godkännande|
|Grupphantering|Uppdatera åtkomstgranskning|
|Grupphantering|Uppdatera inställningar för e-postavisering om åtkomstgranskning|
|Grupphantering|Uppdatera inställningen för upprepnings antal för åtkomst granskning|
|Grupphantering|Uppdatera upprepnings varaktighet för åtkomst granskning i dagar|
|Användarhantering|Uppdatera inställning för upprepnings slut typ för åtkomst granskning|
|Användarhantering|Uppdatera inställningen för upprepnings typ för åtkomst granskning|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Granskningskategori|Aktivitet|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationAborted|
|PIM|ActivationAborted|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Har lagts till|
|PIM|AddedOutsidePIM|
|PIM|Tilldela|
|PIM|DismissAlert|
|PIM|Upphöj|
|PIM|ReactivateAlert|
|PIM|Borttaget|
|PIM|RemovedOutsidePIM|
|PIM|Begär granskningsstopp|
|PIM|Ändringar av rollinställningar|
|PIM|ScanAlertsNow|
|PIM|Registrering|
|PIM|Ta bort|
|PIM|Ta bort behörighet|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Självbetjäning, grupphantering

|Granskningskategori|Aktivitet|
|---|---|
|Grupphantering|Återställ användarlösenord|
|Grupphantering|Återställ användare|
|Grupphantering|Genomför ändring av användarlösenord|
|Grupphantering|Ange användarhanterare|
|Grupphantering|Aktivera användares OATH-tokenmetadata|
|Grupphantering|Uppdatera StsRefreshTokenValidFrom-tidsstämpel|
|Grupphantering|Uppdatera externa hemligheter|
|Grupphantering|Uppdatera användare|
|Grupphantering|Administratören skapar ett tillfälligt lösenord|


## <a name="self-service-password-management"></a>Lösenordshantering via självbetjäning

|Granskningskategori|Aktivitet|
|---|---|
|Kataloghantering|Administratörer kräver att användaren återställer sitt lösenord|
|Kataloghantering|Tilldela extern användare till program|
|Användarhantering|E-postmeddelandet skickades inte, användaren har avbrutit prenumerationen|
|Användarhantering|Bjud in extern användare|
|Användarhantering|Lös in inbjudan för extern användare|
|Användarhantering|Generering av viral klientorganisation|
|Användarhantering|Generering av viral användare|
|Användarhantering|Användarlösenordsregistrering|
|Användarhantering|Återställning av användarlösenord|
|Användarhantering|Blockerad från lösenordsåterställning via självbetjäning|


## <a name="terms-of-use"></a>Användningsvillkor

|Granskningskategori|Aktivitet|
|---|---|
|Användningsvillkor|Godkänn användningsvillkoren|
|Användningsvillkor|Skapa användningsvillkor|
|Användningsvillkor|Ta bort användningsvillkor|
|Användningsvillkor|Ta bort medgivande|
|Användningsvillkor|Ta bort användningsvillkor|
|Användningsvillkor|Redigera användningsvillkoren|
|Användningsvillkor|Användnings villkor upphör att gälla|
|Användningsvillkor|Användnings villkor för hård borttagning|
|Användningsvillkor|Publicera användningsvillkoren|
|Användningsvillkor|Avpublicera användningsvillkoren|


## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure AD-rapporter](overview-reports.md).
- [Rapporten gransknings loggar](concept-audit-logs.md). 
- [Programmerings åtkomst till Azure AD-rapporter](concept-reporting-api.md)
