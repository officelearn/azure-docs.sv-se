---
title: Microsoft-identitetsplattformskonton & klientprofiler på Android | Azure
description: En översikt över Microsofts identitetsplattformskonton för Android
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: d0497ad68e7b29e6d8c83dd860ba8f509e229579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611877"
---
# <a name="accounts--tenant-profiles-android"></a>Konton och klientorganisationsprofiler (Android)

Den här artikeln innehåller `account` en översikt över vad en är i Microsofts identitetsplattform.

MSAL-API (Microsoft Authentication Library) ersätter termen *användare* med *termkontot*. En orsak är att en användare (människa eller programvaruagent) kan ha eller kan använda flera konton. Dessa konton kan finnas i användarens egen organisation och/eller i andra organisationer som användaren är medlem i.

Ett konto på Microsofts identitetsplattform består av:

- En unik identifierare.  
- En eller flera autentiseringsuppgifter som används för att visa ägarskap/kontroll av kontot.
- En eller flera profiler som består av attribut som:
  - Bild, Förnamn, Efternamn, Titel, Kontorsplats
- Ett konto har en källa till auktoritet eller postsystem. Det här är det system där kontot skapas och där autentiseringsuppgifterna som är associerade med kontot lagras. I system med flera innehavare som Microsofts identitetsplattform är postsystemet `tenant` där kontot skapades. Den här klienten `home tenant`kallas också .
- Konton i Microsofts identitetsplattform har följande system för post:
  - Azure Active Directory, inklusive Azure Active Directory B2C.
  - Microsoft-konto (Live).
- Konton från registersystem utanför Microsofts identitetsplattform finns representerade inom Microsofts identitetsplattform, inklusive:
  - identiteter från anslutna lokala kataloger (Windows Server Active Directory)
  - externa identiteter från LinkedIn, GitHub och så vidare.
  I dessa fall har ett konto både ett ursprungssystem för post och ett system för post inom Microsofts identitetsplattform.
- Microsoft-identitetsplattformen gör att ett konto kan användas för att komma åt resurser som tillhör flera organisationer (Azure Active Directory-klienter).
  - Om du vill registrera att ett konto från ett postsystem (AAD-klient A) har åtkomst till en resurs i ett annat postsystem (AAD Tenant B) måste kontot representeras i klienten där resursen har definierats. Detta görs genom att skapa en lokal post för kontot från system A i system B.
  - Den här lokala posten, som är representationen av kontot, är bunden till det ursprungliga kontot.
  - MSAL exponerar den här `Tenant Profile`lokala posten som en .
  - Klientprofilen kan ha olika attribut som är lämpliga för den lokala kontexten, till exempel Befattning, Kontorsplats, Kontaktinformation osv.
- Eftersom ett konto kan finnas i en eller flera klienter kan ett konto ha mer än en profil.

