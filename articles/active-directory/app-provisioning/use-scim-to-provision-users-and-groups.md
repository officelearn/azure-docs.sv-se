---
title: Utveckla en SCIM-slutpunkt för användaretablering till appar från Azure AD
description: System för SCIM (Cross-Domain Identity Management) standardiserar automatisk användaretablering. Lär dig att utveckla en SCIM-slutpunkt, integrera ditt SCIM-API med Azure Active Directory och börja automatisera etablering av användare och grupper i dina molnprogram.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0507989ec25db595a85b89f15d8ff7d056a970f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297683"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Skapa en SCIM-slutpunkt och konfigurera användaretablering med Azure Active Directory (Azure AD)

Som programutvecklare kan du använda SCIM-användarhanterings-API (System for Cross-Domain Identity Management) för att möjliggöra automatisk etablering av användare och grupper mellan ditt program och Azure AD. I den här artikeln beskrivs hur du skapar en SCIM-slutpunkt och integreras med Azure AD-etableringstjänsten. SCIM-specifikationen innehåller ett gemensamt användarschema för etablering. När SCIM används tillsammans med federationsstandarder som SAML eller OpenID Connect ger de administratörer en heltäckande, standardbaserad lösning för åtkomsthantering.

SCIM är en standardiserad definition av två slutpunkter: en /Users-slutpunkt och en /Groups-slutpunkt. Den använder vanliga REST-verb för att skapa, uppdatera och ta bort objekt, och ett fördefinierat schema för vanliga attribut som gruppnamn, användarnamn, förnamn, efternamn och e-post. Appar som erbjuder ett SCIM 2.0 REST API kan minska eller eliminera smärtan av att arbeta med ett eget användarhanterings-API. Alla kompatibla SCIM-klienter vet till exempel hur man gör ett HTTP-INLÄGG för ett JSON-objekt till slutpunkten /Användare för att skapa en ny användarpost. I stället för att behöva ett något annorlunda API för samma grundläggande åtgärder kan appar som överensstämmer med SCIM-standarden omedelbart dra nytta av befintliga klienter, verktyg och kod. 

![Etablering från Azure AD till en app med SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Standard-api:erna för användarobjekt och vila för hantering som definieras i SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) gör det enklare att integrera identitetsleverantörer och appar med varandra. Programutvecklare som bygger en SCIM-slutpunkt kan integreras med alla SCIM-kompatibla klienter utan att behöva göra ett anpassat arbete.

