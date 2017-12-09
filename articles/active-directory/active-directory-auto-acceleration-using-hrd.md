---
title: "Konfigurera inloggning Auto-Acceleration för ett program med hjälp av Home sfär identifiering princip | Microsoft Docs"
description: "I den här artikeln lär du dig vad en Azure AD-klient är och hur du hanterar Azure via Azure Active Directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: b1f0e5da09ef1d6acd234b72878cc71d66bb551e
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-using-home-realm-discovery-hrd-policy"></a>Konfigurera inloggning auto-acceleration för ett program med hjälp av startsfär (HRD) för identifiering princip

## <a name="introduction-to-home-realm-discovery-and-auto-acceleration"></a>Introduktion till identifiering av startsfär och automatisk acceleration
Följande dokument innehåller en introduktion till identifiering av startsfär och automatisk acceleration.

### <a name="home-realm-discovery"></a>Identifiering av startsfär
Hem identifiering av startsfär är en process som gör att Azure Active Directory att avgöra när inloggning, där en användare behöver att autentisera.  När du loggar in till en Azure AD-klient för åtkomst till en resurs eller i Azure AD vanliga inloggningssidan anger användaren ett användarnamn (UPN).  Azure AD används den identifiera där användaren behöver logga in.   Användaren kan behöva tas autentiseras till något av följande:

- Hem innehavaren av användaren (kan vara samma klientorganisation som resursen som användaren försöker komma åt). 
- Microsoft-konto.   Användaren är gäst i resurs-klient
- En annan identitetsleverantör federerade med Azure AD-klient.  En lokal identitetsleverantör till exempel AD FS för instansen.

### <a name="auto-acceleration"></a>Automatisk acceleration 
Vissa organisationer konfigurera sina Azure Active Directory-klient för att federera med en annan IdP, till exempel AD FS för autentisering av användare.  I dessa fall, när du loggar in på ett program först visas med en Azure AD-inloggningssida och när de har skrivit in deras UPN hämtas sedan till sidan för IdP.  I fall där det klokt att administratörer kan användare dirigeras direkt till sidan logga in när du loggar in till specifika program, hoppar över den första sidan i Azure Active Directory. Detta kallas att ”logga in automatiskt-acceleration”.

I fall där klienten är federerat till en annan IdP för inloggning blir aktiveras automatisk acceleration användaren logga in mer effektiv i fall där du vet att alla loggar in kan autentiseras av som IdP.  Du kan konfigurera automatisk acceleration för enskilda program.

>[!NOTE]
>Om du konfigurerar ett program för automatisk acceleration gästanvändare kan inte logga in. Tar användaren direkt till en federerad IdP för autentisering är en enkelriktad gata som det inte går att hämta tillbaka till sidan för Azure Active Directory.  Gästanvändare som kan behöva dirigeras till andra klienter eller en extern IdP som Microsoft-konto för att autentisera, inte att logga in på programmet som identifiering av startsfär steget hoppas över.  

Det finns två sätt att kontrollera auto-acceleration till en federerad IdP.  Antingen:   

- Använda en ledtråd för domänen på begäranden om autentisering för ett program 
- Konfigurera en princip för HomeRealmDiscovery om du vill aktivera automatisk acceleration

## <a name="domain-hints"></a>Domän-tips 
Domän tips är direktiv som ingår i autentiseringsbegäran från ett program.  De kan användas för att öka takten användaren att deras federerade IdP-inloggningssida eller de kan användas av ett program med flera innehavare för att öka takten användaren direkt till den anpassade Azure AD-inloggningssida för klienten.  Ett program largeapp.com kan ge sina kunder kan komma åt program på en anpassad URL contoso.largeapp.com och kan innehålla en ledtråd domänen Contoso.com i autentiseringsbegäran. Domän-tipset syntax varierar beroende på det protokoll som används och de konfigureras vanligtvis i programmet.

**WS-Federation**: whr=contoso.com i frågesträngen

**SAML**: antingen en SAML-autentisering-begäran som innehåller en ledtråd för domänen eller en fråga sträng whr=contoso.com

**Öppna ID Connect**: en fråga sträng domain_hint=contoso.com 

