---
title: Konfigurera inloggning automatisk acceleration för ett program med en princip för identifiering av startsfär | Microsoft Docs
description: Förklarar vad en Azure AD-klient är och hur du hanterar Azure via Azure Active Directory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: barbkess
ms.openlocfilehash: f24be44b00f9c4e789e8d4797f6a0516dcfe940f
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494065"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurera Azure Active Directory-inloggning i beteendet för ett program med hjälp av en princip för identifiering av startsfär

Följande dokument innehåller en introduktion till att konfigurera beteendet för Azure Active Directory-autentisering för federerade användare.   Den omfattar konfiguration av automatisk acceleration och autentisering begränsningar för användare på federerade domäner.

## <a name="home-realm-discovery"></a>Identifiering av startsfär
Identifiering av Home startsfär (HRD) är den process som gör att Azure Active Directory (Azure AD) för att avgöra om en användare behöver autentisera på inloggningstid.  När en användare loggar in till en Azure AD-klient till en resurs eller till Azure AD vanliga-inloggningssidan ange de ett användarnamn (UPN). Azure AD använder som för att upptäcka där användarna måste logga in. 

Användaren kan behöva åtgärdas till någon av följande platser för att autentiseras:

- Startklientorganisation för användaren (kan vara samma klient som den resurs som användaren försöker komma åt). 

- Microsoft-konto.  Användaren är gäst i klienten för resursen.

-  En lokal identitetsprovider som Active Directory Federation Services (AD FS).

- Någon annan identitetsprovider som är federerad med Azure AD-klient.

## <a name="auto-acceleration"></a>Automatisk acceleration 
Vissa organisationer konfigurera domäner i sina Azure Active Directory-klient för att federera med en annan IDP: N, till exempel AD FS för autentisering av användare.  

När en användare loggar in i ett program, visas de först en Azure AD-inloggningssida. När de har skrivit sina UPN, om de finns i en federerad domän tas de sedan till sidan logga in på IDP: N som betjänar den domänen. Under vissa omständigheter kan administratörer vill dirigera användare till sidan logga in när de loggar in på specifika program. 

Användare kan därmed hoppa över den första sidan i Azure Active Directory. Den här processen kallas ”logga in automatisk acceleration”.

I fall där klienten är federerat till en annan IDP: N för att logga in gör automatisk acceleration användaren logga in mer strömlinjeformad.  Du kan konfigurera automatisk acceleration för enskilda program.

>[!NOTE]
>Om du konfigurerar ett program för automatisk acceleration för kan inte gästanvändare logga in. Om du skapar en användare direkt till en federerad IDP: N för autentisering, finns det inget sätt att de kan gå tillbaka till inloggningssidan för Azure Active Directory. Gästanvändare som kan behöva dirigeras till andra klienter eller en extern IDP: N, till exempel ett Microsoft-konto, kan inte logga in till programmet eftersom de hoppar över identifiering av startsfär steg.  

Det finns två sätt att kontrollera automatisk acceleration för till en federerad IDP: N:   

- Använd ett domäntips på begäranden om autentisering för ett program. 
- Konfigurera en princip för identifiering av startsfär för att aktivera automatisk acceleration.

### <a name="domain-hints"></a>Tips för domänen    
Tips för domänen är direktiv som ingår i autentiseringsbegäran från ett program. De kan användas för att påskynda användaren till sina federerad IdP-inloggningssida. Eller de kan användas av ett program för flera innehavare för att påskynda användaren direkt till den anpassade Azure AD-inloggningssida för klienten.  

Programmet ”largeapp.com” kan till exempel aktivera sina kunder att komma åt programmet på en anpassad URL ”contoso.largeapp.com”. Appen kan även innehålla ett domäntips på contoso.com i autentiseringsbegäran. 

Domän-tipset syntax varierar beroende på vilket protokoll som används och konfigureras vanligtvis i programmet.

**WS-Federation**: whr=contoso.com i frågesträngen.

**SAML**: antingen en SAML-autentisering-begäran som innehåller ett domäntips eller en fråga sträng whr=contoso.com.

**Öppna ID Connect**: en sträng domain_hint=contoso.com för frågan. 

Om ett domäntips ingår i autentiseringsbegäran från programmet, och klienten är federerad med domänen, försöker Azure AD omdirigerar logga in till IDP: N som har konfigurerats för domänen. 

Om tips för domänen inte refererar till en verifierad federerad domän, ignoreras och normal identifiering av startsfär anropas.

