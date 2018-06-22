---
title: Konfigurera inloggning auto-acceleration för ett program med hjälp av en princip för identifiering av startsfär | Microsoft Docs
description: Beskriver vad en Azure AD-klient är och hur du hanterar Azure via Azure Active Directory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 06/08/2018
ms.author: barbkess
ms.openlocfilehash: f2ab0a4458c83aa9e5c9cee4875e41c24f615018
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301207"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurera Azure Active Directory inloggningsalternativ beteendet för ett program med hjälp av en princip för identifiering av startsfär

Följande dokument innehåller en introduktion till att konfigurera autentiseringsinställningar för Azure Active Directory för externa användare.   Den omfattar konfiguration av automatisk acceleration och autentisering begränsningar för användare i externa domäner.

## <a name="home-realm-discovery"></a>Identifiering av startsfär
Identifiering av startsfär (HRD) är en process som gör att Azure Active Directory (Azure AD) för att avgöra om en användare behöver autentisera vid inloggning tidpunkt.  När en användare loggar in till en Azure AD-klient till en resurs eller till den Azure AD vanliga inloggningssidan, skriver de en (user Principal name). Azure AD används den för att identifiera om användaren behöver logga in. 

Användaren kan behöva tas till någon av följande platser kan verifieras:

- Hem innehavaren av användaren (kan vara samma klientorganisation som resursen som användaren försöker komma åt). 

- Microsoft-konto.  Användaren är gäst i resurs-klienten.

-  En lokal identitetsleverantör, till exempel Active Directory Federation Services (AD FS).

- En annan identitetsleverantör federerade med Azure AD-klient.

## <a name="auto-acceleration"></a>Automatisk acceleration 
Vissa organisationer konfigurera domäner i sina Azure Active Directory-klient för att federera med en annan IdP, till exempel AD FS för autentisering av användare.  

När en användare loggar in på ett program, visas de först en Azure AD-inloggningssida. När de har skrivit deras UPN om de finns i en federerad domän kommer de sedan till inloggningssidan för IdP betjänar domänen. Under vissa omständigheter kan administratörer vill dirigera användare till inloggningssidan när de loggar in på specifika program. 

Användare kan därför hoppa över den första sidan i Azure Active Directory. Den här processen kallas ”logga in automatiskt-acceleration”.

I fall där klienten är federerat till en annan IdP för inloggning gör automatisk acceleration användaren logga in mer effektiv.  Du kan konfigurera automatisk acceleration för enskilda program.

>[!NOTE]
>Om du konfigurerar ett program för automatisk acceleration kan inte gästanvändare logga in. Om du skapar en användare direkt till en federerad IdP för autentisering, går det inte att att gå tillbaka till sidan för Azure Active Directory. Gästanvändare som kan behöva dirigeras till andra klienter eller en extern IdP, till exempel ett Microsoft-konto, kan inte logga in på programmet eftersom de hoppar över identifiering av startsfär steg.  

Det finns två sätt att kontrollera auto-acceleration till en federerad IdP:   

- Använd en ledtråd för domänen på begäranden om autentisering för ett program. 
- Konfigurera en princip för identifiering av startsfär för att aktivera automatisk acceleration.

### <a name="domain-hints"></a>Domän-tips    
Domän tips är direktiv som ingår i autentiseringsbegäran från ett program. De kan användas för att öka takten användaren att deras federerade IdP-inloggningssida. Eller de kan användas av ett program med flera innehavare för att öka takten användaren direkt till den anpassade Azure AD-inloggningssida för klienten.  

Programmet ”largeapp.com” kan till exempel aktivera sina kunder kan komma åt program på en anpassad URL ”contoso.largeapp.com”. Appen kan även innehålla en ledtråd domänen contoso.com i autentiseringsbegäran. 

Domän-tipset syntax varierar beroende på det protokoll som används och konfigureras vanligtvis i programmet.

**WS-Federation**: whr=contoso.com i frågesträngen.

**SAML**: antingen en SAML-autentiseringsbegäran som innehåller en ledtråd för domänen eller en fråga sträng whr=contoso.com.

**Öppna ID Connect**: en sträng domain_hint=contoso.com för frågan. 

Om en ledtråd för domänen ingår i autentiseringsbegäran från programmet, och klienten är federerat med domänen, försöker Azure AD att omdirigera inloggning till IdP som har konfigurerats för domänen. 

Om domän-tipset inte refererar till en federerad verifierad domän, ignoreras och normal identifiering av startsfär anropas.

