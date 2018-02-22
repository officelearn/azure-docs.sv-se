---
title: "Konfigurera inloggning auto-acceleration för ett program med hjälp av en princip för identifiering av startsfär | Microsoft Docs"
description: "Beskriver vad en Azure AD-klient är och hur du hanterar Azure via Azure Active Directory."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: deaa52a062eb01450f760324e01e520fcbe894e1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurera inloggning auto-acceleration för ett program med hjälp av en princip för identifiering av startsfär

Följande dokument innehåller en introduktion till identifiering av startsfär och automatisk acceleration.

## <a name="home-realm-discovery"></a>Identifiering av startsfär
Identifiering av startsfär (HRD) är en process som gör att Azure Active Directory (Azure AD) för att fastställa samtidigt inloggning där en användare behöver att autentisera.  När en användare loggar in till en Azure AD-klient till en resurs eller till den Azure AD vanliga inloggningssidan, skriver de en (user Principal name). Azure AD används den för att identifiera om användaren behöver logga in. 

Användaren kan behöva tas till någon av följande platser kan verifieras:

- Hem innehavaren av användaren (kan vara samma klientorganisation som resursen som användaren försöker komma åt). 

- Microsoft-konto.  Användaren är gäst i resurs-klienten.

- En annan identitetsleverantör federerade med Azure AD-klient.

-  En lokal identitetsleverantör, till exempel Active Directory Federation Services (AD FS).

## <a name="auto-acceleration"></a>Automatisk acceleration 
Vissa organisationer konfigurera sina Azure Active Directory-klient för att federera med en annan IdP, till exempel AD FS för autentisering av användare.  

I dessa fall när en användare loggar till ett program visas de först först med en Azure AD-inloggningssida. De tas sedan till sidan för IdP när de har skrivit deras UPN. Under vissa omständigheter kan administratörer vill dirigera användare till inloggningssidan när de loggar in på specifika program. 

Detta innebär att användare kan hoppa över den första sidan i Azure Active Directory. Den här processen kallas ”logga in automatiskt-acceleration”.

I fall där klienten är federerat till en annan IdP för inloggning gör automatisk acceleration användaren logga in mer effektiv.  Du kan konfigurera automatisk acceleration för enskilda program.

>[!NOTE]
>Om du konfigurerar ett program för automatisk acceleration kan inte gästanvändare logga in. Om du skapar en användare direkt till en federerad IdP för autentisering, går det inte att att gå tillbaka till sidan för Azure Active Directory. Gästanvändare som kan behöva dirigeras till andra klienter eller en extern IdP, till exempel ett Microsoft-konto, kan inte logga in på programmet eftersom de hoppar över identifiering av startsfär steg.  

Det finns två sätt att kontrollera auto-acceleration till en federerad IdP:   

- Använd en ledtråd för domänen på begäranden om autentisering för ett program. 
- Konfigurera en princip för identifiering av startsfär för att aktivera automatisk acceleration.

## <a name="domain-hints"></a>Domän-tips 
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
>Om en ledtråd för domänen ingår i en autentiseringsbegäran åsidosätter sig eventuella HRD som har angetts för programmet.

## <a name="home-realm-discovery-policy"></a>Hem princip för identifiering av startsfär
Vissa program ger ett sätt att konfigurera autentiseringsbegäran avger. I dessa fall går inte att använda domänen tips för att styra automatisk acceleration. Automatisk acceleration kan konfigureras via Grupprincip för att få samma beteende.  

### <a name="set-hrd-policy"></a>Ange HRD-princip
Det finns tre steg för att logga in automatiskt-acceleration på ett program:


1. Skapa en princip för HRD för automatisk acceleration.

2. Söka efter tjänstens huvudnamn som du vill koppla principen.

3. Koppla principen till tjänstens huvudnamn. Principer kan ha skapats i en klient, men de har inte någon effekt förrän de är kopplade till en entitet. 

En HRD-princip kan kopplas till ett huvudnamn för tjänsten och endast en HRD-princip kan vara aktiv på en viss enhet åt gången.  

Du kan använda Microsoft Azure Active Directory Graph API direkt eller Azure Active Directory PowerShell-cmdlets för att ställa in automatisk acceleration med hjälp av HRD princip.

Graph-API som manipulerar princip beskrivs i den [åtgärder på principen](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) artikel på MSDN.

Följande är ett exempel HRD principdefinitionen:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

Typ av princip är ”HomeRealmDiscoveryPolicy”.

Om **AccelerateToFederatedDomain** är false principen har ingen effekt.

**PreferredDomain** bör ange en domän som du vill påskynda. Det kan utelämnas om klienten har endast en federerad domän.  Om det utelämnas, och det finns fler än en verifierad federerade domänen, har principen ingen effekt.