Mer information om automatisk acceleration med domän-tips som stöds av Azure Active Directory finns i den [Enterprise Mobility + Security-bloggen](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Om ett domäntips ingår i en autentiseringsbegäran, åsidosätter sin närvaro automatisk acceleration som har angetts för programmet i HRD-princip.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Home princip för identifiering av startsfär för automatisk acceleration
Vissa program ger ett sätt att konfigurera de sänder autentiseringsbegäran. I dessa fall kan går det inte att använda domänen tips för att styra automatisk acceleration. Automatisk acceleration kan konfigureras via Grupprincip att uppnå samma beteende.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Aktivera direkt autentisering för äldre program
Det är bra för program att använda AAD-bibliotek och interaktiv inloggning för att autentisera användare. Biblioteken hand tar om federerad användarflöden.  Ibland äldre program skrivits inte för att förstå federation. De kan utföra inte identifiering av startsfär och samverka inte med rätt federerad slutpunkt för att autentisera en användare. Om du vill kan du använda HRD-princip för att aktivera vissa äldre program som skickar autentiseringsuppgifter att autentisera direkt med Azure Active Directory. Lösenordets Hash-synkronisering måste vara aktiverat. 

> [!IMPORTANT]
> Aktivera endast direkt autentisering om du har aktiverat lösenordets Hash-synkronisering och du vet att det är Okej att autentisera det här programmet utan alla principer som implementeras av den lokala IDP: N. Om du inaktiverar Lösenordshashsynkronisering eller stänga av katalogsynkronisering med AD Connect av någon anledning, bör du ta bort den här principen för att eliminera risken för direkt autentisering med hjälp av inaktuella lösenords-hash.

## <a name="set-hrd-policy"></a>Ange HRD-princip
Det finns tre steg för att inställningen HRD-princip på ett program för federerad inloggning automatisk acceleration eller direkt molnbaserade program:

1. Skapa en HRD-princip.

2. Hitta tjänstens huvudnamn som du vill koppla principen.

3. Koppla principen till tjänstens huvudnamn. 

Principerna börjar inte gälla för ett visst program när de är anslutna till ett huvudnamn för tjänsten. 

Endast en HRD-princip kan vara aktiv på ett huvudnamn för tjänsten åt gången.  

Du kan använda Microsoft Azure Active Directory Graph API direkt eller Azure Active Directory PowerShell-cmdlets för att skapa och hantera HRD-princip.

Graph-API som manipulerar princip beskrivs i den [åtgärder i principen](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) artikeln på MSDN.

Följande är ett exempel HRD principdefinitionen:
    
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

Principtypen är ”HomeRealmDiscoveryPolicy”.

**AccelerateToFederatedDomain** är valfritt. Om **AccelerateToFederatedDomain** är falsk, principen har ingen effekt på automatisk acceleration. Om **AccelerateToFederatedDomain** är true och det är bara en verifierad och federerad domän i klienten och sedan användarna kommer att tas direkt till federerad IDP: N för inloggning. Om det är SANT och det finns fler än en verifierad domän i klienten, **PreferredDomain** måste anges.

**PreferredDomain** är valfritt. **PreferredDomain** ska indikera en domän som du vill påskynda. Det kan utelämnas om klienten har endast en federerad domän.  Om det utelämnas, och det finns fler än en verifierad federerad domän, har principen ingen effekt.

 Om **PreferredDomain** anges, måste den matcha en verifierad, federerad domän för klienten. Alla användare av programmet måste kunna logga in på domänen.

**AllowCloudPasswordValidation** är valfritt. Om **AllowCloudPasswordValidation** är SANT och sedan programmet tillåts att autentisera en federerad användare genom att presentera autentiseringsuppgifter direkt till Azure Active Directory tokenslutpunkten. Detta fungerar bara om lösenordets Hash-synkronisering är aktiverat.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioritet och utvärderingen av HRD-principer
Identifieringen av Startsfären principer skapas och tilldelas till specifika organisationer och tjänstens huvudnamn. Det innebär att det är möjligt för flera principer för ett visst program. Dessa regler: HRD-princip som påverkas


- Om ett domäntips finns i autentiseringsbegäran ignoreras alla HRD-princip för automatisk acceleration. Det beteende som anges av tips för domänen används.

- Annars, om en princip är explicit tilldelade till tjänstens huvudnamn, den tillämpas. 

- Om det finns inga tips för domänen och ingen princip uttryckligen har tilldelats till tjänstens huvudnamn, tillämpas en princip som uttryckligen har tilldelats den överordnade organisationen för tjänstens huvudnamn. 

- Om det finns inga tips för domänen och ingen princip har tilldelats till tjänstens huvudnamn eller organisationen, används standardbeteendet för identifieringen av Startsfären.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Självstudie för att ställa in HRD-princip på ett program 
Vi använder Azure AD PowerShell-cmdletar för att gå igenom några scenarier, inklusive:


- Ställa in HRD-princip för att göra automatisk acceleration för ett program i en klient till en federerad domän.

- Ställa in HRD-princip för att göra automatisk acceleration för ett program till en av flera domäner som har verifierats för din klient.

- Att konfigurera HRD-princip för ett äldre program att dirigera autentisering med användarnamn/lösenord till Azure Active Directory för en extern användare.

- Visa en lista över de program som en princip har konfigurerats.


### <a name="prerequisites"></a>Förutsättningar
I följande exempel har du skapa, uppdatera, länka och ta bort principer på tjänsthuvudnamn för programmet i Azure AD.

1.  Börja genom att ladda ned den senaste förhandsversionen av Azure AD PowerShell-cmdlet. 

2.  När du har hämtat Azure AD PowerShell-cmdlets, kör du kommandot Connect för att logga in på Azure AD med ditt administratörskonto:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Kör följande kommando för att se alla principer i din organisation:

    ``` powershell
    Get-AzureADPolicy
    ```

Om inget returneras innebär det att du har inga principer som skapats i din klient.

### <a name="example-set-hrd-policy-for-an-application"></a>Exempel: Set HRD-princip för ett program 

I det här exemplet skapar du en princip som när den är tilldelad till ett program antingen: 
- Automatisk accelererar-användare till en AD FS-inloggningsskärmen när de loggar in på ett program när det finns en enda domän i din klient. 
- Automatisk påskyndar användare till en AD FS-inloggningsskärmen det är mer än en federerad domän i din klient.
- Gör det möjligt för icke-interaktiv användarnamn/lösenord logga in direkt till Azure Active Directory för federerade användare för de program som principen har tilldelats.

#### <a name="step-1-create-an-hrd-policy"></a>Steg 1: Skapa en HRD-princip

Följande princip accelererar automatisk-användare till en AD FS-inloggningsskärmen när de loggar in på ett program när det finns en enda domän i din klient.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
Följande princip accelererar automatisk-användare till en AD FS-inloggningsskärmen finns mer än en federerad domän i din klient. Om du har mer än en federerad domän som autentiserar användare för program, måste du ange domänen för att påskynda automatiskt.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Om du vill skapa en princip för att aktivera autentisering med användarnamn/lösenord för federerade användare direkt med Azure Active Directory för vissa program, kör du följande kommando:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Se din nya princip och hämta dess **ObjectID**, kör du följande kommando:

``` powershell
Get-AzureADPolicy
```


Om du vill använda HRD-princip när du har skapat den, kan du tilldela den till flera program tjänstens huvudnamn.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Steg 2: Hitta tjänstens huvudnamn som att tilldela principen  
Du behöver den **ObjectID** service-säkerhetsobjekt som du vill tilldela principen. Det finns flera sätt att hitta den **ObjectID** för tjänstens huvudnamn.    

Du kan använda portalen eller du kan fråga [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Du kan även gå till den [Graph Explorer-verktyget](https://developer.microsoft.com/graph/graph-explorer) och logga in på ditt Azure AD-konto finns i din organisations tjänstens huvudnamn. Du kan använda cmdleten get-AzureADServicePrincipal för att visa tjänstens huvudnamn och deras ID eftersom du använder PowerShell.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Steg 3: Tilldela principen till tjänstens huvudnamn  
När du har den **ObjectID** för tjänstens huvudnamn för programmet som du vill konfigurera automatisk acceleration, kör du följande kommando. Det här kommandot associerar HRD-princip som du skapade i steg 1 med tjänstens huvudnamn som du letade upp i steg 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Du kan upprepa det här kommandot för varje tjänstens huvudnamn som du vill lägga till principen.

I fall där ett program redan har en tilldelad policy för HomeRealmDiscovery du inte lägga till en andra profil.  I så fall ändra definitionen för den princip för identifiering av startsfär som tilldelas till programmet för att lägga till ytterligare parametrar.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Steg 4: Kontrollera vilka program tjänsthuvudnamn HRD-princip har tilldelats
Att kontrollera vilka program har konfigurerats HRD-princip kan använda den **Get-AzureADPolicyAppliedObject** cmdlet. Skickar den de **ObjectID** på den princip som du vill kontrollera.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Steg 5: Är du klar!
Försök att kontrollera att den nya principen fungerar.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Exempel: Lista programmen för vilka HRD principen har konfigurerats

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Steg 1: Lista över alla principer som har skapats i din organisation 

``` powershell
Get-AzureADPolicy
```

Obs den **ObjectID** på den princip som du vill lista tilldelningar för.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Steg 2: Visa tjänsthuvudnamnen som principen är tilldelad  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Exempel: Ta bort en HRD-princip för ett program
#### <a name="step-1-get-the-objectid"></a>Steg 1: Hämta ObjectID
Använd exemplet ovan för att få den **ObjectID** av principen och som program-tjänstens huvudnamn som du vill ta bort den. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Steg 2: Ta bort principen för program tjänstens huvudnamn  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Steg 3: Kontrollera borttagningen genom att ange tjänsthuvudnamnen som principen är tilldelad 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Nästa steg
- Mer information om hur autentisering fungerar i Azure AD finns i [autentiseringsscenarier för Azure AD](../develop/authentication-scenarios.md).
- Mer information om användare enkel inloggning finns i [programåtkomst och enkel inloggning med Azure Active Directory](configure-single-sign-on-portal.md).
- Gå till den [utvecklarguide för Active Directory](../develop/azure-ad-developers-guide.md) en översikt över alla developer-relaterat innehåll.