Om en ledtråd för domänen ingår i autentiseringsbegäran från programmet och klienten är federerat med domänen, försöker Azure AD att omdirigera inloggning till IdP som konfigurerats för domänen.  Om domän-tipset inte refererar till en federerad verifierad domän, ignoreras och identifiering av startsfär för normal anropas.

Se den här [blogginlägget](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/) mer information om automatisk acceleration med domän-tips som stöds av Azure Active Directory.

>[!NOTE]
>Om en ledtråd för domänen ingår i en autentiseringsbegäran åsidosätter sig eventuella HRD som har angetts för programmet.

## <a name="home-realm-discovery-hrd-policy"></a>Princip för startsfär identifiering (HRD)
Vissa program ger inte ett sätt att konfigurera autentiseringsbegäran avger och i dessa fall det går inte att använda domänen tips för att styra automatisk acceleration.   Automatisk acceleration kan konfigureras via Grupprincip för att få samma beteende.  

### <a name="setting-hrd-policy"></a>Principen för HRD
Det finns tre steg för att logga in automatiskt-acceleration på ett program


1. Skapa en princip för HRD för automatisk acceleration
2. Hitta tjänsten principen som du vill koppla principen
3. Koppla principen till tjänsten principen.  Principer kan ha skapats i en klient, men de har inte någon effekt förrän de är kopplade till en entitet.  En HRD-princip kan kopplas till ett huvudnamn för tjänsten och endast en HRD-princip kan vara aktiv på en viss enhet åt gången.  

Du kan använda Microsoft Azure Active Directory Graph API direkt eller Azure Active Directory PowerShell-Cmdlets för att ställa in automatisk acceleration med hjälp av HRD-princip

Graph-API som manipulerar princip beskrivs [här](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations).

Här är ett exempel HRD principdefinitionen:
    
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

Om **AccelerateToFederatedDomain** är false och sedan principen har ingen effekt **PreferredDomain** bör ange en domän som påskyndar till men kan utelämnas om klienten har endast en federerad domän.  Om det utelämnas och det finns fler än en verifierad federerade domänen har principen ingen effekt.

Om **PreferredDomain** anges måste den matcha en verifierad, federerad domän för klienten och alla användare av den aktuella ansökan måste kunna logga in på domänen.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioritet och utvärdering av HRD-principer
HRD-principer kan skapas och sedan tilldelas specifika organisationer och tjänstens huvudnamn. Det innebär att det är möjligt för flera principer ska gälla för ett visst program. Dessa regler: HRD-principen som påverkas


- Om en ledtråd domän finns i autentiseringsbegäran ignoreras eventuella HRD-principen och det beteende som angetts av tipset domän används.
- Annars, om en princip tilldelas explicit till tjänstens huvudnamn, den tillämpas. 
- Om det finns ingen domän-tipset och ingen princip uttryckligen har tilldelats till tjänstens huvudnamn, tillämpas en princip som uttryckligen har tilldelats den överordnade organisationen tjänstens huvudnamn. 
- Om det finns ingen domän-tipset och ingen princip har tilldelats till tjänstens huvudnamn eller organisationen, används standardbeteendet för HRD.

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-using-hrd-policy-with-azure-active-directory-powershell-cmdlets"></a>Självstudier för att logga in automatiskt-acceleration på ett program med hjälp av HRD-princip med Azure Active Directory PowerShell-cmdlets
Vi går igenom några scenarier, inklusive:


- Konfigurera automatisk acceleration för ett program för en klient till en federerad domän
- Konfigurera automatisk acceleration för ett program till en av flera domäner som har verifierats för din klient
- Visar en lista över program som är konfigurerad för en princip

### <a name="prerequisites"></a>Krav
I exemplen nedan du skapa, uppdatera, länkar och ta bort principer på programmet tjänstens huvudnamn i Azure AD.

1.  Hämta den senaste Azure AD PowerShell-Cmdlet förhandsversionen till att börja. 
2.  När du har Azure AD PowerShell-cmdlet: ar, kör du kommandot Connect att logga in på Azure AD med ditt administratörskonto.

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Kör följande kommando för att se alla principer i din organisation.

    ``` powershell
    Get-AzureADPolicy
    ```