Om **PreferredDomain** anges måste den matcha en verifierad, federerad domän för klienten. Alla användare av programmet måste kunna logga in på domänen.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioritet och utvärdering av HRD-principer
HRD-principer kan skapas och sedan tilldelas specifika organisationer och tjänstens huvudnamn. Det innebär att det är möjligt för flera principer ska gälla för ett visst program. Dessa regler: HRD-principen som påverkas


- Om en ledtråd domän finns i autentiseringsbegäran ignoreras eventuella HRD-principen. Det beteende som anges av tipset domän används.

- Annars, om en princip tilldelas explicit till tjänstens huvudnamn, den tillämpas. 

- Om det finns ingen domän-tipset och ingen princip uttryckligen har tilldelats till tjänstens huvudnamn, tillämpas en princip som uttryckligen har tilldelats den överordnade organisationen tjänstens huvudnamn. 

- Om det finns ingen domän-tipset och ingen princip har tilldelats till tjänstens huvudnamn eller organisationen, används standardbeteendet för HRD.

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-by-using-an-hrd-policy"></a>Självstudier för att logga in automatiskt-acceleration på ett program med en HRD-princip
Vi använder Azure AD PowerShell-cmdlets för att gå igenom några scenarier, inklusive:


- Konfigurera automatisk acceleration för ett program för en klient till en federerad domän.

- Konfigurera automatisk acceleration för ett program till en av flera domäner som har verifierats för din klient.

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

### <a name="example-set-auto-acceleration-for-an-application"></a>Exempel: Set automatiskt acceleration för ett program 
I det här exemplet skapar du en princip som automatiskt-accelererar användare till en AD FS på inloggningsskärmen när de loggar in på ett program. Användarna kan logga in till AD FS utan att behöva ange ett användarnamn på inloggningssidan för Azure AD först. 

#### <a name="step-1-create-an-hrd-policy"></a>Steg 1: Skapa en HRD-princip
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Om du har en federerad domän som autentiserar användare för program som du behöver skapa endast en HRD-princip.  

Se din nya princip och hämta dess **ObjectID**, kör du följande kommando:

``` powershell
Get-AzureADPolicy
```


Om du vill aktivera automatisk acceleration när du har en princip för HRD, kan du tilldela den till flera program tjänstens huvudnamn.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Steg 2: Hitta tjänsten som du vill tilldela principen  
Du behöver den **ObjectID** av tjänsten-säkerhetsobjekt som du vill tilldela principen. Det finns flera sätt att hitta den **ObjectID** för tjänstens huvudnamn.    

Du kan använda portalen eller fråga [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Du kan även gå till den [diagram Explorer verktyget](https://graphexplorer.cloudapp.net/) och logga in på Azure AD-kontot för att se din organisations tjänstens huvudnamn. Du kan använda cmdlet get-AzureADServicePrincipal för att lista tjänstens huvudnamn och deras ID eftersom du använder PowerShell.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Steg 3: Tilldela principen till tjänstens huvudnamn  
När du har den **ObjectID** av tjänstens huvudnamn för programmet som du vill konfigurera automatisk acceleration, kör du följande kommando. Det här kommandot associerar HRD-principen som du skapade i steg 1 med tjänstens huvudnamn som du letade upp i steg 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Du kan upprepa det här kommandot för varje huvudnamn för tjänsten som du vill lägga till principen.

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>Steg 4: Kontrollera vilka program tjänstens huvudnamn automatiskt acceleration-principen tilldelas till
Kontrollera vilka program har automatiskt acceleration princip har konfigurerats, använda den **Get-AzureADPolicyAppliedObject** cmdlet. Skickar den **ObjectID** på den princip som du vill kontrollera.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Steg 5: Du är klar!
Försök att kontrollera att den nya principen fungerar.

### <a name="example-list-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>Exempel: Visa en lista över program som är konfigurerad för en princip för automatisk acceleration

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Steg 1: Visa en lista över alla principer som har skapats i din organisation 

``` powershell
Get-AzureADPolicy
```

Observera den **ObjectID** på den princip som du vill lista tilldelningar för.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Steg 2: Visa en lista över tjänstens huvudnamn som principen har tilldelats  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-auto-acceleration-policy-for-an-application"></a>Exempel: Ta bort en princip för automatisk acceleration för ett program
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
- Mer information om hur autentisering fungerar i Azure AD finns [autentiseringsscenarier för Azure AD](develop/active-directory-authentication-scenarios.md).
- Mer information om användare enkel inloggning finns [programåtkomst och enkel inloggning med Azure Active Directory](active-directory-enterprise-apps-manage-sso.md).
- Besök den [Active Directory-guide för utvecklare](develop/active-directory-developers-guide.md) en översikt över alla utvecklare-relaterat innehåll.
