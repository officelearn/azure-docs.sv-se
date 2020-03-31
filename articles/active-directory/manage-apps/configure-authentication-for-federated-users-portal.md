---
title: Konfigurera automatisk acceleration för inloggning med Home Realm Discovery
description: Lär dig hur du konfigurerar Home Realm Discovery-principen för Azure Active Directory-autentisering för federerade användare, inklusive automatisk acceleration och domäntips.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60bfc964ffc394b3f79c9d279158003f383b7331
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943430"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurera Azure Active Directory-inloggningsbeteende för ett program med hjälp av en princip för identifiering av Home Realm

Den här artikeln innehåller en introduktion till konfiguration av Azure Active Directory-autentiseringsbeteende för federerade användare. Den omfattar konfiguration av begränsningar för automatisk acceleration och autentisering för användare i federerade domäner.

## <a name="home-realm-discovery"></a>Upptäckt av hemsfär
Home Realm Discovery (HRD) är den process som gör att Azure Active Directory (Azure AD) kan avgöra var en användare behöver autentisera vid inloggningstid.  När en användare loggar in på en Azure AD-klient för att komma åt en resurs, eller till den gemensamma inloggningssidan för Azure AD, skriver de ett användarnamn (UPN). Azure AD använder det för att ta reda på var användaren behöver logga in. 

Användaren kan behöva tas till någon av följande platser för att autentiseras:

- Användarens hemklient (kan vara samma klient som den resurs som användaren försöker komma åt). 

- Microsoft-konto.  Användaren är gäst i resursklienten.

-  En lokal identitetsprovider som Active Directory Federation Services (AD FS).

- En annan identitetsprovider som är federerad med Azure AD-klienten.

## <a name="auto-acceleration"></a>Automatisk acceleration 
Vissa organisationer konfigurerar domäner i sin Azure Active Directory-klient för att federera med ett annat IDP, till exempel AD FS för användarautentisering.  

När en användare loggar in på ett program visas de först med en Azure AD-inloggningssida. När de har skrivit sitt UPN, om de är i en federerad domän de sedan tas till inloggningssidan för IdP betjänar domänen. Under vissa omständigheter kanske administratörer vill dirigera användare till inloggningssidan när de loggar in på specifika program. 

Som ett resultat användare kan hoppa över den första Azure Active Directory-sidan. Den här processen kallas "automatisk acceleration för inloggning".

I de fall klienten är federerad till ett annat IdP för inloggning, gör automatisk acceleration användarens inloggning mer strömlinjeformad.  Du kan konfigurera automatisk acceleration för enskilda program.

>[!NOTE]
>Om du konfigurerar ett program för automatisk acceleration kan gästanvändare inte logga in. Om du tar en användare direkt till ett federerat IDP för autentisering finns det inget sätt för dem att komma tillbaka till inloggningssidan för Azure Active Directory. Gästanvändare, som kan behöva dirigeras till andra klienter eller ett externt IDP, till exempel ett Microsoft-konto, kan inte logga in på det programmet eftersom de hoppar över home realm discovery-steget.  

Det finns två sätt att styra automatisk acceleration till ett federerat IDP:   

- Använd en domäntips om autentiseringsbegäranden för ett program. 
- Konfigurera en home realm discovery-princip för att aktivera automatisk acceleration.

### <a name="domain-hints"></a>Tips för domän    
Domäntips är direktiv som ingår i autentiseringsbegäran från ett program. De kan användas för att accelerera användaren till deras federerade IdP-inloggningssida. Eller så kan de användas av ett program med flera innehavare för att accelerera användaren direkt till den märkesvaror Azure AD inloggningssidan för sin klientorganisation.  

Programmet "largeapp.com" kan till exempel göra det möjligt för deras kunder att komma åt programmet med en anpassad URL "contoso.largeapp.com". Appen kan också innehålla en domänledtråd för att contoso.com i autentiseringsbegäran. 

Syntaxen för domäntips varierar beroende på vilket protokoll som används och den är vanligtvis konfigurerad i programmet.

**WS-Federation**: whr=contoso.com i frågesträngen.

**SAML**: Antingen en SAML-autentiseringsbegäran som innehåller en domänledtråd eller en frågesträng whr=contoso.com.

**Öppna ID Connect:** En frågesträng domain_hint=contoso.com. 

Om en domäntips ingår i autentiseringsbegäran från programmet och klienten är federerad med den domänen, försöker Azure AD omdirigera inloggning till IdP som är konfigurerat för den domänen. 

Om domäntipset inte refererar till en verifierad federerad domän ignoreras den och normal Home Realm Discovery anropas.