> [!NOTE]
> MSAL behandlar Microsoft-kontosystemet (Live, MSA) som en annan klient inom Microsofts identitetsplattform. Klient-ID för Microsoft-kontots klient är:`9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Översiktsdiagram för konto

![Diagram över kontoöversikt](./media/accounts-overview/accounts-overview.svg)

I diagrammet ovan:

- Kontot `bob@contoso.com` skapas i den lokala Active Directory för Windows Server (ursprung lokalt postsystem).
- Kontot `tom@live.com` skapas i Microsoft-kontots klientorganisation.
- `bob@contoso.com`har åtkomst till minst en resurs i följande Azure Active Directory-klienter:
  - contoso.com (molnsystem för post - kopplat till lokalt postsystem)
  - fabrikam.com
  - woodgrovebank.com
  - Det finns `bob@contoso.com` en klientprofil för var och en av dessa klienter.
- `tom@live.com`har åtkomst till resurser i följande Microsoft-klienter:
  - contoso.com
  - fabrikam.com
  - Det finns `tom@live.com` en klientprofil för var och en av dessa klienter.
- Information om Tom och Bob i andra klienter kan skilja sig från det i postsystemet. De kan skilja sig åt med attribut som befattning, Kontorsplats och så vidare. De kan vara medlemmar i grupper och/eller roller inom varje organisation (Azure Active Directory Tenant). Vi refererar till bob@contoso.com denna information som klientprofil.

I diagrammet bob@contoso.com och tom@live.com har åtkomst till resurser i olika Azure Active Directory-klienter. Mer information finns [i Lägga till Azure Active Directory B2B-samarbetsanvändare i Azure-portalen](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

## <a name="accounts-and-single-sign-on-sso"></a>Konton och enkel inloggning (SSO)

MSAL-tokencachen lagrar en *enda uppdateringstoken* per konto. Den uppdateringstoken kan användas för att tyst begära åtkomsttoken från flera Microsoft identity-plattformsklienter. När en mäklare installeras på en enhet hanteras kontot av mäklaren och enhetsomfattande enkel inloggning blir möjlig.

> [!IMPORTANT]
> B2C-konto (Business to Consumer) och uppdateringstokensbeteende skiljer sig från resten av Microsofts identitetsplattform. Mer information finns i [B2C-principer & konton](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Kontoidentifierare

MSAL-konto-ID är inte ett kontoobjekt-ID. Det är inte tänkt att tolkas och / eller åberopas för att förmedla något annat än unikhet inom Microsofts identitetsplattform.

För kompatibilitet med Azure AD Authentication Library (ADAL) och för att underlätta migreringen från ADAL till MSAL kan MSAL slå upp konton med valfri giltig identifierare för kontot som är tillgängligt i MSAL-cachen.  Följande hämtar till exempel alltid samma kontoobjekt för tom@live.com eftersom var och en av identifierarna är giltig:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Komma åt anspråk om ett konto

Förutom att begära en åtkomsttoken begär MSAL också alltid en ID-token från varje klient. Detta sker genom att alltid begära följande omfattningar:

- Openid
- profil

ID-token innehåller en lista över anspråk. `Claims`är namn/värdepar om kontot och används för att göra begäran.

Som tidigare nämnts kan varje klient där det finns ett konto lagra olika information om kontot, inklusive men inte begränsat till attribut som: befattning, kontorsplats och så vidare.

Ett konto kan vara medlem eller gäst i flera organisationer, men MSAL frågar inte en tjänst för att få en lista över de klienter som kontot är medlem i. I stället bygger MSAL upp en lista över klienter som kontot finns i, som ett resultat av tokenbegäranden som har gjorts.

De anspråk som exponeras för kontoobjektet är alltid anspråk från "hemklienten"/{authority} för ett konto. Om det kontot inte har använts för att begära en token för sin hemklient kan MSAL inte tillhandahålla anspråk via kontoobjektet.  Ett exempel:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Om du vill visa en lista över anspråk som är tillgängliga från kontoobjektet läser du [anspråk i en id_token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token)

> [!TIP]
> Om du vill inkludera ytterligare anspråk i din id_token läser du den valfria anspråksdokumentationen i [Så här: Tillhandahålla valfria anspråk till din Azure AD-app](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)

### <a name="access-tenant-profile-claims"></a>Få tillgång till anspråk på klientprofil

Om du vill komma åt anspråk om ett konto som de `IMultiTenantAccount`visas i andra klienter måste du först casta ditt kontoobjekt till . Alla konton kan vara flera innehavare, men antalet klientprofiler som är tillgängliga via MSAL baseras på vilka klienter du har begärt token från att använda det aktuella kontot.  Ett exempel:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C-principer & konton

Uppdatera token för ett konto delas inte över B2C-principer. Därför är enkel inloggning med token inte möjlig. Detta betyder inte att enda inloggning inte är möjlig. Det betyder att enkel inloggning måste använda en interaktiv upplevelse där en cookie är tillgänglig för att möjliggöra enkel inloggning.

Detta innebär också att när det gäller MSAL, om du förvärvar token med hjälp av olika B2C-principer, så behandlas dessa som separata konton - var och en med sin egen identifierare. Om du vill använda ett konto `acquireTokenSilent`för att begära en token med hjälp av måste du välja kontot i listan över konton som matchar den princip som du använder med tokenbegäran. Ett exempel:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```