Om ingenting returneras har du inga principer som skapas i din klientorganisation

### <a name="example-setting-auto-acceleration-for-an-application"></a>Exempel: Ange automatisk acceleration för ett program 
I det här exemplet kan du skapa en princip som automatiskt-accelererar användare till en AD FS inloggningssidan när du loggar in till ett program.  Detta görs utan att ange ett användarnamn på inloggningssidan för Azure AD först. 

#### <a name="step-1-create-an-hrd-policy"></a>Steg 1: Skapa en HRD-princip
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Om du har en federerad domän som autentiserar användare för program, behöver du bara skapa en princip för HRD.  
Kör följande kommando för att se din nya princip och hämta dess ObjectID.

``` powershell
Get-AzureADPolicy
```


När du har en HRD princip för att aktivera automatisk acceleration tilldela du det till flera principer för program-tjänsten.

#### <a name="step-2-locate-the-service-principal-to-assign-the-policy-to"></a>Steg 2: Leta reda på om du vill tilldela principen till tjänstens huvudnamn.  
Du måste ObjectId av tjänsten-säkerhetsobjekt som du vill tilldela principen till. Det finns flera sätt att hitta objekt-ID för tjänstens huvudnamn.    

Du kan använda portalen, du kan fråga efter den [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) eller gå till vår [diagram Explorer verktyget](https://graphexplorer.cloudapp.net/) och logga in på Azure AD-kontot för att se din organisations tjänstens huvudnamn eller eftersom du använder PowerShell, du kan använda cmdlet get-AzureADServicePrincipal service-principer och deras ID.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Steg 3: Tilldela principen till tjänstens huvudnamn  
När du har ObjectId för tjänstens huvudnamn för programmet som du vill konfigurera automatisk acceleration för, kör du följande kommando för att associera principen HRD du skapade i steg 1 med huvudnamn för tjänsten som du letade upp i steg 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Du kan upprepa det här kommandot för varje Service Principal du vill lägga till principen till.

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>Steg 4: Kontrollera vilka program tjänstens huvudnamn automatiskt acceleration-principen tilldelas till
Kontrollera vilka program har automatiskt acceleration princip har konfigurerats Använd cmdleten Get-AzureADPolicyAppliedObject och skickar den objectId för principen som du vill kontrollera.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Steg 5: Du är klar!
Försök att kontrollera att den nya principen fungerar.

### <a name="example-listing-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>Exempel: Visa en lista över program som är konfigurerad för en princip för automatisk acceleration

#### <a name="step-1-list-all-policies-created-in-your-organization"></a>Steg 1: Visa en lista över alla principer som skapas i din organisation 

``` powershell
Get-AzureADPolicy
```

Observera den **objekt-ID** på den princip som du vill lista tilldelningar för.

#### <a name="step-2-list-the-service-principals-the-policy-is-assigned-to"></a>Steg 2: Listas i tjänstens huvudnamn principen har tilldelats.  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-removing-an-auto-acceleration-policy-for-an-application"></a>Exempel: Tar bort en princip för automatisk acceleration för ett program
#### <a name="step-1-use-the-previous-example-to-get-the-objectid-of-the-policy-and-that-of-the-application-service-principal-you-wish-to-remove-it-from"></a>Steg 1: Använd det tidigare exemplet för att hämta ObjectId av principen och som du vill ta bort den från programmet tjänstens huvudnamn
#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Steg 2: Ta bort principen från programmet tjänstens huvudnamn.  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-the-policy-is-assigned-to"></a>Steg 3: Kontrollera borttagningen genom att registrera tjänstens huvudnamn principen har tilldelats 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Nästa steg
- Mer information om hur autentisering fungerar i Azure AD finns [Autentiseringsscenarier för Azure AD](develop/active-directory-authentication-scenarios.md).
- Mer information om användare enkel inloggning finns [programåtkomst och enkel inloggning med Azure Active Directory](active-directory-enterprise-apps-manage-sso.md)
- Besök den [Active Directory-guide för utvecklare](develop/active-directory-developers-guide.md) en översikt över alla utvecklare-relaterat innehåll.