Mer information om automatisk acceleration med hjälp av domäntips som stöds av Azure Active Directory finns i [bloggen Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Om en domäntips ingår i en autentiseringsbegäran åsidosätter dess närvaro automatisk acceleration som är inställd för programmet i HRD-principen.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Home Realm Discovery-policy för automatisk acceleration
Vissa program tillhandahåller inte ett sätt att konfigurera autentiseringsbegäran som de avger. I dessa fall är det inte möjligt att använda domäntips för att styra automatisk acceleration. Automatisk acceleration kan konfigureras via principen för att uppnå samma beteende.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Aktivera direkt autentisering för äldre program
Bästa praxis är att program använder AAD-bibliotek och interaktiv inloggning för att autentisera användare. Biblioteken tar hand om de federerade användarflödena.  Ibland skrivs inte äldre program för att förstå federationen. De utför inte identifiering av hemsfärer och interagerar inte med rätt federerad slutpunkt för att autentisera en användare. Om du väljer att använda HRD-principen för att aktivera specifika äldre program som skickar användarnamn/lösenordsuppgifter för att autentisera direkt med Azure Active Directory. Synkronisering av lösenord hash måste vara aktiverat. 

> [!IMPORTANT]
> Aktivera endast direkt autentisering om du har aktiverat Lösenord Hash Sync och du vet att det är okej att autentisera det här programmet utan några principer som implementerats av ditt lokala IdP. Om du inaktiverar Synkronisering av lösenord hash eller inaktiverar katalogsynkronisering med AD Connect av någon anledning, bör du ta bort den här principen för att förhindra möjligheten till direkt autentisering med hjälp av en inaktuell lösenordshh.

## <a name="set-hrd-policy"></a>Ange HRD-princip
Det finns tre steg för att ange HRD-princip för ett program för federerade automatisk acceleration eller direkta molnbaserade program:

1. Skapa en HRD-princip.

2. Leta reda på tjänstens huvudnamn som principen ska kopplas till.

3. Koppla principen till tjänstens huvudnamn. 

Principer börjar endast gälla för ett visst program när de är kopplade till ett huvudnamn för tjänsten. 

Endast en HRD-princip kan vara aktiv på ett tjänsthuvudnamn åt gången.  

Du kan använda Azure Active Directory PowerShell-cmdletar för att skapa och hantera HRD-principer.

Följande är ett exempel HRD principdefinition:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":true
    }
   }