Automatisera etablering till ett program kräver att du skapar och integrerar en SCIM-slutpunkt med Azure AD SCIM-klienten. Utför följande steg för att börja etablera användare och grupper i ditt program. 
    
  * **[Steg 1: Utforma användar- och gruppschemat.](#step-1-design-your-user-and-group-schema)** Identifiera de objekt och attribut som ditt program behöver och bestäm hur de mappas till användar- och gruppschemat som stöds av Azure AD SCIM-implementeringen.

  * **[Steg 2: Förstå Azure AD SCIM-implementeringen.](#step-2-understand-the-azure-ad-scim-implementation)** Förstå hur Azure AD SCIM-klienten implementeras och modellera hantering och svar för SCIM-protokollbegäran.

  * **[Steg 3: Skapa en SCIM-slutpunkt.](#step-3-build-a-scim-endpoint)** En slutpunkt måste vara SCIM 2.0-kompatibel för att integreras med Azure AD-etableringstjänsten. Som ett alternativ kan du använda CLI-bibliotek (Microsoft Common Language Infrastructure) och kodexempel för att skapa slutpunkten. Dessa prover är endast till för referens och testning. Vi rekommenderar att du inte kodar din produktionsapp för att vara beroende av dem.

  * **[Steg 4: Integrera din SCIM-slutpunkt med Azure AD SCIM-klienten.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Om din organisation använder ett program från tredje part som implementerar profilen för SCIM 2.0 som Azure AD stöder, kan du börja automatisera både etablering och avetablering av användare och grupper direkt.

  * **[Steg 5: Publicera ditt program i Azure AD-programgalleriet.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Gör det enkelt för kunder att upptäcka ditt program och enkelt konfigurera etablering. 

![Steg för att integrera en SCIM-slutpunkt med Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Steg 1: Utforma användar- och gruppschema

Varje program kräver olika attribut för att skapa en användare eller grupp. Starta integreringen genom att identifiera de objekt (användare, grupper) och attribut (namn, chef, befattning osv.) som programmet kräver. SCIM-standarden definierar ett schema för hantering av användare och grupper. Det centrala användarschemat kräver endast tre attribut: **id** (tjänsteproviderdefinierad identifierare), **externalId** (klientdefinierad identifierare) och **meta** (skrivskyddade metadata som underhålls av tjänsteleverantören). Alla andra attribut är valfria. Förutom det grundläggande användarschemat definierar SCIM-standarden ett företagsanvändartillägg och en modell för att utöka användarschemat så att det uppfyller programmets behov. Om ditt program till exempel kräver en användares chef kan du använda företagets användarschema för att samla in användarens chef och det grundläggande schemat för att samla in användarens e-post. Så här utformar du schemat nedan:
  1. Lista de attribut som ditt program kräver. Det kan vara bra att dela upp dina krav i de attribut som behövs för autentisering (t.ex. loginName och e-post), attribut som behövs för att hantera livscykeln för användaren (t.ex. status / aktiv), och andra attribut som behövs för att ditt program ska fungera (t.ex. chef, tagg).
  2. Kontrollera om dessa attribut redan har definierats i det grundläggande användarschemat eller företagets användarschema. Om några attribut som du behöver och inte omfattas av de grundläggande användarscheman eller företagets användarscheman måste du definiera ett tillägg till användarschemat som täcker de attribut du behöver. I exemplet nedan har vi lagt till ett tillägg till användaren för att tillåta etablering av en "tagg" för en användare. Det är bäst att börja med bara de centrala och företagsanvändarscheman och expandera ut till ytterligare anpassade scheman senare.  
  3. Mappa SCIM-attributen till användarattributen i Azure AD. Om ett av attributen som du har definierat i scim-slutpunkten inte har en tydlig motsvarighet i Azure AD-användarschemat, finns det en god chans att data inte lagras på användarobjektet alls på de flesta klienter. Överväg om det här attributet kan vara valfritt för att skapa en användare. Om attributet är viktigt för att ditt program ska fungera, vägleda klientadministratören att utöka deras schema eller använda ett tilläggsattribut som visas nedan för egenskapen "taggar".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabell 1: Beskriva de attribut som du behöver 
| Steg 1: Bestäm attribut som appen kräver| Steg 2: Mappa appkrav till SCIM-standard| Steg 3: Mappa SCIM-attribut till Azure AD-attributen|
|--|--|--|
|loginName (loginName)|userName|userPrincipalName|
|firstName|name.givenName|förnamn|
|lastName|name.lastName|lastName|
|workMail (arbeteMail)|E-postmeddelanden[typ eq "arbete"].värde|Mail|
|manager|manager|manager|
|tagg|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|aktiv|isSoftDeleted (beräknat värde lagras inte på användaren)|

Schemat som definieras ovan skulle representeras med hjälp av Json nyttolast nedan. Observera att förutom de attribut som krävs för programmet innehåller JSON-representationen de obligatoriska attributen "id", "externalId" och "meta".

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>Tabell 2: Mappning av standardanvändarattribut
Du kan sedan använda tabellen nedan för att förstå hur de attribut som ditt program kräver kan mappas till ett attribut i Azure AD och SCIM RFC. Du kan [anpassa](customize-application-attributes.md) hur attribut mappas mellan Azure AD och din SCIM-slutpunkt. Observera att du inte behöver stödja både användare och grupper eller alla attribut som visas nedan. De är en referens för hur attribut i Azure AD ofta mappas till egenskaper i SCIM-protokollet. 

| Azure Active Directory-användare | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktiv |
|avdelning|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|Employeeid|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Faksimil-telefonAntal |phoneNumbers[typ eq "fax"].värde |
| förnamn |name.givenName |
| jobbTitle |title |
| e-post |e-postmeddelanden[typ eq "arbete"].värde |
| mailNickname (mailNickname) |externt |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| mobil |phoneNumbers[typ eq "mobil"].värde |
| Postnummer |adresser[typ eq "arbete"].postalCode |
| proxyadresser |e-postmeddelanden[typ eq "andra"]. Värde |
| fysiskt leverans-OfficeName |adresser[typ eq "andra"]. Formaterad |
| streetAddress (streetAddress) |adresser[typ eq "work"].streetAddress |
| surname |name.familyName |
| telefonnummer |phoneNumbers[typ eq "arbete"].värde |
| användare-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabell 3: Standardgruppattributmappning

| Azure Active Directory-grupp | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| e-post |e-postmeddelanden[typ eq "arbete"].värde |
| mailNickname (mailNickname) |displayName |
| medlemmar |medlemmar |
| Objectid |externt |
| proxyAddresses |e-postmeddelanden[typ eq "andra"]. Värde |

Det finns flera slutpunkter definierade i SCIM RFC. Du kan komma igång med /User-slutpunkten och sedan expandera därifrån. Slutpunkten /Schemas är användbar när du använder anpassade attribut eller om schemat ändras ofta. Det gör det möjligt för en klient att hämta det senaste schemat automatiskt. Slutpunkten /Bulk är särskilt användbar när du stöder grupper. I tabellen nedan beskrivs de olika slutpunkter som definierats i SCIM-standarden. Slutpunkten /Schemas är användbar när du använder anpassade attribut eller om schemat ändras ofta. Det gör det möjligt för en klient att hämta det mest uppdaterade schemat automatiskt. Slutpunkten /Bulk är särskilt användbar när du stöder grupper. I tabellen nedan beskrivs de olika slutpunkter som definierats i SCIM-standarden. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabell 4: Bestäm de slutpunkter som du vill utveckla
|Slutpunkt|BESKRIVNING|
|--|--|
|/Användare|Utför CRUD-åtgärder på ett användarobjekt.|
|/Grupp|Utför CRUD-åtgärder på ett gruppobjekt.|
|/ServiceProviderConfig|Innehåller information om funktionerna i SCIM-standarden som stöds, till exempel de resurser som stöds och autentiseringsmetoden.|
|/ResourceTypes|Anger metadata för varje resurs|
|/Scheman|Den uppsättning attribut som stöds av varje klient och tjänsteleverantör kan variera. En tjänsteleverantör kan innehålla "namn", "titel" och "e-post", medan en annan tjänsteleverantör använder "namn", "titel" och "phoneNumbers". Schemans slutpunkt gör det möjligt att identifiera de attribut som stöds.|
|/Bulk|Med massåtgärder kan du utföra åtgärder på en stor samling resursobjekt i en enda åtgärd (t.ex. uppdatera medlemskap för en stor grupp).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Steg 2: Förstå Azure AD SCIM-implementeringen
> [!IMPORTANT]
> Beteendet för Azure AD SCIM-implementeringen uppdaterades senast den 18 december 2018. Information om vad som har ändrats finns i [SCIM 2.0-protokollefterlevnad för azure AD-tjänsten för användaretablering](application-provisioning-config-problem-scim-compatibility.md).

Om du skapar ett program som stöder ett SCIM 2.0-API för användarhantering beskriver det här avsnittet i detalj hur Azure AD SCIM-klienten implementeras. Den visar också hur du modellerar din SCIM-protokollbegäran hantering och svar. När du har implementerat SCIM-slutpunkten kan du testa den genom att följa proceduren som beskrivs i föregående avsnitt.

Inom [SCIM 2.0-protokollspecifikationen](http://www.simplecloud.info/#Specification)måste ditt program uppfylla följande krav:

* Stöder att skapa användare, och eventuellt även grupper, enligt avsnitt [3.3 i SCIM-protokollet](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Stöder ändring av användare eller grupper med PATCH-begäranden enligt [avsnitt 3.5.2 i SCIM-protokollet](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Stöder hämtning av en känd resurs för en användare eller grupp som skapats tidigare, enligt [avsnitt 3.4.1 i SCIM-protokollet](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Stöder att fråga användare eller grupper enligt avsnitt [3.4.2 i SCIM-protokollet](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Som standard hämtas användare `id` av sina och `username` `externalid`frågor av sina och `displayName`, och grupper efterfrågas av .  
* Stöder att fråga användare efter ID och hanterare enligt avsnitt 3.4.2 i SCIM-protokollet.  
* Stöder frågegrupper efter ID och medlem enligt avsnitt 3.4.2 i SCIM-protokollet.  
* Accepterar en enda innehavartoken för autentisering och auktorisering av Azure AD till ditt program.

Följ dessa allmänna riktlinjer när du implementerar en SCIM-slutpunkt för att säkerställa kompatibilitet med Azure AD:

* `id`är en obligatorisk egenskap för alla resurser. Varje svar som returnerar en resurs bör se `ListResponse` till att varje resurs har den här egenskapen, förutom med noll medlemmar.
* Svar på en fråga/filterbegäran `ListResponse`bör alltid vara en .
* Grupper är valfria, men stöds bara om SCIM-implementeringen stöder PATCH-begäranden.
* Det är inte nödvändigt att inkludera hela resursen i PATCH-svaret.
* Microsoft Azure AD använder endast följande operatorer:  
    - `eq`
    - `and`
* Kräver inte en skiftlägeskänslig matchning på strukturella element `op` i SCIM, https://tools.ietf.org/html/rfc7644#section-3.5.2särskilt PATCH-åtgärdsvärden, enligt definitionen i . Azure AD avger värdena för `Add` `Replace`op `Remove`som , och .
* Microsoft Azure AD gör begäranden om att hämta en slumpmässig användare och grupp för att säkerställa att slutpunkten och autentiseringsuppgifterna är giltiga. Det görs också som en del av **Test Connection-flödet** i [Azure-portalen](https://portal.azure.com). 
* Attributet som resurserna kan efterfrågas på bör anges som ett matchande attribut för programmet i [Azure-portalen](https://portal.azure.com). Mer information finns i [Anpassa attributmappningar för användaretablering](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>Tillhandahållande och avetablering av användare

Följande bild visar de meddelanden som Azure Active Directory skickar till en SCIM-tjänst för att hantera livscykeln för en användare i programmets identitetsarkiv.  

![Visar sekvensen för etablera och avetablering av användare](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Sekvens för etablering och avetablering av användare*

### <a name="group-provisioning-and-deprovisioning"></a>Gruppavsättningar och avetablering

Gruppetablering och avetablering är valfria. När den implementeras och aktiveras visar följande bild de meddelanden som Azure AD skickar till en SCIM-tjänst för att hantera livscykeln för en grupp i programmets identitetsarkiv.  Dessa meddelanden skiljer sig från meddelandena om användare på två sätt:

* Begäranden om att hämta grupper anger att medlemsattributet ska uteslutas från alla resurser som tillhandahålls som svar på begäran.  
* Begäranden om att avgöra om ett referensattribut har ett visst värde är begäranden om medlemsattributet.  

![Visar sekvensen för gruppetablering och avetablering](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Gruppavsättningar och avetableringssekvens*

### <a name="scim-protocol-requests-and-responses"></a>FÖRFRÅGNINGAR och svar för SCIM-protokoll
Det här avsnittet innehåller exempel på SCIM-begäranden som avges av Azure AD SCIM-klienten och exempel på förväntade svar. För bästa resultat bör du koda din app för att hantera dessa begäranden i det här formatet och avge de förväntade svaren.

> [!IMPORTANT]
> Information om hur och när azure AD-användaretableringstjänsten avger de åtgärder som beskrivs nedan finns i avsnittet [Etableringscykler: Inledande och inkrementell](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) i [Hur etablering fungerar](how-provisioning-works.md).

[Användaråtgärder](#user-operations)
  - [Skapa användare](#create-user) ([begäran svar)](#request) / [Response](#response)
  - [Hämta användare](#get-user) ([svar på begäran)](#request-1) / [Response](#response-1)
  - [Hämta användare efter fråga](#get-user-by-query) ([begärandesvar](#request-2) / [Response](#response-2))
  - [Hämta användare efter fråga - Nollresultat](#get-user-by-query---zero-results) ([begäranssvar](#request-3)
/ [Response](#response-3))
  - [Uppdatera användare [Egenskaper med flera värden]](#update-user-multi-valued-properties) ([begärandesvar](#request-4) /  [Response](#response-4))
  - [Uppdatera användare [Egenskaper med ett värde]](#update-user-single-valued-properties) ([begärandesvar](#request-5)
/ [Response](#response-5)) 
  - [Inaktivera användare](#disable-user) ([begäran svar)](#request-14) / 
[Response](#response-14)
  - [Ta bort användare](#delete-user) ([begäran svar)](#request-6) / 
[Response](#response-6)


[Koncernens verksamhet](#group-operations)
  - [Skapa grupp](#create-group) ( [svar på begäran](#request-7) / [)](#response-7)
  - [Hämta grupp](#get-group) [(svar på begäran)](#request-8) / [Response](#response-8)
  - [Hämta grupp efter displayName](#get-group-by-displayname) ([svar på begäran)](#request-9) / [Response](#response-9)
  - [Uppdatera grupp [attribut som inte är medlemmar]](#update-group-non-member-attributes) ([svar på begäran](#request-10) /
  [)](#response-10)
  - [Uppdateringsgruppen [Lägg till medlemmar]](#update-group-add-members) ( [svar på begäran](#request-11) /
[)](#response-11)
  - [Uppdateringsgruppen [Ta bort medlemmar]](#update-group-remove-members) ( [svar på begäran](#request-12) /
[)](#response-12)
  - [Ta bort grupp](#delete-group) ([svar på begäran](#request-13) /
[)](#response-13)

### <a name="user-operations"></a>Användaråtgärder

* Användare kan frågas `userName` `email[type eq "work"]` av eller attribut.  

#### <a name="create-user"></a>Skapa användare

###### <a name="request"></a>Förfrågan

*POST /Användare*
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

*HTTP/1.1 201 Skapad*
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

###### <a name="request"></a><a name="request-1"></a>Förfrågan
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Svar (användaren hittades)
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

###### <a name="request"></a>Förfrågan
*FÅ /Användare/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Svar (Användaren hittades inte. Observera att detaljen inte krävs, bara status.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Hämta fråga för användare

##### <a name="request"></a><a name="request-2"></a>Förfrågan

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Svar

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

#### <a name="get-user-by-query---zero-results"></a>Hämta användare efter fråga - Nollresultat

##### <a name="request"></a><a name="request-3"></a>Förfrågan

*GET /Users?filter=userName eq "icke-existerande användare"*

##### <a name="response"></a><a name="response-3"></a>Svar

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

#### <a name="update-user-multi-valued-properties"></a>Uppdatera användare [Egenskaper med flera värden]

##### <a name="request"></a><a name="request-4"></a>Förfrågan

*PATCH /Användare/6764549bef60420686bc HTTP/1.1*
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

##### <a name="response"></a><a name="response-4"></a>Svar

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

#### <a name="update-user-single-valued-properties"></a>Uppdatera användare [Egenskaper med ett värde]

##### <a name="request"></a><a name="request-5"></a>Förfrågan

*PATCH /Användare/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a><a name="response-5"></a>Svar

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

### <a name="disable-user"></a>Inaktivera användare

##### <a name="request"></a><a name="request-14"></a>Förfrågan

*PATCH /Användare/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>Svar

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Ta bort användare

##### <a name="request"></a><a name="request-6"></a>Förfrågan

*TA BORT /Användare/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Svar

*HTTP/1.1 204 Inget innehåll*

### <a name="group-operations"></a>Koncernens verksamhet

* Grupper ska alltid skapas med en tom medlemslista.
* Grupper kan efterfrågas `displayName` av attributet.
* Uppdatering av grupp patch-begäran bör ge ett *HTTP 204 Inget innehåll* i svaret. Att returnera en kropp med en lista över alla medlemmar är inte tillrådligt.
* Det är inte nödvändigt att stödja återvändande alla medlemmar i gruppen.

#### <a name="create-group"></a>Skapa grupp

##### <a name="request"></a><a name="request-7"></a>Förfrågan

*POST /Grupper HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>Svar

*HTTP/1.1 201 Skapad*
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

##### <a name="request"></a><a name="request-8"></a>Förfrågan

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Svar
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

#### <a name="get-group-by-displayname"></a>Hämta grupp efter displayNamn

##### <a name="request"></a><a name="request-9"></a>Förfrågan
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Svar

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

#### <a name="update-group-non-member-attributes"></a>Uppdatera grupp [attribut som inte är medlemmar]

##### <a name="request"></a><a name="request-10"></a>Förfrågan

*PATCH /Grupper/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response"></a><a name="response-10"></a>Svar

*HTTP/1.1 204 Inget innehåll*

### <a name="update-group-add-members"></a>Uppdateringsgruppen [Lägg till medlemmar]

##### <a name="request"></a><a name="request-11"></a>Förfrågan

*PATCH /Grupper/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-11"></a>Svar

*HTTP/1.1 204 Inget innehåll*

#### <a name="update-group-remove-members"></a>Uppdateringsgruppen [Ta bort medlemmar]

##### <a name="request"></a><a name="request-12"></a>Förfrågan

*PATCH /Grupper/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-12"></a>Svar

*HTTP/1.1 204 Inget innehåll*

#### <a name="delete-group"></a>Ta bort grupp

##### <a name="request"></a><a name="request-13"></a>Förfrågan

*TA BORT /Grupper/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Svar

*HTTP/1.1 204 Inget innehåll*

### <a name="security-requirements"></a>Säkerhetskrav
**TLS-protokollversioner**

De enda godtagbara TLS-protokollversionerna är TLS 1.2 och TLS 1.3. Inga andra versioner av TLS är tillåtna. Ingen version av SSL är tillåten. 
- RSA-nycklar måste vara minst 2 048 bitar.
- ECC-nycklar måste vara minst 256 bitar, som genereras med hjälp av en godkänd elliptisk kurva


**Nyckellängder**

Alla tjänster måste använda X.509-certifikat som genereras med kryptografiska nycklar med tillräcklig längd, vilket innebär:

**Cipher-sviter**

Alla tjänster måste konfigureras för att använda följande chiffersviter, i exakt den ordning som anges nedan. Observera att om du bara har ett RSA-certifikat har de installerade ECDSA-chiffersviterna ingen effekt. </br>

Minsta stapel för TLS 1.2 Cipher Suites:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>Steg 3: Skapa en SCIM-slutpunkt

Nu när du har utformat ditt schema och förstått Azure AD SCIM-implementeringen kan du komma igång med att utveckla din SCIM-slutpunkt. I stället för att börja om från början och bygga implementeringen helt på egen hand kan du lita på ett antal SCIM-bibliotek med öppen källkod som publiceras av SCIM-communityn.

Referenskoden för open [reference code](https://aka.ms/SCIMReferenceCode) source .NET Core som publiceras av Azure AD-etableringsteamet är en sådan resurs som kan starta utvecklingen. När du har byggt din SCIM-slutpunkt vill du testa den. Du kan använda insamling av [postman tester](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) som tillhandahålls som en del av referenskoden eller köra igenom provet förfrågningar / svar som [anges ovan](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations).  

   > [!Note]
   > Referenskoden är avsedd att hjälpa dig att komma igång med att skapa din SCIM-slutpunkt och tillhandahålls "AS IS". Bidrag från samhället är välkomna att hjälpa till att bygga och underhålla koden.

Lösningen består av två projekt, _Microsoft.SCIM_ och _Microsoft.SCIM.WebHostSample_.

_Microsoft.SCIM-projektet_ är det bibliotek som definierar komponenterna i webbtjänsten som överensstämmer med SCIM-specifikationen. Det förklarar gränssnittet _Microsoft.SCIM.IProvider_, begäranden översätts till samtal till leverantörens metoder, som skulle programmeras för att fungera på en identitetsbutik.

![Uppdelning: En begäran översatt till samtal till leverantörens metoder](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

_Projektet Microsoft.SCIM.WebHostSample_ är ett Visual Studio-ASP.NET Core Web Application, baserat på den _tomma_ mallen. På så sätt kan exempelkoden distribueras som fristående, värd i behållare eller i Internet Information Services. Det implementerar också _Microsoft.SCIM.IProvider-gränssnittet_ som håller klasser i minnet som ett exempel identitetslagring.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Skapa en anpassad SCIM-slutpunkt

SCIM-tjänsten måste ha ett HTTP-adress- och serverautentiseringscertifikat som rotcertifikatutfärdaren är ett av följande namn på:

* Cnnic
* Comodo
* CyberTrust (cybertrust)
* DigiCert
* Geotrust
* GlobalSign
* Gå Pappa
* Verisign
* WoSign (andra)

.NET Core SDK innehåller ett HTTPS-utvecklingscertifikat som kan användas under utvecklingen, certifikatet installeras som en del av den första körningen. Beroende på hur du kör ASP.NET Core Web Application kommer det att lyssna på en annan port:

* Microsoft.SCIM.WebHostSample:https://localhost:5001
* IIS Express:https://localhost:44359/

Om du vill ha mer information om HTTPS i ASP.NET Core använder du följande länk: [Framtvinga HTTPS i ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Hantering av autentisering av slutpunkt

Begäranden från Azure Active Directory innehåller en OAuth 2.0-innehavartoken. Alla tjänster som tar emot begäran bör autentisera utfärdaren som Azure Active Directory för den förväntade Azure Active Directory-klienten.

I token identifieras utfärdaren av ett ISS-anspråk, till exempel `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`. I det här exemplet identifierar basadressen för anspråksvärdet `https://sts.windows.net`Azure Active Directory som utfärdare, medan det relativa adresssegmentet, _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_, är en unik identifierare för Azure Active Directory-klienten som token utfärdades för.

Målgruppen för token kommer att vara programmall-ID för programmet i galleriet, var och `iss` en av de program som är registrerade i en enda klient kan få samma anspråk med SCIM-begäranden. Ansökningsmalls-ID:t för varje program i [ProvisioningFeedback@microsoft.com](mailto:ProvisioningFeedback@microsoft.com) galleriet varierar, vänligen kontakta för frågor kring programmall-ID för ett galleriprogram. ProgrammallenS ID för alla anpassade appar är _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_.

I exempelkoden autentiseras begäranden med hjälp av paketet Microsoft.AspNetCore.Authentication.JwtBearer. Följande kod tvingar fram att begäranden till någon av tjänstens slutpunkter autentiseras med hjälp av innehavartoken som utfärdats av Azure Active Directory för en angiven klient:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

En innehavartoken krävs också för att använda de medföljande [postman-testerna](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) och utföra lokal felsökning med localhost. Exempelkoden använder ASP.NET Core-miljöer för att ändra autentiseringsalternativen under utvecklingsfasen och aktivera användning av en självsignerad token.

Om du vill ha mer information om flera miljöer i ASP.NET Core använder du följande länk: [Använd flera miljöer i ASP.NET Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

Följande kod tvingar fram att begäranden till någon av tjänstens slutpunkter autentiseras med en innehavartoken som signerats med en anpassad nyckel:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

Skicka en GET-begäran till tokenstyrenheten för att få en giltig innehavartoken, metoden _GenerateJSONWebToken_ ansvarar för att skapa en token som matchar de parametrar som konfigurerats för utveckling:

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Hantering av etablering och avetablering av användare

***Exempel 1. Fråga tjänsten efter en matchande användare***

Azure Active Directory frågar tjänsten för en användare med ett externtId-attributvärde som matchar attributevärdet mailNickname för en användare i Azure AD. Frågan uttrycks som en HTTP-begäran (Hypertext Transfer Protocol), till exempel det här exemplet, där jyoung är ett exempel på ett mailNickname för en användare i Azure Active Directory.

>[!NOTE]
> Detta är bara ett exempel. Alla användare har inte ett mailNickname-attribut och värdet som en användare har kanske inte är unikt i katalogen. Det attribut som används för matchning (som i det här fallet är externalId) kan också konfigureras i [Azure AD-attributmappningarna](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

I exempelkoden översätts begäran till ett anrop till QueryAsync-metoden för tjänstens leverantör. Här är signaturen av denna metod: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

För en användare med ett visst värde för attributet externalId i exempelfrågan är värdena för argumenten som skickas till metoden QueryAsync:

* Parametrar. AlternateFilters.Count: 1
* Parametrar. AlternateFilters.elementAt(0). AttributePath: "externalId"
* Parametrar. AlternateFilters.elementAt(0). JämförelseOperator: ComparisonOperator.Equals
* Parametrar. AlternateFilter.elementAt(0). JämförelseVärde: "jyoung"

***Exempel 2. Etablera en användare***

Om svaret på en fråga till webbtjänsten för en användare med ett externtId-attributvärde som matchar attributevärdet mailNickname för en användare inte returnerar några användare, begär Azure Active Directory att tjänsten etablerar en användare som motsvarar den som motsvarar den som motsvarar den i Azure Active Directory.  Här är ett exempel på en sådan begäran: 

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

I exempelkoden översätts begäran till ett anrop till CreateAsync-metoden för tjänstens leverantör. Här är signaturen av denna metod: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

I en begäran om att etablera en användare är värdet för resursargumentet en instans av klassen Microsoft.SCIM.Core2EnterpriseUser, definierad i biblioteket Microsoft.SCIM.Schemas.  Om begäran om att etablera användaren lyckas förväntas implementeringen av metoden returnera en instans av klassen Microsoft.SCIM.Core2EnterpriseUser, med värdet för egenskapen Identifier inställd på den unika identifieraren för den nyligen etablerade Användaren.  

***Exempel 3. Fråga om en användares aktuella tillstånd*** 

Om du vill uppdatera en användare som är känd för att finnas i ett identitetsarkiv som är frontad av en SCIM fortsätter Azure Active Directory genom att begära det aktuella tillståndet för användaren från tjänsten med en begäran, till exempel: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

I exempelkoden översätts begäran till ett anrop till metoden RetrieveAsync för tjänstens leverantör. Här är signaturen av denna metod: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

I exemplet med en begäran om att hämta en användares aktuella tillstånd är värdena för egenskaperna för objektet som anges som värdet för parameterargumentet följande: 
  
* Identifierare: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Exempel 4. Fråga värdet för ett referensattribut som ska uppdateras*** 

Om ett referensattribut ska uppdateras frågar Azure Active Directory tjänsten för att avgöra om det aktuella värdet för referensattributet i identitetsarkivet som visas av tjänsten redan matchar värdet för attributet i Azure Active Katalog. För användare är det enda attribut som det aktuella värdet efterfrågas på det här sättet förvaltarattributet. Här är ett exempel på en begäran för att avgöra om förvaltarattributet för ett användarobjekt för närvarande har ett visst värde: I exempelkoden översätts begäran till ett anrop till QueryAsync-metoden för tjänstens leverantör. Värdet för egenskaperna för objektet som anges som värdet för parameterargumentet är följande: 
  
* Parametrar. AlternateFilters.Count: 2
* Parametrar. AlternateFilters.ElementAt(x). AttributPath: "ID"
* Parametrar. AlternateFilters.ElementAt(x). JämförelseOperator: ComparisonOperator.Equals
* Parametrar. AlternateFilter.elementAt(x). JämförelseVärde: "54D382A4-2050-4C03-94D1-E769F1D15682"
* Parametrar. AlternateFilters.ElementAt(y). AttributePath: "manager"
* Parametrar. AlternateFilters.ElementAt(y). JämförelseOperator: ComparisonOperator.Equals
* Parametrar. AlternateFilter.elementAt(y). JämförelseVärde: "2819c223-7f76-453a-919d-413861904646"
* Parametrar. RequestedAttributePaths.ElementAt(0): "ID"
* Parametrar. SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Här kan värdet för index x vara 0 och värdet för index y kan vara 1, eller värdet på x kan vara 1 och värdet på y kan vara 0, beroende på ordningen för uttrycken för filterfrågeparametern.   

***Exempel 5. Begär från Azure AD till en SCIM-tjänst för att uppdatera en användare*** 

Här är ett exempel på en begäran från Azure Active Directory till en SCIM-tjänst för att uppdatera en användare: 

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

I exempelkoden översätts begäran till ett anrop till UpdateAsync-metoden för tjänstens leverantör. Här är signaturen av denna metod: 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

I exemplet med en begäran om att uppdatera en användare har objektet som värdet för korrigeringsargumentet dessa egenskapsvärden: 
  
* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest som PatchRequest2). Operations.Count: 1
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Lägg till
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "manager"
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). Värde.Antal: 1
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referens: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Värde: 2819c223-7f76-453a-919d-413861904646

***Exempel 6. Avetablera en användare***

Om du vill avetablera en användare från ett identitetsarkiv som är frontat av en SCIM-tjänst skickar Azure AD en begäran, till exempel:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

I exempelkoden översätts begäran till ett anrop till Metoden DeleteAsync för tjänstens leverantör. Här är signaturen av denna metod: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

Objektet som anges som värdet för argumentet resourceIdentifier har dessa egenskapsvärden i exemplet med en begäran om att avetablera en användare: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Steg 4: Integrera din SCIM-slutpunkt med Azure AD SCIM-klienten

Azure AD kan konfigureras för att automatiskt etablera tilldelade användare och grupper till program som implementerar en specifik profil för [SCIM 2.0-protokollet](https://tools.ietf.org/html/rfc7644). Detaljerna i profilen dokumenteras i [steg 2: Förstå Azure AD SCIM-implementeringen](#step-2-understand-the-azure-ad-scim-implementation).

Kontakta programleverantören eller programleverantörens dokumentation om du vill ha kompatibilitet med dessa krav.

> [!IMPORTANT]
> Azure AD SCIM-implementeringen bygger på azure AD-användaretableringstjänsten, som är utformad för att ständigt hålla användarna synkroniserade mellan Azure AD och målprogrammet, och implementerar en mycket specifik uppsättning standardåtgärder. Det är viktigt att förstå dessa beteenden för att förstå beteendet hos Azure AD SCIM-klienten. Mer information finns i avsnittet [Etableringscykler: Inledande och inkrementellt](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) i [Hur etablering fungerar](how-provisioning-works.md).

### <a name="getting-started"></a>Komma igång

Program som stöder SCIM-profilen som beskrivs i den här artikeln kan anslutas till Azure Active Directory med funktionen "icke-galleriprogram" i Azure AD-programgalleriet. När Azure AD är anslutet kör azure AD en synkroniseringsprocess var 40:e minut där den frågar programmets SCIM-slutpunkt för tilldelade användare och grupper och skapar eller ändrar dem enligt tilldelningsinformationen.

**Så här ansluter du ett program som stöder SCIM:**

1. Logga in på [Azure Active Directory-portalen](https://aad.portal.azure.com). Observera att du kan få tillgång till en kostnadsfri utvärderingsversion för Azure Active Directory med P2-licenser genom att registrera dig för [utvecklarprogrammet](https://developer.microsoft.com/office/dev-program)
2. Välj **Enterprise-program** i den vänstra rutan. En lista över alla konfigurerade appar visas, inklusive appar som har lagts till från galleriet.
3. Välj **+ Nytt program** > **Alla** > **program som inte är galleri**.
4. Ange ett namn på ditt program och välj **Lägg till** för att skapa ett appobjekt. Den nya appen läggs till i listan över företagsprogram och öppnas på skärmen för apphantering.

   ![Skärmbild visar Azure AD-programgalleriet](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD-programgalleri*

5. På skärmen för apphantering väljer du **Etablera** på den vänstra panelen.
6. Välj **Automatisk**på menyn **Etableringsläge** .

   ![Exempel: En apps etableringssida i Azure-portalen](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurera etablering i Azure-portalen*

7. I fältet **Klient-URL** anger du URL:en för programmets SCIM-slutpunkt. Exempel: `https://api.contoso.com/scim/`
8. Om SCIM-slutpunkten kräver en OAuth-bäraretoken från en annan utfärdare än Azure AD, kopierar du den nödvändiga OAuth-innehavartoken till det valfria **hemliga tokenfältet.** Om det här fältet lämnas tomt innehåller Azure AD en OAuth-innehavartoken som utfärdats från Azure AD för varje begäran. Appar som använder Azure AD som identitetsprovider kan validera den här Azure AD-utfärdade token. 
   > [!NOTE]
   > Det rekommenderas ***inte*** att lämna det här fältet tomt och förlita sig på en token som genereras av Azure AD. Det här alternativet är främst tillgängligt för testning.
9. Välj **Testanslutning** om du vill att Azure Active Directory ska försöka ansluta till SCIM-slutpunkten. Om försöket misslyckas visas felinformation.  

    > [!NOTE]
    > **Test Connection** frågar SCIM-slutpunkten för en användare som inte finns, med hjälp av ett slumpmässigt GUID som den matchande egenskap som valts i Azure AD-konfigurationen. Det förväntade korrekta svaret är HTTP 200 OK med ett tomt SCIM ListResponse-meddelande.

10. Om försöken att ansluta till programmet lyckas väljer du **Spara** för att spara administratörsautentiseringsuppgifterna.
11. I avsnittet **Mappningar** finns två valbara uppsättningar [attributmappningar:](customize-application-attributes.md)en för användarobjekt och en för gruppobjekt. Välj var och en för att granska attribut som synkroniseras från Azure Active Directory till din app. De attribut som valts som **matchande** egenskaper används för att matcha användare och grupper i appen för uppdateringsåtgärder. Välj **Spara** om du vill genomföra eventuella ändringar.

    > [!NOTE]
    > Du kan också inaktivera synkronisering av gruppobjekt genom att inaktivera mappningen av "grupper".

12. Under **Inställningar**definierar fältet **Scope** vilka användare och grupper som ska synkroniseras. Välj **Synkronisera endast tilldelade användare och grupper** (rekommenderas) till endast synkronisera användare och grupper som tilldelats på fliken Användare och **grupper.**
13. När konfigurationen är klar ställer du in **etableringsstatusen** **till På**.
14. Välj **Spara** om du vill starta azure AD-etableringstjänsten.
15. Om synkronisering endast tilldelade användare och grupper (rekommenderas) måste du välja fliken **Användare och grupper** och tilldela de användare eller grupper som du vill synkronisera.

När den inledande cykeln har startat kan du välja **Etableringsloggar** på den vänstra panelen för att övervaka förloppet, som visar alla åtgärder som utförs av etableringstjänsten i din app. Mer information om hur du läser Azure AD-etableringsloggarna finns i [Rapportera om automatisk etablering av användarkonton](check-status-user-account-provisioning.md).

> [!NOTE]
> Den inledande cykeln tar längre tid att utföra än senare synkroniseringar, som inträffar ungefär var 40:e minut så länge tjänsten körs.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Steg 5: Publicera ditt program i Azure AD-programgalleriet

Om du skapar ett program som ska användas av mer än en klient kan du göra det tillgängligt i Azure AD-programgalleriet. Detta gör det enkelt för organisationer att identifiera programmet och konfigurera etablering. Det är enkelt att publicera din app i Azure AD-galleriet och göra etableringen tillgänglig för andra. Kolla in stegen [här](../develop/howto-app-gallery-listing.md). Microsoft kommer att arbeta med dig för att integrera ditt program i vårt galleri, testa din slutpunkt och släppa [introduktionsdokumentation](../saas-apps/tutorial-list.md) för kunder att använda. 

### <a name="gallery-onboarding-checklist"></a>Checklista för introduktion av galleri
Följ checklistan nedan för att se till att ditt program är inbyggt snabbt och kunderna har en smidig driftsättningsupplevelse. Informationen kommer att samlas in från dig när du går ombord på galleriet. 
> [!div class="checklist"]
> * Stöd för en [SCIM 2.0-användare](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) och gruppslutpunkt (endast en krävs men båda rekommenderas)
> * Stöd minst 25 begäranden per sekund per klient (Obligatoriskt)
> * Upprätta tekniska och supportkontakter för att vägleda kunder efter introduktionsgalleri (Obligatoriskt)
> * 3 Icke-utgående testreferenser för ditt program (Obligatoriskt)
> * Stöd OAuth-auktoriseringskodsbidraget eller en långlivade token enligt beskrivningen nedan (Obligatoriskt)
> * Upprätta en teknisk och supportkontaktpunkt för att stödja kunder efter galleri onboarding (Obligatoriskt)
> * Stöd för uppdatering av flera gruppmedlemskap med en enda PATCH (Rekommenderas) 
> * Dokumentera scim-slutpunkten offentligt (rekommenderas) 
> * [Identifiering av stödschema](https://tools.ietf.org/html/rfc7643#section-6) (rekommenderas)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Auktorisering för etablering av kopplingar i programgalleriet
SCIM-specifikationen definierar inte ett SCIM-specifikt schema för autentisering och auktorisering. Den bygger på användningen av befintliga branschstandarder. Azure AD-etableringsklienten stöder två auktoriseringsmetoder för program i galleriet. 

|Auktoriseringsmetod|Fördelar|Nackdelar|Support|
|--|--|--|--|
|Användarnamn och lösenord (rekommenderas inte eller stöds av Azure AD)|Lätt att implementera|Osäker - [Din Pa $ $word spelar ingen roll](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Stöds från fall till fall för galleriappar. Stöds inte för appar som inte är galleri.|
|Långlivade bärare token|Långlivade token kräver inte att en användare är närvarande. De är lätta för administratörer att använda när du ställer in etablering.|Långlivade tokens kan vara svåra att dela med en administratör utan att använda osäkra metoder som e-post. |Stöds för galleri- och icke-galleriappar. |
|Beviljande av OAuth-auktoriseringskod|Åtkomsttoken är mycket kortare än lösenord och har en automatiserad uppdateringsmekanism som långlivade innehavartoken inte har.  En verklig användare måste vara närvarande under den första auktoriseringen och lägga till en ansvarsnivå. |Kräver att en användare är närvarande. Om användaren lämnar organisationen är token ogiltig och auktoriseringen måste slutföras igen.|Stöds för galleriappar. Stöd för appar som inte är gallerier pågår.|
|Bevilja OAuth-klientautentiseringsuppgifter|Åtkomsttoken är mycket kortare än lösenord och har en automatiserad uppdateringsmekanism som långlivade innehavartoken inte har. Både auktoriseringskodsbidraget och klientautentiseringsbehörighetsbe beviljandet skapar samma typ av åtkomsttoken, så att flytta mellan dessa metoder är transparent till API:et.  Etablering kan automatiseras helt och nya token kan begäras tyst utan användarinteraktion. ||Stöds inte för galleri- och icke-galleriappar. Stödet finns i vår eftersläpning.|

[!NOTE] Det rekommenderas inte att lämna tokenfältet tomt i Azure AD-etableringskonfigurationens anpassade appgränssnitt. Token som genereras är främst tillgänglig för testning.

**Bidragsflöde för OAuth-auktoriseringskod:** Etableringstjänsten stöder [beviljandet av auktoriseringskod](https://tools.ietf.org/html/rfc6749#page-24). När du har skickat in din begäran om publicering av din app i galleriet kommer vårt team att arbeta med dig för att samla in följande information:
*  Auktoriserings-URL: En URL från klienten för att erhålla auktorisering från resursägaren via omdirigering av användare och agenter. Användaren omdirigeras till den här URL:en för att auktorisera åtkomst. 
*  Url för tokenutbyte: En URL från klienten för att byta ut ett auktoriseringsbidrag för en åtkomsttoken, vanligtvis med klientautentisering.
*  Klient-ID: Auktoriseringsservern utfärdar en registrerad klient som en klientidentifierare, vilket är en unik sträng som representerar registreringsinformationen som tillhandahålls av klienten.  Klientidentifieraren är inte en hemlighet. Den är exponerad för resursägaren och **får inte** användas ensam för klientautentisering.  
*  Klienthemlighet: Klienthemligheten är en hemlighet som genereras av auktoriseringsservern. Det bör vara ett unikt värde som endast är känt för auktoriseringsservern. 

Observera att OAuth v1 inte stöds på grund av exponering av klienthemligheten. OAuth v2 stöds.  

Bästa praxis (rekommenderas men krävs inte):
* Stöd för flera omdirigeringsadresser. Administratörer kan konfigurera etablering från både "portal.azure.com" och "aad.portal.azure.com". Stöd för flera omdirigeringsadresser säkerställer att användare kan auktorisera åtkomst från båda portalerna.
* Stöd flera hemligheter för att säkerställa smidig hemlig förnyelse, utan driftstopp. 

**Långlivade OAuth bärare tokens:** Om ditt program inte stöder OAuth-auktoriseringskodstillbehörsflödet kan du också generera en långlivade OAuth-bäraretoken än att en administratör kan använda för att ställa in etableringsintegrationen. Token ska vara evig, annars kommer etableringsjobbet att [sättas i karantän](application-provisioning-quarantine-status.md) när token upphör att gälla. Denna token måste vara lägre än 1KB i storlek.  

För ytterligare autentiserings- och auktoriseringsmetoder, låt oss veta på [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Galleri go-to-market lansering checklista
För att öka medvetenheten och efterfrågan på vår gemensamma integration rekommenderar vi att du uppdaterar din befintliga dokumentation och förstärker integrationen i dina marknadsföringskanaler.  Nedanstående är en uppsättning checklista aktiviteter som vi rekommenderar att du slutför för att stödja lanseringen

* **Försäljnings- och kundsupportberedskap.** Se till att dina försäljnings- och supportteam är medvetna och kan tala med integrationsfunktionerna. Informera ditt sälj- och supportteam, förse dem med vanliga frågor och inkludera integreringen i ditt försäljningsmaterial. 
* **Blogginlägg och/eller pressmeddelande.** Skapa ett blogginlägg eller pressmeddelande som beskriver den gemensamma integrationen, fördelarna och hur du kommer igång. [Exempel: Imprivata och Azure Active Directory Pressmeddelande](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Sociala medier.** Utnyttja dina sociala medier som Twitter, Facebook eller LinkedIn för att marknadsföra integrationen till dina kunder. Var noga @AzureAD med att inkludera så att vi kan retweet ditt inlägg. [Exempel: Imprivata Twitter Post](https://twitter.com/azuread/status/1123964502909779968)
* **Marknadsföring webbplats.** Skapa eller uppdatera dina marknadsföringssidor (t.ex. integrationssida, partnersida, prissida osv.) för att inkludera tillgängligheten för den gemensamma integrationen. [Exempel: Pingboard-integreringssida,](https://pingboard.com/org-chart-for) [sidan Integrering av Smartsheet,](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad) [Monday.com prissida](https://monday.com/pricing/) 
* **Teknisk dokumentation.** Skapa en hjälpcenterartikel eller teknisk dokumentation om hur kunder kan komma igång. [Exempel: Envoy + Microsoft Azure Active Directory-integrering.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Kundkommunikation.** Varna kunderna för den nya integrationen genom din kundkommunikation (månatliga nyhetsbrev, e-postkampanjer, produktreleaseanteckningar). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Tillåt IP-adresser som används av Azure AD-etableringstjänsten att göra SCIM-begäranden

Vissa appar tillåter inkommande trafik till sin app. För att Azure AD-etableringstjänsten ska fungera som förväntat måste IP-adresser som används tillåtas. En lista över IP-adresser för varje tjänsttag/region finns i JSON-filen – [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Du kan ladda ner och programmera dessa IPs i din brandvägg efter behov. De reserverade IP-intervallen för Azure AD-etablering finns under "AzureActiveDirectoryDomainServices".

## <a name="related-articles"></a>Relaterade artiklar

* [Automatisera etablering av användare och avetablering till SaaS-appar](user-provisioning.md)
* [Anpassa attributmappningar för användaretablering](customize-application-attributes.md)
* [Skriva uttryck för attributmappningar](functions-for-customizing-application-data.md)
* [Omfångsfilter för användaretablering](define-conditional-rules-for-provisioning-user-accounts.md)
* [Meddelanden om kontoetablering](user-provisioning.md)
* [Lista över självstudier om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)