Mer information om automatisk acceleration med domänen tipsen som stöds av Azure Active Directory finns i [Enterprise Mobility + Security blog](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Om en ledtråd för domänen ingår i en autentiseringsbegäran åsidosätter sig automatiskt acceleration som har angetts för programmet i HRD princip.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Hem princip för identifiering av startsfär för automatisk acceleration
Vissa program ger ett sätt att konfigurera autentiseringsbegäran avger. I dessa fall går inte att använda domänen tips för att styra automatisk acceleration. Automatisk acceleration kan konfigureras via Grupprincip för att få samma beteende.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Aktivera direkt-autentisering för äldre program
Det är bra för program att använda AAD-bibliotek och interaktiv inloggning för att autentisera användare. Biblioteken hand ta om federerade användare flöden.  Ibland äldre program skrivas inte till förstå federation. De att utföra inte identifiering av startsfär och samverka inte med rätt federerade slutpunkten för att autentisera en användare. Om du väljer att kan du använda HRD princip för att aktivera vissa äldre program som skickar autentiseringsuppgifter vid autentisering direkt med Azure Active Directory. Lösenordshashsynkronisering måste aktiveras. 

> [!IMPORTANT]
> Endast aktivera direkt-autentisering om du har aktiverat lösenordets Hash-synkronisering och du vet att det går att autentisera det här programmet utan några principer som implementeras av din lokala IdP. Om du inaktiverar Lösenordshashsynkronisering eller stänga av katalogsynkronisering med AD Connect av någon anledning, bör du ta bort den här principen för att förhindra möjligheten att direkt-autentisering med inaktuella lösenords-hash.

## <a name="set-hrd-policy"></a>Ange HRD-princip
Det finns tre steg för att inställningen HRD för på ett program för federerad inloggning auto-acceleration eller direkt molnbaserade program:

1. Skapa en princip för HRD.

2. Leta upp huvudnamn för tjänsten som du vill koppla principen.

3. Koppla principen till tjänstens huvudnamn. 

Principerna börjar inte gälla för ett visst program när de är anslutna till en tjänstens huvudnamn. 

Endast en HRD-princip kan vara aktiv på ett huvudnamn för tjänsten åt gången.  

Du kan använda Microsoft Azure Active Directory Graph API direkt eller Azure Active Directory PowerShell-cmdlets för att skapa och hantera principer för HRD.

Graph-API som manipulerar princip beskrivs i den [åtgärder på principen](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) artikel på MSDN.

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

Typ av princip är ”HomeRealmDiscoveryPolicy”.

**AccelerateToFederatedDomain** är valfritt. Om **AccelerateToFederatedDomain** är false principen har ingen effekt på automatisk acceleration. Om **AccelerateToFederatedDomain** är true och det är bara en verifierad och federerade domänen i innehavare och användare kommer att tas rakt till federerade IdP för inloggning. Om det är true och det finns fler än en verifierad domän i klienten, **PreferredDomain** måste anges.

**PreferredDomain** är valfritt. **PreferredDomain** bör ange en domän som du vill påskynda. Det kan utelämnas om klienten har endast en federerad domän.  Om det utelämnas, och det finns fler än en verifierad federerade domänen, har principen ingen effekt.

 Om **PreferredDomain** anges måste den matcha en verifierad, federerad domän för klienten. Alla användare av programmet måste kunna logga in på domänen.

**AllowCloudPasswordValidation** är valfritt. Om **AllowCloudPasswordValidation** är true och sedan programmet tillåts att autentisera en federerad användare genom att presentera autentiseringsuppgifter direkt till tokenslutpunkten Azure Active Directory. Detta fungerar bara om lösenordets Hash-synkronisering är aktiverat.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioritet och utvärdering av HRD-principer
HRD-principer kan skapas och sedan tilldelas specifika organisationer och tjänstens huvudnamn. Det innebär att det är möjligt för flera principer ska gälla för ett visst program. Dessa regler: HRD-principen som påverkas


- Om domän-tipset finns i autentiseringsbegäran ignoreras eventuella HRD-principen för automatisk acceleration. Det beteende som anges av tipset domän används.

- Annars, om en princip tilldelas explicit till tjänstens huvudnamn, den tillämpas. 

- Om det finns ingen domän-tipset och ingen princip uttryckligen har tilldelats till tjänstens huvudnamn, tillämpas en princip som uttryckligen har tilldelats den överordnade organisationen tjänstens huvudnamn. 

- Om det finns ingen domän-tipset och ingen princip har tilldelats till tjänstens huvudnamn eller organisationen, används standardbeteendet för HRD.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Självstudier för principen för HRD på ett program 
Vi använder Azure AD PowerShell-cmdlets för att gå igenom några scenarier, inklusive:


- Ställa in HRD princip för att göra automatisk acceleration för ett program i en klient till en federerad domän.

- Ställa in HRD princip för att göra automatisk acceleration för ett program till en av flera domäner som har verifierats för din klient.

- Ställa in HRD princip för att aktivera äldre program att dirigera användarnamn/lösenord för autentisering till Azure Active Directory för en federerad användare.

- Visar en lista över program som är konfigurerad för en princip.


### <a name="prerequisites"></a>Förutsättningar
I följande exempel du skapa, uppdatera, länkar och ta bort principer på programmet tjänstens huvudnamn i Azure AD.

1.  Hämta den senaste Azure AD PowerShell-cmdlet förhandsversionen till att börja. 

2.  När du har hämtat Azure AD PowerShell-cmdlet: ar, kör du kommandot Anslut för att logga in på Azure AD med ditt administratörskonto:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Kör följande kommando för att se alla principer i din organisation:

    ``` powershell
    Get-AzureADPolicy
    ```

Om ingenting returneras innebär det att du har inga principer som skapas i din klient.

### <a name="example-set-hrd-policy-for-an-application"></a>Exempel: Set HRD princip för ett program 

I det här exemplet skapar du en princip som när den är tilldelad till ett program antingen: 
- Automatisk accelererar-användare till en AD FS på inloggningsskärmen när de loggar in på ett program när det finns en enda domän i din klient. 
- Automatisk accelererar användare till en AD FS på inloggningsskärmen det är mer än en federerad domän i din klient.
- Gör det möjligt för icke-interaktiv användarnamn/lösenord logga in direkt till Azure Active Directory för externa användare för de program som principen har tilldelats.

#### <a name="step-1-create-an-hrd-policy"></a>Steg 1: Skapa en HRD-princip

Följande princip accelererar automatiskt-användare till en AD FS på inloggningsskärmen när de loggar in på ett program när det finns en enda domän i din klient.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
Följande princip påskyndar auto-användare till en AD FS på inloggningsskärmen finns mer än en federerad domän i din klient. Om du har mer än en federerad domän som autentiserar användare för program, måste du ange domänen för att påskynda automatiskt.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Om du vill skapa en princip för att aktivera autentisering av användarnamn/lösenord för federerade användare direkt med Azure Active Directory för specifika program, kör du följande kommando:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Se din nya princip och hämta dess **ObjectID**, kör du följande kommando:

``` powershell
Get-AzureADPolicy
```


Om du vill tillämpa principen HRD när du har skapat den, kan du tilldela det till flera program tjänstens huvudnamn.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Steg 2: Hitta tjänsten som du vill tilldela principen  
Du behöver den **ObjectID** av tjänsten-säkerhetsobjekt som du vill tilldela principen. Det finns flera sätt att hitta den **ObjectID** för tjänstens huvudnamn.    

Du kan använda portalen eller fråga [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Du kan även gå till den [diagram Explorer verktyget](https://graphexplorer.cloudapp.net/) och logga in på Azure AD-kontot för att se din organisations tjänstens huvudnamn. Du kan använda cmdlet get-AzureADServicePrincipal för att lista tjänstens huvudnamn och deras ID eftersom du använder PowerShell.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Steg 3: Tilldela principen till tjänstens huvudnamn  
När du har den **ObjectID** av tjänstens huvudnamn för programmet som du vill konfigurera automatisk acceleration, kör du följande kommando. Det här kommandot associerar HRD-principen som du skapade i steg 1 med tjänstens huvudnamn som du letade upp i steg 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Du kan upprepa det här kommandot för varje huvudnamn för tjänsten som du vill lägga till principen.

I fall där ett program redan har tilldelats en HomeRealmDiscovery princip du inte lägga till ett andra.  I så fall ändra definitionen för identifiering av startsfär principer som har tilldelats programmet att lägga till ytterligare parametrar.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Steg 4: Kontrollera vilka program tjänstens huvudnamn HRD-principen tilldelas till
Kontrollera vilka program har HRD-principen är konfigurerad använder den **Get-AzureADPolicyAppliedObject** cmdlet. Skickar den **ObjectID** på den princip som du vill kontrollera.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Steg 5: Du är klar!
Försök att kontrollera att den nya principen fungerar.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Exempel: Lista programmen för vilka HRD principen har konfigurerats

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Steg 1: Visa en lista över alla principer som har skapats i din organisation 

``` powershell
Get-AzureADPolicy
```

Observera den **ObjectID** på den princip som du vill lista tilldelningar för.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Steg 2: Visa en lista över tjänstens huvudnamn som principen har tilldelats  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Exempel: Ta bort en princip för HRD för ett program
#### <a name="step-1-get-the-objectid"></a>Steg 1: Hämta ObjectID
Använda föregående exempel för att få den **ObjectID** av principen och som program-tjänstens huvudnamn som du vill ta bort den. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Steg 2: Ta bort principen från programmet tjänstens huvudnamn  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Steg 3: Kontrollera borttagningen genom att ange tjänstens huvudnamn som principen har tilldelats 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Nästa steg
- Mer information om hur autentisering fungerar i Azure AD finns [autentiseringsscenarier för Azure AD](../develop/active-directory-authentication-scenarios.md).
- Mer information om användare enkel inloggning finns [programåtkomst och enkel inloggning med Azure Active Directory](configure-single-sign-on-portal.md).
- Besök den [Active Directory-guide för utvecklare](../develop/active-directory-developers-guide.md) en översikt över alla utvecklare-relaterat innehåll.