```

Principtypen är "HomeRealmDiscoveryPolicy".

**AccelerateToFederatedDomain** är valfritt. Om **AccelerateToFederatedDomain** är falskt, har principen ingen effekt på automatisk acceleration. Om **AccelerateToFederatedDomain** är sant och det bara finns en verifierad och federerad domän i klienten, kommer användarna att tas direkt till den federerade IDP för inloggning. Om det är sant och det finns mer än en verifierad domän i klienten måste **PreferredDomain** anges.

**PreferredDomain** är valfritt. **PreferredDomain** bör ange en domän som ska accelereras. Det kan utelämnas om klienten bara har en federerad domän.  Om den utelämnas och det finns mer än en verifierad federerad domän har principen ingen effekt.

 Om **PreferredDomain** anges måste den matcha en verifierad, federerad domän för klienten. Alla användare av programmet måste kunna logga in på den domänen.

**AllowCloudPasswordValidation** är valfritt. Om **AllowCloudPasswordValidation** är sant tillåts programmet att autentisera en federerad användare genom att presentera användarnamn/lösenordsuppgifter direkt till Azure Active Directory token slutpunkt. Detta fungerar bara om Password Hash Sync är aktiverat.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioritering och utvärdering av hrd-policyer
HRD-principer kan skapas och sedan tilldelas till specifika organisationer och tjänsthuvudnamn. Det innebär att det är möjligt för flera principer att tillämpas på ett visst program. HRD-principen som träder i kraft följer dessa regler:


- Om det finns en domäntips i autentiseringsbegäran ignoreras alla HRD-principer för automatisk acceleration. Det beteende som anges av domäntipset används.

- Annars, om en princip uttryckligen tilldelas tjänstens huvudnamn, tillämpas den. 

- Om det inte finns någon domäntips och ingen princip uttryckligen tilldelas tjänstens huvudnamn, tillämpas en princip som uttryckligen tilldelas den överordnade organisationen för tjänsthuvudhuvudhuvudet. 

- Om det inte finns någon domäntips och ingen princip har tilldelats tjänstens huvudnamn eller organisationen används standard-HRD-beteendet.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Självstudiekurs för att ange HRD-princip för ett program 
Vi använder Azure AD PowerShell-cmdlets för att gå igenom några scenarier, bland annat:


- Ställa in HRD-princip för att göra automatisk acceleration för ett program i en klient med en enda federerad domän.

- Ställa in HRD-princip för att göra automatisk acceleration för ett program till en av flera domäner som har verifierats för din klientorganisation.

- Konfigurera HRD-principen för att göra det möjligt för ett äldre program att göra direkt användarnamn/lösenordsautentisering till Azure Active Directory för en federerad användare.

- Lista de program som en princip har konfigurerats för.


### <a name="prerequisites"></a>Krav
I följande exempel skapar, uppdaterar, länkar och tar du bort principer för programtjänstens huvudnamn i Azure AD.

1.  Börja med att hämta den senaste azure AD PowerShell-cmdlet-förhandsversionen. 

2.  När du har hämtat Azure AD PowerShell-cmdletar kör du kommandot Anslut för att logga in på Azure AD med ditt administratörskonto:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Kör följande kommando om du vill visa alla principer i organisationen:

    ``` powershell
    Get-AzureADPolicy
    ```

Om inget returneras betyder det att du inte har några principer som skapats i din klientorganisation.

### <a name="example-set-hrd-policy-for-an-application"></a>Exempel: Ange HRD-princip för ett program 

I det här exemplet skapar du en princip som antingen: 
- Automatiskt accelererar användare till en AD FS-inloggningsskärm när de loggar in på ett program när det finns en enda domän i din klientorganisation. 
- Automatiskt accelererar användare till en AD FS-inloggningsskärm finns det mer än en federerad domän i din klientorganisation.
- Aktiverar icke-interaktiv inloggning för användarnamn/lösenord direkt till Azure Active Directory för federerade användare för de program som principen har tilldelats.

#### <a name="step-1-create-an-hrd-policy"></a>Steg 1: Skapa en HRD-princip

Följande princip accelererar automatiskt användare till en AD FS-inloggningsskärm när de loggar in på ett program när det finns en enda domän i din klientorganisation.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
Följande princip accelererar automatiskt användare till en AD FS-inloggningsskärm finns det mer än en federerad domän i din klientorganisation. Om du har mer än en federerad domän som autentiserar användare för program måste du ange vilken domän som ska accelereras automatiskt.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Om du vill skapa en princip för att aktivera autentisering av användarnamn och lösenord för federerade användare direkt med Azure Active Directory för specifika program kör du följande kommando:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Om du vill visa den nya principen och hämta dess **ObjectID**kör du följande kommando:

``` powershell
Get-AzureADPolicy
```


Om du vill tillämpa HRD-principen när du har skapat den kan du tilldela den till flera huvudnamn för programtjänsten.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Steg 2: Leta reda på tjänstens huvudnamn som principen ska tilldelas  
Du behöver **ObjectID** för de tjänsthuvudnamn som du vill tilldela principen till. Det finns flera sätt att hitta **ObjectID** för tjänstens huvudnamn.    

Du kan använda portalen eller fråga [efter Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta). Du kan också gå till [Graph Explorer Tool](https://developer.microsoft.com/graph/graph-explorer) och logga in på ditt Azure AD-konto för att se alla organisationens tjänsthuvudnamn. 

Eftersom du använder PowerShell kan du använda följande cmdlet för att lista tjänstens huvudnamn och deras ID:n.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Steg 3: Tilldela principen till tjänstens huvudnamn  
När du har **objectID** för tjänstens huvudnamn för det program som du vill konfigurera automatisk acceleration för kör du följande kommando. Det här kommandot associerar HRD-principen som du skapade i steg 1 med tjänstens huvudnamn som du hittade i steg 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Du kan upprepa det här kommandot för varje tjänsthuvudnamn som du vill lägga till principen till.

Om ett program redan har en HomeRealmDiscovery-princip tilldelad kan du inte lägga till en andra.  I så fall ändrar du definitionen av principen Home Realm Discovery som har tilldelats programmet för att lägga till ytterligare parametrar.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Steg 4: Kontrollera vilka programtjänsthuvudnamn din HRD-princip har tilldelats
Om du vill kontrollera vilka program som har HRD-principen konfigurerad använder du cmdleten **Get-AzureADPolicyAppliedObject.** Skicka det **till ObjectID** för den princip som du vill kontrollera.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Steg 5: Du är klar!
Försök med programmet för att kontrollera att den nya principen fungerar.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Exempel: Lista de program som HRD-principen har konfigurerats för

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Steg 1: Lista alla principer som har skapats i organisationen 

``` powershell
Get-AzureADPolicy
```

Observera **ObjectID för** den princip som du vill visa tilldelningar för.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Steg 2: Lista de tjänsthuvudnamn som principen har tilldelats  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Exempel: Ta bort en HRD-princip för ett program
#### <a name="step-1-get-the-objectid"></a>Steg 1: Hämta ObjectID
Använd föregående exempel för att hämta **ObjectID** för principen och för det programtjänsthuvudnamn som du vill ta bort det från. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Steg 2: Ta bort principtilldelningen från programtjänstens huvudnamn  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Steg 3: Kontrollera borttagningen genom att ange de huvudnamn för tjänsten som principen har tilldelats 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>Nästa steg
- Mer information om hur autentisering fungerar i Azure AD finns i [Autentiseringsscenarier för Azure AD](../develop/authentication-scenarios.md).
- Mer information om enkel inloggning för användare finns i [Enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md).
- Besök [Microsofts identitetsplattform](../develop/v2-overview.md) för en översikt över allt utvecklarrelaterat innehåll.
