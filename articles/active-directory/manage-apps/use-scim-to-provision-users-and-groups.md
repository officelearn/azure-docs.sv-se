---
title: Automatisera etablering av appar med SCIM i Azure Active Directory | Microsoft Docs
description: Azure Active Directory kan automatiskt etablera användare och grupper till valfritt program eller en identitet Arkiv som är fronted av en webbtjänst med gränssnittet som definierats i protokollspecifikation SCIM
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a51401bcb8d282fef10b0b06e646b652bf5f8e8
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67513391"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Med hjälp av System för domänerna Identity Management (SCIM) att automatiskt etablera användare och grupper från Azure Active Directory till program

## <a name="overview"></a>Översikt

SCIM är standardiserade protokoll och scheman som syftar till att enheten större konsekvens i hur identiteter som hanteras i system. När ett program har stöd för en SCIM-slutpunkt för hantering av användare, skicka Azure AD-tjänst för användaretablering begäranden om att skapa, ändra eller ta bort tilldelade användare och grupper till den här slutpunkten. 

Många av programmen för vilka Azure AD stöder [förintegrerade automatisk användaretablering](../saas-apps/tutorial-list.md) implementera SCIM som innebär att ta emot användaren ändringsmeddelanden.  Förutom dessa kan kunder kan ansluta appar som har stöd för en specifik profil av den [SCIM 2.0 protokollspecifikation](https://tools.ietf.org/html/rfc7644) med alternativet allmän ”inte är ett galleriprogram”-integrering i Azure-portalen. 

I den här artikeln huvudsakliga fokus ligger på profilen för SCIM 2.0 som Azure AD implementerar som en del av dess allmän SCIM-anslutningsapp för appar som inte finns i galleriet. Lyckad testning av ett program som stöder dock SCIM med allmänna Azure AD connector är ett steg för att få en app som visas i Azure AD-galleriet som ytterligare etableringen av användare. Läs mer på att få ditt program listat i Azure AD-programgalleriet [så här: Lista ditt program i Azure AD-programgalleriet](../develop/howto-app-gallery-listing.md).
 

>[!IMPORTANT]
>Beteendet för Azure AD SCIM-implementering uppdaterades senast 18 December 2018. Information om vad som ändrats finns [SCIM 2.0-protokollet kompatibiliteten för Azure AD-användare etableringstjänsten](application-provisioning-config-problem-scim-compatibility.md).

![][0]
*Bild 1: Från Azure Active Directory-etablering till ett program eller en identity-Arkiv som implementerar SCIM*

Den här artikeln är uppdelad i fyra delar:

* **[Etablering av användare och grupper till program från tredje part som har stöd för SCIM 2.0](#provisioning-users-and-groups-to-applications-that-support-scim)**  – om din organisation använder en tredjepartsprogram att implementerar profil av SCIM 2.0 Azure AD stöder, du kan börja automatisera både aktivering och inaktivering av användare och grupper i dag.

* **[Förstå Azure AD SCIM-implementeringen](#understanding-the-azure-ad-scim-implementation)**  -om du skapar ett program som stöder en SCIM 2.0 Användarhantering API är det här avsnittet beskrivs i detalj hur Azure AD SCIM-klienten har implementerats och hur du ska modellera ditt SCIM-protokoll begära felhantering och svar.
  
* **[Att skapa en SCIM-slutpunkten med hjälp av Microsoft CLI bibliotek](#building-a-scim-endpoint-using-microsoft-cli-libraries)**  -bibliotek för Common Language Infrastructure (CLI) tillsammans med kodexempel visar hur du utvecklar en SCIM-slutpunkt och översätta SCIM meddelanden.  

* **[Schemareferens i användar- och](#user-and-group-schema-reference)**  -beskriver schemat användare och grupper som stöds av Azure AD SCIM-implementeringen för appar som inte finns i galleriet. 

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Etablering av användare och grupper till program som stöder SCIM
Azure AD kan konfigureras att automatiskt etablera tilldelade användare och grupper till program som implementerar en specifik profil av den [SCIM 2.0-protokollet](https://tools.ietf.org/html/rfc7644). Information om profilen finns dokumenterade i [förstå implementeringen av Azure AD SCIM](#understanding-the-azure-ad-scim-implementation).

Kontrollera med din leverantör av program eller ditt program leverantörens dokumentation för instruktioner för kompatibilitet med dessa krav.

>[!IMPORTANT]
>Azure AD SCIM-implementering bygger på Azure AD-användare provisioning-tjänsten, som är utformad för att ständigt synkronisera användare mellan Azure AD och målprogrammet och implementerar en särskild uppsättning standard-åtgärder. Det är viktigt att förstå dessa beteenden att förstå beteendet för Azure AD SCIM-klienten. Mer information finns i [vad som händer under etableringen av användare?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Komma igång
Program som stöder SCIM-profilen som beskrivs i den här artikeln kan anslutas till Azure Active Directory med hjälp av funktionen ”icke-galleriprogram” i Azure AD-programgalleriet. När du är ansluten, kör Azure AD en synkroniseringsprocess varje 40 minuter där den frågar programmets SCIM-slutpunkten för tilldelade användare och grupper, och skapar eller ändrar dem enligt tilldelningsinformation.

**Ansluta ett program som stöder SCIM:**

1. Logga in på den [Azure Active Directory-portalen](https://aad.portal.azure.com). 

1. Välj **företagsprogram** i den vänstra rutan. En lista över alla konfigurerade appar visas, inklusive appar som har lagts till från galleriet.

1. Välj **+ nytt program** > **alla** > **icke-galleriprogram**.

1. Ange ett namn för ditt program och välj **Lägg till** att skapa en app-objekt. Den nya appen läggs till i listan över företagsprogram och öppnar dess appskärmen för hantering.
    
   ![][1]
   *Bild 2: Azure AD-programgalleriet*
    
1. På skärmen för appen, Välj **etablering** på den vänstra panelen.
1. I den **etablering läge** menyn och välj **automatisk**.
    
   ![][2]
   *Bild 3: Konfigurera etablering i Azure portal*
    
1. I den **klient-URL** fältet, anger du URL till slutpunkten för programmets SCIM. Exempel: https://api.contoso.com/scim/v2/
1. Om SCIM-slutpunkten kräver en OAuth-ägartoken från en utfärdare än Azure AD kan sedan kopiera den obligatoriska OAuth-ägartoken till det valfria **hemlighet Token** fält. 
1. Välj **Testanslutningen** ha Azure Active Directory som försöker ansluta till SCIM-slutpunkten. Om försöket misslyckas, visas information om felet.  

    >[!NOTE]
    >**Testa anslutning** frågar SCIM-slutpunkten för en användare som inte finns med en slumpmässig GUID som egenskapen matchande som valts i Azure AD-konfigurationen. Förväntade rätt svar är HTTP 200 OK med ett tomt SCIM ListResponse-meddelande. 

1. Om försöker att ansluta till program-lyckas väljer **spara** att spara autentiseringsuppgifter som administratör.
1. I den **mappningar** avsnittet finns det två valbar uppsättningar attributmappningar: en för användarobjekt och en för gruppobjekt. Välj var och en att granska de attribut som synkroniseras från Azure Active Directory till din app. Attribut som har markerats som **matchande** egenskaper som används för att matcha de användare och grupper i din app för uppdateringsåtgärder. Välj **spara** att genomföra ändringarna.

    >[!NOTE]
    >Du kan också inaktivera synkronisering av gruppobjekt genom att inaktivera ”grupper”-mappning. 

1. Under **inställningar**, **omfång** fältet definierar vilka användare och grupper synkroniseras. Välj **synkronisera enbart tilldelade användare och grupper** (rekommenderas) att endast synkronisera användare och grupper som har tilldelats i den **användare och grupper** fliken.
1. När konfigurationen är klar kan du ange den **Etableringsstatus** till **på**.
1. Välj **spara** att starta den Azure AD-etableringstjänsten. 
1. Om synkronisera enbart tilldelade användare och grupper (rekommenderas), måste du markera den **användare och grupper** fliken och tilldela användare eller grupper som du vill synkronisera.

När den inledande synkroniseringen har startat, kan du välja **granskningsloggar** i den vänstra panelen för att övervaka förloppet, som visar alla åtgärder som utförs av etableringstjänsten i din app. Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](check-status-user-account-provisioning.md).

> [!NOTE]
> Den första synkroniseringen tar längre tid att genomföra än senare synkroniseringar som sker ungefär var 40 minut så länge som tjänsten körs. 

## <a name="understanding-the-azure-ad-scim-implementation"></a>Förstå Azure AD SCIM-implementering

Om du skapar ett program som stöder en SCIM 2.0 Användarhantering API är det här avsnittet beskrivs i detalj hur Azure AD SCIM-klienten har implementerats och hur du ska modellera SCIM-protokollet begär felhantering och svar. När du har implementerat din SCIM-slutpunkt kan testa du den genom att följa proceduren som beskrivs i föregående avsnitt.

I den [SCIM 2.0 protokollspecifikation](http://www.simplecloud.info/#Specification), ditt program måste uppfylla följande krav:

* Stöder skapandet av användare och eventuellt också grupper, enligt avsnittet [3.3 av SCIM-protokollet](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Har stöd för ändra användare eller grupper med PATCH-förfrågningar enligt [avsnittet 3.5.2 av SCIM-protokollet](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Har stöd för hämtning av en känd resurs för en användare eller grupp som skapades tidigare, enligt [avsnittet 3.4.1 av SCIM-protokollet](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Har stöd för frågor till användare eller grupper, enligt avsnittet [3.4.2 av SCIM-protokollet](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Som standard hämtas användare av deras `id` och med sina `username` och `externalid`, och grupper tillfrågas av `displayName`.  
* Har stöd för fråga användaren efter ID och efter manager enligt avsnittet 3.4.2 av SCIM-protokollet.  
* Har stöd för frågor till grupper efter ID och efter medlem, enligt avsnittet 3.4.2 av SCIM-protokollet.  
* Accepterar en enda ägartoken för autentisering och auktorisering av Azure AD i ditt program.

Följ dessa riktlinjer när du implementerar en SCIM-slutpunkt för att säkerställa kompatibilitet med Azure AD:

* `id` är en obligatorisk egenskap för alla resurser. Varje svar som returnerar en resurs bör se till att varje resurs har den här egenskapen, förutom för `ListResponse` med noll medlemmar.
* Svar på en begäran om frågefilter/bör alltid vara ett `ListResponse`.
* Grupper är valfritt, men bara stöds om SCIM-implementeringen stöder PATCH-förfrågningar.
* Det är inte nödvändigt att inkludera hela resursen i PATCH-svaret.
* Microsoft Azure AD använder endast följande operatorer:  
     - `eq`
     - `and`
* Kräver inte en skiftlägeskänslig matchning på strukturella element i SCIM i viss PATCH `op` åtgärden värden, enligt definitionen i https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD genererar värdena för 'op' som `Add`, `Replace`, och `Remove`.
* Microsoft Azure AD gör begäranden för att hämta en slumpmässig användare och grupper för att säkerställa att slutpunkten och autentiseringsuppgifterna är giltiga. Det görs också som en del av **Testanslutningen** flow i den [Azure-portalen](https://portal.azure.com). 
* Attributet som resurserna som kan efterfrågas på ska anges som ett matchande attribut till programmet i den [Azure-portalen](https://portal.azure.com). Mer information finns i [anpassa användaren etablering attributmappningar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Användaretablering och avetablering
Följande bild visar meddelandena som Azure Active Directory skickar till SCIM-tjänsten att hantera livscykeln för en användare i programmets identitet store.  

![][4]
*Bild 4: Användaretablering och avetablering sekvens*

### <a name="group-provisioning-and-de-provisioning"></a>Gruppetablering och avetablering
Gruppen etablering och avetablering är valfria. När implementeras och aktiveras följande bild visar meddelandena som Azure AD skickar till en SCIM-tjänst för att hantera livscykeln för en grupp i programmets identitet store.  Meddelandena som skiljer sig från meddelanden om användare på två sätt: 

* Begäranden att hämta grupper anger att attributet medlemmar är som ska undantas från en resurs i svaret på begäran.  
* Förfrågningar att avgöra om ett referensattribut har ett visst värde är begäranden om attributet medlemmar.  

![][5]
*Bild 5: Gruppetablering och avetablering sekvens*

### <a name="scim-protocol-requests-and-responses"></a>SCIM-protokollbegäranden och svar
Det här avsnittet innehåller exempel SCIM begäranden som orsakats av Azure AD SCIM-klienten och exempel förväntade svar. För bästa resultat bör du koda din app för att hantera dessa begäranden i följande format och sända de förväntade svar.

>[!IMPORTANT]
>Information om hur och när Azure AD-tjänst för användaretablering genererar de åtgärder som beskrivs nedan finns i [vad som händer under etableringen av användare?](user-provisioning.md#what-happens-during-provisioning).

- [Användaråtgärder](#user-operations)
  - [Skapa användare](#create-user)
    - [Förfrågan](#request)
    - [Svar](#response)
  - [Hämta användare](#get-user)
    - [Förfrågan](#request-1)
    - [Svar](#response-1)
  - [Hämta användare av frågan](#get-user-by-query)
    - [Förfrågan](#request-2)
    - [Svar](#response-2)
  - [Hämta användare av frågan - resultat](#get-user-by-query---zero-results)
    - [Förfrågan](#request-3)
    - [Svar](#response-3)
  - [Uppdatera användare [med flera värden egenskaper]](#update-user-multi-valued-properties)
    - [Förfrågan](#request-4)
    - [Svar](#response-4)
  - [Uppdatera användare [enkelvärdesattribut egenskaper]](#update-user-single-valued-properties)
    - [Förfrågan](#request-5)
    - [Svar](#response-5)
  - [Ta bort användare](#delete-user)
    - [Förfrågan](#request-6)
    - [Svar](#response-6)
- [Grupp-åtgärder](#group-operations)
  - [Skapa grupp](#create-group)
    - [Förfrågan](#request-7)
    - [Svar](#response-7)
  - [Hämta grupp](#get-group)
    - [Förfrågan](#request-8)
    - [Svar](#response-8)
  - [Hämta grupp av displayName](#get-group-by-displayname)
    - [Förfrågan](#request-9)
    - [Svar](#response-9)
  - [Uppdatera grupp [icke-Medlemsattribut]](#update-group-non-member-attributes)
    - [Förfrågan](#request-10)
    - [Svar](#response-10)
  - [Uppdatera grupp [Lägg till medlemmar]](#update-group-add-members)
    - [Förfrågan](#request-11)
    - [Svar](#response-11)
  - [Uppdatera grupp [ta bort medlemmar]](#update-group-remove-members)
    - [Förfrågan](#request-12)
    - [Svar](#response-12)
  - [Ta bort grupp](#delete-group)
    - [Förfrågan](#request-13)
    - [Svar](#response-13)

### <a name="user-operations"></a>Användaråtgärder

* Användare kan fråga `userName` eller `email[type eq "work"]` attribut.  

#### <a name="create-user"></a>Skapa användare

###### <a name="request"></a>Förfrågan
*POST/användare*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Svar
*HTTP/1.1 201 skapas*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```


#### <a name="get-user"></a>Hämta användare

###### <a name="request-1"></a>Begäran
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Svar
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```
#### <a name="get-user-by-query"></a>Hämta användare av frågan

##### <a name="request-2"></a>Begäran
*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response-2"></a>Svar
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Hämta användare av frågan - resultat

##### <a name="request-3"></a>Begäran
*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response-3"></a>Svar
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Uppdatera användare [med flera värden egenskaper]

##### <a name="request-4"></a>Begäran
*PATCH /Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response-4"></a>Svar
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Uppdatera användare [enkelvärdesattribut egenskaper]

##### <a name="request-5"></a>Begäran
*KORRIGERA/användare/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response-5"></a>Svar
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="delete-user"></a>Ta bort användare

##### <a name="request-6"></a>Begäran
*Ta bort /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Svar
*HTTP/1.1 204 inget innehåll*

### <a name="group-operations"></a>Grupp-åtgärder

* Alltid skall att skapa grupper med en lista med tomma medlemmar.
* Grupper kan fråga den `displayName` attribut.
* Uppdatering av gruppen PATCH-begäran bör ge en *HTTP 204-inget innehåll* i svaret. Returnerar en brödtext med en lista över alla medlemmar är inte lämpligt.
* Det är inte nödvändigt att stödja returnerar alla medlemmar i gruppen.

#### <a name="create-group"></a>Skapa grupp

##### <a name="request-7"></a>Begäran
*POST/groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "members": [],
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response-7"></a>Svar
*HTTP/1.1 201 skapas*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Hämta grupp

##### <a name="request-8"></a>Begäran
*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response-8"></a>Svar
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Hämta grupp av displayName

##### <a name="request-9"></a>Begäran
*/ Groups GET? excludedAttributes = medlemmar & filter = displayName eq ”displayName” HTTP/1.1*

##### <a name="response-9"></a>Svar
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```
#### <a name="update-group-non-member-attributes"></a>Uppdatera grupp [icke-Medlemsattribut]

##### <a name="request-10"></a>Begäran
*KORRIGERA/grupper/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response-10"></a>Svar
*HTTP/1.1 204 inget innehåll*

### <a name="update-group-add-members"></a>Uppdatera grupp [Lägg till medlemmar]

##### <a name="request-11"></a>Begäran
*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-11"></a>Svar
*HTTP/1.1 204 inget innehåll*

#### <a name="update-group-remove-members"></a>Uppdatera grupp [ta bort medlemmar]

##### <a name="request-12"></a>Begäran
*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-12"></a>Svar
*HTTP/1.1 204 inget innehåll*

#### <a name="delete-group"></a>ta bort grupp

##### <a name="request-13"></a>Begäran
*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>Svar
*HTTP/1.1 204 inget innehåll*


## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Att skapa en SCIM-slutpunkten med hjälp av Microsoft CLI-bibliotek
Du kan aktivera automatisk användaretablering för nästan alla program eller en identitet store genom att skapa en SCIM-webbtjänst som har kontakt med Azure Active Directory.

Så fungerar här det:

1. Azure AD tillhandahåller en gemensam språk (CLI) infrastrukturbibliotek med namnet Microsoft.SystemForCrossDomainIdentityManagement som ingår i den kod som exempel beskrivs nedan. Systemintegrerare och utvecklare kan använda det här biblioteket för att skapa och distribuera en SCIM-baserade webbtjänstslutpunkt som kan ansluta Azure AD till identitetsarkiv för alla program.
2. Mappningar implementeras i webbtjänsten för att mappa det standardiserade användarschemat till användarschemat och protokoll som krävs av programmet. 
3. Slutpunkts-URL är registrerad i Azure AD som en del av ett anpassat program i programgalleriet.
4. Användare och grupper har tilldelats det här programmet i Azure AD. Vid tilldelning, är de lägger till en kö som ska synkroniseras till målprogrammet. Hantering av kön synkroniseringsprocessen körs varje 40 minuter.

### <a name="code-samples"></a>Kodexempel
Att göra den här processen enklare, [kodexempel](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) har angett, vilket skapar en SCIM web service-slutpunkt och visa Automatisk etablering. I exemplet är av en leverantör som underhåller en fil med rader med kommaavgränsade värden som representerar användare och grupper.    

**Krav**

* Visual Studio 2013 eller senare
* [Azure SDK för .NET](https://azure.microsoft.com/downloads/)
* Windows dator som har stöd för ASP.NET framework 4.5 som ska användas som SCIM-slutpunkt. Den här datorn måste vara tillgänglig från molnet.
* [En Azure-prenumeration med en utvärderingsversion eller licensierad version av Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Komma igång
Det enklaste sättet att implementera en SCIM-slutpunkt som kan ta emot etableringsbegäranden från Azure AD är att skapa och distribuera kodexemplet som visar de etablerade användarna till en fil med kommaavgränsade värden (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Skapa en exempel SCIM-slutpunkt

1. Ladda ned koden exempelpaketet på [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Packa upp paketet och placera den på din Windows-dator på en plats, till exempel C:\AzureAD-BYOA-Provisioning-Samples\.
1. Starta FileProvisioning\Host\FileProvisioningService.csproj-projekt i Visual Studio i den här mappen.
1. Välj **verktyg** > **NuGet-Pakethanteraren** > **Pakethanterarkonsolen**, och kör följande kommandon för det FileProvisioningService-projektet för att matcha referenserna lösningen:

   ```powershell
    Update-Package -Reinstall
   ```

1. Skapa FileProvisioningService-projektet.
1. Starta Kommandotolken programmet i Windows (som administratör) och använda den **cd** kommando för att ändra katalogen till din **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**mapp.
1. Kör följande kommando ersätter `<ip-address>` med IP-adress eller domännamn namnet på den Windows-datorn:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. I Windows under **Windows-inställningar** > **nätverk och Internet-inställningar**väljer den **Windows-brandväggen**  >   **Avancerade inställningar**, och skapa en **inkommande regel** som tillåter ingående åtkomst till port 9000.
1. Om Windows-dator bakom en router kan måste routern konfigureras för att köra Network Access Translation mellan dess port 9000 som exponeras för internet och port 9000 på Windows-dator. Den här konfigurationen krävs för Azure AD för att få åtkomst till den här slutpunkten i molnet.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Registrerar exempel SCIM slutpunkten i Azure AD

1. Logga in på den [Azure Active Directory-portalen](https://aad.portal.azure.com). 

1. Välj **företagsprogram** i den vänstra rutan. En lista över alla konfigurerade appar visas, inklusive appar som har lagts till från galleriet.

1. Välj **+ nytt program** > **alla** > **icke-galleriprogram**.

1. Ange ett namn för ditt program och välj **Lägg till** att skapa en app-objekt. Det programobjekt som skapas är avsedd att representera målappen du skulle etablering för och implementera enkel inloggning för och inte bara SCIM-slutpunkten.

1. På skärmen för appen, Välj **etablering** på den vänstra panelen.

1. I den **etablering läge** menyn och välj **automatisk**.
    
   ![][2]
   *Bild 6: Konfigurera etablering i Azure portal*
    
1. I den **klient-URL** fältet, anger du URL: en och en port för din SCIM-slutpunkt som visas på internet. Transaktionen är något som liknar http://testmachine.contoso.com:9000 eller http://\< ip-adress >: 9000 / där \< ip-adress > är internet exponerade IP adress. 

1. Om SCIM-slutpunkten kräver en OAuth-ägartoken från en utfärdare än Azure AD kan sedan kopiera den obligatoriska OAuth-ägartoken till det valfria **hemlighet Token** fält. 
1. Välj **Testanslutningen** ha Azure Active Directory som försöker ansluta till SCIM-slutpunkten. Om försöket misslyckas, visas information om felet.  

    >[!NOTE]
    >**Testa anslutning** frågar SCIM-slutpunkten för en användare som inte finns med en slumpmässig GUID som egenskapen matchande som valts i Azure AD-konfigurationen. Förväntade rätt svar är HTTP 200 OK med ett tomt SCIM ListResponse-meddelande

1. Om försöker att ansluta till program-lyckas väljer **spara** att spara autentiseringsuppgifter som administratör.

1. I den **mappningar** avsnittet finns det två valbar uppsättningar attributmappningar: en för användarobjekt och en för gruppobjekt. Välj var och en att granska de attribut som synkroniseras från Azure Active Directory till din app. Attribut som har markerats som **matchande** egenskaper som används för att matcha de användare och grupper i din app för uppdateringsåtgärder. Välj **spara** att genomföra ändringarna.

1. Under **inställningar**, **omfång** fältet definierar vilka användare som är och eller grupper är synkroniserade. Välj **”synkronisera enbart tilldelade användare och grupper** (rekommenderas) att endast synkronisera användare och grupper som har tilldelats i den **användare och grupper** fliken.

1. När konfigurationen är klar kan du ange den **Etableringsstatus** till **på**.

1. Välj **spara** att starta den Azure AD-etableringstjänsten. 

1. Om synkronisera enbart tilldelade användare och grupper (rekommenderas), måste du markera den **användare och grupper** fliken och tilldela användare eller grupper som du vill synkronisera.

När den inledande synkroniseringen har startat, kan du välja **granskningsloggar** i den vänstra panelen för att övervaka förloppet, som visar alla åtgärder som utförs av etableringstjänsten i din app. Mer information om hur du läser den Azure AD etablering loggar finns i [rapportering om automatisk användarkontoetablering](check-status-user-account-provisioning.md).

Det sista steget i att verifiera exemplet är att öppna filen TargetFile.csv i mappen \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug på din Windows-dator. När etableringen har körts, innehåller den här filen information om alla tilldelade och etablerat användare och grupper.

### <a name="development-libraries"></a>Utvecklingsbibliotek
För att utveckla dina egna webbtjänst som överensstämmer med SCIM-specifikationen först du med att bekanta dig med följande bibliotek som tillhandahålls av Microsoft för att hjälpa dig att påskynda utvecklingen: 

- Vanliga språk infrastruktur (CLI) bibliotek erbjuds för användning med språk baserat på den infrastrukturen, till exempel C#. En av dessa bibliotek, Microsoft.SystemForCrossDomainIdentityManagement.Service, deklarerar ett gränssnitt, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, som visas i följande bild. En utvecklare som använder biblioteken skulle implementera gränssnittet med en klass som kan hänvisas till, med det allmänna skyddet, som en provider. Biblioteken kan utvecklare distribuera en webbtjänst som överensstämmer med SCIM-specifikationen. Webbtjänsten kan antingen finnas i Internet Information Services eller valfri körbara CLI-sammansättningen. Begäran översätts till anrop till leverantörens metoder som skulle programmeras av utvecklaren att fungera på vissa identitetsarkiv.
  
   ![][3]
  
- [Express route-hanterare](https://expressjs.com/guide/routing.html) tillgängliga för parsning av node.js begäran objekt som representerar anrop (enligt specifikationen SCIM), görs till en node.js-webbtjänst.   

### <a name="building-a-custom-scim-endpoint"></a>Att skapa en anpassad SCIM-slutpunkt
Utvecklare som använder de CLI-biblioteken kan ha sina tjänster i alla körbara CLI-sammansättningen eller i Internet Information Services. Här är exempelkod som värd för en tjänst i en körbar sammansättning på adressen http://localhost:9000: 

   ```csharp
    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }
   ```

Den här tjänsten måste ha en HTTP-adress och server certifikat för serverautentisering som rotcertifikatutfärdaren är en av följande namn: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Ett certifikat för serverautentisering kan bindas till en port på en Windows-värd med hjälp av verktyget network shell: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Här är värdet som angetts för argumentet certhash tumavtrycket för certifikatet, medan värdet som angetts för argumentet appid är ett valfritt globalt unikt ID.  

Ska vara värd för tjänsten i Internet Information Services, skulle en utvecklare skapa en CLI kod bibliotekssammansättning med en klass med namnet Start i standardnamnområdet av sammansättningen.  Här är ett exempel på sådana en klass: 

   ```csharp
    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }
   ```

### <a name="handling-endpoint-authentication"></a>Hantering av slutpunktsautentisering
Begäranden från Azure Active Directory omfattar en OAuth 2.0-ägartoken.   Alla tjänster som tar emot begäran ska autentisera utfärdaren som Azure Active Directory för den förväntade Azure Active Directory-klienten för åtkomst till Azure Active Directory Graph-webbtjänsten.  I token utfärdaren identifieras med ett iss-anspråk som ”iss” ”:"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/ ".  I det här exemplet basadressen för anspråksvärdet, https://sts.windows.net , identifierar Azure Active Directory som utfärdaren, medan relativt adressen segment, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, är en unik identifierare för Azure Active Directory-klient för vilket token utfärdats.  Om token har utfärdats för att komma åt Azure Active Directory Graph-webbtjänsten ska identifierare för tjänsten, 00000002-0000-0000-c000-000000000000, i värdet för den token aud anspråk.  Var och en av de program som är registrerade i en enda klient kan få samma `iss` anspråk med SCIM-begäranden.

Utvecklare som använder CLI-bibliotek som tillhandahålls av Microsoft för att skapa en SCIM-tjänst kan autentisera begäranden från Azure Active Directory med hjälp av Microsoft.Owin.Security.ActiveDirectory-paketet genom att följa dessa steg: 

1. I en provider, implementerar du egenskapen Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior genom att låta den returnera en metod som anropas när tjänsten startas: 

   ```csharp
     public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
     {
       get
       {
         return this.OnServiceStartup;
       }
     }

     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
       System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
     {
     }
   ```

2. Lägg till följande kod i metoden har varje begäran till någon av tjänstslutpunkter autentiserad som försetts med en token utfärdad av Azure Active Directory för en angiven klient för åtkomst till Azure AD Graph-webbtjänsten: 

   ```csharp
     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
       System.Web.Http.HttpConfiguration HttpConfiguration configuration)
     {
       // IFilter is defined in System.Web.Http.dll.  
       System.Web.Http.Filters.IFilter authorizationFilter = 
         new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

       // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
       // System.IdentityModel.Token.Jwt.dll.
       SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
         new TokenValidationParameters()
         {
           ValidAudience = "00000002-0000-0000-c000-000000000000"
         };

       // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
       // Microsoft.Owin.Security.ActiveDirectory.dll
       Microsoft.Owin.Security.ActiveDirectory.
       WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
         new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
         TokenValidationParameters = tokenValidationParameters,
         Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                       // identifier for this one.  
       };

       applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
     }
   ```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Hantering av etablering och borttagning av användare

1. Azure Active Directory frågar tjänsten för en användare med ett externalId-attributvärde som matchar mailNickname-attributvärdet för en användare i Azure AD. Frågan uttrycks som en Hypertext Transfer Protocol (HTTP)-begäran, till exempel det här exemplet där jyoung är ett exempel på en mailNickname för en användare i Azure Active Directory.

    >[!NOTE]
    > Detta är bara ett exempel. Inte alla användare har en mailNickname-attributet och en användare har värdet får inte vara unika i katalogen. Attributet som används för att matcha (som i det här fallet är externalId) är också kan konfigureras i den [Azure AD-attributmappningar](customize-application-attributes.md).

   ```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ```
   Om tjänsten har skapats med hjälp av CLI-bibliotek som tillhandahålls av Microsoft för att implementera SCIM-tjänster, översätts förfrågan till ett anrop till metoden fråga i den tjänstleverantör.  Här är signaturen för metoden: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ```
   Här är definitionen av gränssnittet Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 
   ```csharp
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }
   ```

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   Om tjänsten har skapats med Common Language infrastruktur-bibliotek som tillhandahålls av Microsoft för att implementera SCIM-tjänster, översätts förfrågan till ett anrop till metoden fråga i den tjänstleverantör.  Här är signaturen för metoden: 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Här är definitionen av gränssnittet Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 

   ```csharp
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
         { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
       system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
       { get; }
       System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
       { get; }
       string SchemaIdentifier 
       { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
     {
         Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
           { get; set; }
         string AttributePath 
           { get; } 
         Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
           { get; }
         string ComparisonValue 
           { get; }
     }

     public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
     {
         Equals
     }
   ```

   I följande exempel av en fråga för en användare med ett angivet värde för attributet externalId och är värden för argumenten som skickas till Query-metoden: 
   * parameters.AlternateFilters.Count: 1
   * parametrar. AlternateFilters.ElementAt(0). AttributePath: ”externalId”
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * parametrar. AlternateFilter.ElementAt(0). ComparisonValue: ”jyoung”
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. Om svaret på en fråga till webbtjänsten för en användare med ett externalId-attributvärde som överensstämmer med mailNickname-attributvärdet för en användare inte returnerar några användare, sedan Azure Active Directory-begäranden som tjänsten etablerar en användare som motsvarar det i Azure Active Directory.  Här är ett exempel på en sådan begäran: 

   ```
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/scim+json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
   ```
   CLI-bibliotek som tillhandahålls av Microsoft för att implementera SCIM tjänster skulle omvandla begäran till ett anrop till metoden skapa av tjänstens-providern.  Skapa-metoden har den här signaturen: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
   ```
   Värdet för argumentet resurs är en instans av Microsoft.SystemForCrossDomainIdentityManagement i en begäran att etablera en användare. Core2EnterpriseUser klassen, som definieras i Microsoft.SystemForCrossDomainIdentityManagement.Schemas-biblioteket.  Om begäran att etablera användaren lyckas, förväntas implementeringen av metoden för att returnera en instans av Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser klass med värdet för egenskapen ID angetts till den unika identifieraren för den nyligen etablerade användaren.  

3. Om du vill uppdatera en användare som är kända i en identitetsarkiv fronted genom en SCIM fortsätter Azure Active Directory genom att begära det aktuella tillståndet för den användaren från tjänsten med en begäran som: 
   ```
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ```
   I en tjänst som skapats med hjälp av CLI-bibliotek som tillhandahålls av Microsoft för att implementera SCIM-tjänster, översätts förfrågan till ett anrop till metoden hämta av tjänstens-providern.  Här är signaturen för hämta-metoden: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
   ```
   I exemplet med en begäran att hämta det aktuella tillståndet för en användare, är värdena för egenskaperna för objektet som tillhandahålls som värde för argumentet parametrar följande: 
  
   * Identifierare: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”

4. Om det är ett referensattribut som ska uppdateras, frågar Azure Active Directory service för att avgöra om det aktuella värdet för referensattributet i Identitetslagret fronted av tjänsten redan matchar värdet för attributet i Azure Active Katalogen. För användare är det enda attributet som efterfrågas det aktuella värdet på det här sättet manager-attribut. Här är ett exempel på en begäran för att avgöra om attributet manager för en viss användare-objektet har ett visst värde: 

   Om tjänsten har skapats med hjälp av CLI-bibliotek som tillhandahålls av Microsoft för att implementera SCIM-tjänster, översätts förfrågan till ett anrop till metoden fråga i den tjänstleverantör. Värdet för egenskaperna för objektet som tillhandahålls som värde för argumentet parametrar är följande: 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: ”ID”
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * parametrar. AlternateFilters.ElementAt(y). AttributePath: ”manager”
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): ”ID”
   * parametrar. SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”

   Här är värdet för indexet x kan vara 0 och värdet för y index kan vara 1, eller värdet för x kan vara 1 och värdet för y kan vara 0, beroende på ordningen på uttryck av filter-frågeparameter.   

5. Här är ett exempel på en begäran från Azure Active Directory till en SCIM-tjänsten för att uppdatera en användare: 
   ```
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/scim+json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```
   Microsoft CLI-bibliotek för att implementera SCIM tjänster skulle omvandla begäran till ett anrop till metoden Update av tjänstens-providern. Här är signaturen för metoden Update: 
   ```csharp
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}
   ```

   Om tjänsten har skapats med Common Language infrastruktur-bibliotek som tillhandahålls av Microsoft för att implementera SCIM-tjänster, översätts förfrågan till ett anrop till metoden fråga i den tjänstleverantör. Värdet för egenskaperna för objektet som tillhandahålls som värde för argumentet parametrar är följande: 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: ”ID”
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
* parametrar. AlternateFilters.ElementAt(y). AttributePath: ”manager”
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): ”ID”
* parametrar. SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”

  Här är värdet för indexet x kan vara 0 och värdet för y index kan vara 1, eller värdet för x kan vara 1 och värdet för y kan vara 0, beroende på ordningen på uttryck av filter-frågeparameter.   

1. Här är ett exempel på en begäran från Azure Active Directory till en SCIM-tjänsten för att uppdatera en användare: 

   ```
     PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
     {
       "schemas": 
       [
         "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
       "Operations":
       [
         {
           "op":"Add",
           "path":"manager",
           "value":
             [
               {
                 "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                 "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```

   Microsoft Common Language infrastruktur-bibliotek för att implementera SCIM tjänster skulle omvandla begäran till ett anrop till metoden Update av tjänstens-providern. Här är signaturen för metoden Update: 

   ```csharp
     // System.Threading.Tasks.Tasks and 
     // System.Collections.Generic.IReadOnlyCollection<T>
     // are defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
     // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

     System.Threading.Tasks.Task Update(
       Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
       string correlationIdentifier);

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
     {
     Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
       PatchRequest 
         { get; set; }
     Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
       ResourceIdentifier 
         { get; set; }        
     }

     public class PatchRequest2: 
       Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
     {
     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
         Operations
         { get;}

     public void AddOperation(
       Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
     }

     public class PatchOperation
     {
     public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
       Name
       { get; set; }

     public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
       Path
       { get; set; }

     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
       { get; }

     public void AddValue(
       Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
     }

     public enum OperationName
     {
       Add,
       Remove,
       Replace
     }

     public interface IPath
     {
       string AttributePath { get; }
       System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
       Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
     }

     public class OperationValue
     {
       public string Reference
       { get; set; }

       public string Value
       { get; set; }
     }
   ```

    I exemplet med en begäran om att uppdatera en användare, har det angivna objektet som värde för argumentet patch egenskapsvärdena: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”
   * (PatchRequest som PatchRequest2). Operations.Count: 1
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
   * (PatchRequest som PatchRequest2). Operations.ElementAt(0). Path.AttributePath: ”manager”
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
   * (PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referens: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

1. Om du vill avetablera en användare från en identity-butik fronted av en SCIM-tjänst, skickar Azure AD en begäran som: 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Om tjänsten har skapats med Common Language infrastruktur-bibliotek som tillhandahålls av Microsoft för att implementera SCIM-tjänster, översätts förfrågan till ett anrop till metoden Delete av tjänstens-providern.   Metoden har den här signaturen: 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   Objektet som tillhandahålls som värde för argumentet resourceIdentifier har de här egenskaperna i exemplet med en begäran om att avetablera en användare: 

1. Om du vill avetablera en användare från en identity-butik fronted av en SCIM-tjänst, skickar Azure AD en begäran som: 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   Om tjänsten har skapats med hjälp av CLI-bibliotek som tillhandahålls av Microsoft för att implementera SCIM-tjänster, översätts förfrågan till ett anrop till metoden Delete av tjänstens-providern.   Metoden har den här signaturen: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   Objektet som tillhandahålls som värde för argumentet resourceIdentifier har de här egenskaperna i exemplet med en begäran om att avetablera en användare: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”

## <a name="user-and-group-schema-reference"></a>Schemareferens för användare och grupper
Azure Active Directory kan etablera två typer av resurser till SCIM-webbtjänster.  Dessa typer av resurser är användare och grupper.  

Användarresurser identifieras av schema-ID `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, som ingår i den här protokollspecifikation: https://tools.ietf.org/html/rfc7643.  Standardmappningen attribut för användare i Azure Active Directory attributen för användarresurser tillhandahålls i tabell 1.  

Gruppera resurser identifieras av schema-ID `urn:ietf:params:scim:schemas:core:2.0:Group`. Tabell 2 visar standardmappningen attribut för grupper i Azure Active Directory attributen för gruppera resurser.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabell 1: Attributmappning för standard-användare

| Azure Active Directory-användare | ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User” |
| --- | --- |
| IsSoftDeleted |aktiv |
| displayName |displayName |
| Fax TelephoneNumber |phoneNumbers [typ eq ”fax”] .value |
| givenName |name.givenName |
| jobTitle |rubrik |
| e-post |e-postmeddelanden [typ eq ”arbete pågår”] .value |
| mailNickname |externalId |
| ansvarig |ansvarig |
| mobila |phoneNumbers [typ eq ”mobil”] .value |
| objekt-ID |ID |
| Postnummer |adresser typ eq ”arbete pågår” .postalCode |
| Proxy-adresser |e-postmeddelande [Ange eq ”annan”]. Värde |
| fysiska-leverans – OfficeName |adresser [Ange eq ”annan”]. Formaterad |
| streetAddress |adresser typ eq ”arbete pågår” .streetAddress |
| Efternamn |name.familyName |
| Telefonnummer |phoneNumbers [typ eq ”arbete pågår”] .value |
| användaren huvudkontot |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tabell 2: Attributmappning för standard-grupp

| Azure Active Directory-grupp | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |externalId |
| e-post |e-postmeddelanden [typ eq ”arbete pågår”] .value |
| mailNickname |displayName |
| medlemmar |medlemmar |
| objekt-ID |ID |
| proxyAddresses |e-postmeddelande [Ange eq ”annan”]. Värde |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Tillåt IP-adresser som används av Azure AD etableringstjänsten för att göra SCIM-begäranden
Vissa appar tillåter inkommande trafik till appen. För den Azure AD-etableringtjänsten ska fungera som förväntat, måste IP-adresser som används tillåtas. En lista över IP-adresser för varje tjänst-taggen /-region, finns i JSON-fil – [Azure IP-intervall och Tjänsttaggar – offentligt moln](https://www.microsoft.com/download/details.aspx?id=56519). Du kan ladda ned och programmet dessa IP-adresser i brandväggen efter behov. De reserverade IP-adressintervall för Azure AD-etablering finns under ”AzureActiveDirectoryDomainServices”.
 

## <a name="related-articles"></a>Relaterade artiklar
* [Automatisera användaren etablering/avetablering för SaaS-appar](user-provisioning.md)
* [Anpassa attributmappningar för etableringen av användare](customize-application-attributes.md)
* [Skriva uttryck för attributmappningar](functions-for-customizing-application-data.md)
* [Omfångsfilter för etableringen av användare](define-conditional-rules-for-provisioning-user-accounts.md)
* [Kontoetableringsmeddelanden](user-provisioning.md)
* [Lista över guider om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
