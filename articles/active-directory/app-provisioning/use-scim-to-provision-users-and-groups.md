---
title: Utveckla en SCIM-slutpunkt för användar etablering till appar från Azure AD
description: System för SCIM (Cross-Domain Identity Management) standardiserar automatisk användar etablering. Lär dig att utveckla en SCIM-slutpunkt, integrera ditt SCIM-API med Azure Active Directory och börja automatisera etableringen av användare och grupper i dina moln program.
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
ms.date: 2/7/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55b31dec0531add8e8c3b40bd9cc3e031ef30000
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066387"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Bygg en SCIM-slutpunkt och konfigurera användar etablering med Azure Active Directory (Azure AD)

Som programutvecklare kan du använda systemet för användar hanterings-API: et för SCIM (Cross-Domain Identity Management) för att aktivera automatisk etablering av användare och grupper mellan ditt program och Azure AD. Den här artikeln beskriver hur du skapar en SCIM-slutpunkt och integrerar med Azure AD Provisioning-tjänsten. SCIM-specifikationen innehåller ett gemensamt användar schema för etablering. När det används tillsammans med Federations standarder som SAML eller OpenID Connect ger SCIM administratörer en heltäckande lösning för åtkomst hantering från slut punkt till slut punkt.

SCIM är en standardiserad definition av två slut punkter: en/users-slutpunkt och en/Groups-slutpunkt. Den använder vanliga REST-verb för att skapa, uppdatera och ta bort objekt och ett fördefinierat schema för vanliga attribut, t. ex. grupp namn, användar namn, förnamn, efter namn och e-post. Appar som erbjuder en SCIM 2,0-REST API kan minska eller eliminera den smärta som fungerar med ett eget användar hanterings-API. Till exempel vet alla kompatibla SCIM-klienter hur du gör en HTTP POST av ett JSON-objekt till/Users-slutpunkten för att skapa en ny användar post. I stället för att behöva ett något annorlunda API för samma grundläggande åtgärder kan appar som uppfyller SCIM-standarden omedelbart dra nytta av befintliga klienter, verktyg och kod. 

![Etablering från Azure AD till en app med SCIM](./media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Standardschemat för användar objekt och REST API: er för hantering som definieras i SCIM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) gör att identitets leverantörer och appar enkelt kan integreras med varandra. Programutvecklare som skapar en SCIM-slutpunkt kan integreras med alla SCIM-kompatibla klienter utan att behöva göra anpassade arbeten.

Att automatisera etableringen av ett program kräver att du skapar och integrerar en SCIM-slutpunkt med Azure AD SCIM-kompatibel. Utför följande steg för att starta etableringen av användare och grupper i ditt program. 
    
  * **[Steg 1: utforma användar-och grupp schemat.](#step-1-design-your-user-and-group-schema)** Identifiera de objekt och attribut som programmet behöver och Bestäm hur de mappar till användar-och grupp schema som stöds av Azure AD SCIM-implementeringen.

  * **[Steg 2: förstå Azure AD SCIM-implementeringen.](#step-2-understand-the-azure-ad-scim-implementation)** Förstå hur Azure AD SCIM-klienten implementeras och utforma SCIM-protokollets hantering och svar.

  * **[Steg 3: Bygg en SCIM-slutpunkt.](#step-3-build-a-scim-endpoint)** En slut punkt måste vara SCIM 2,0-kompatibel för integrering med Azure AD Provisioning-tjänsten. Som ett alternativ kan du använda CLI-bibliotek (Common Language Infrastructure) och kod exempel för att bygga din slut punkt. Dessa exempel är endast för referens och testning. Vi rekommenderar att du kodar din produktions-app för att ta ett beroende på dem.

  * **[Steg 4: integrera din SCIM-slutpunkt med Azure AD SCIM-klienten.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Om din organisation använder ett program från tredje part som implementerar profilen för SCIM 2,0 som Azure AD stöder, kan du börja automatisera både etablering och avetablering av användare och grupper direkt.

  * **[Steg 5: publicera ditt program i Azure AD-programgalleriet.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Gör det enkelt för kunderna att upptäcka ditt program och enkelt konfigurera etablering. 

![Steg för att integrera en SCIM-slutpunkt med Azure AD](./media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Steg 1: utforma användar-och grupp schema

Varje program kräver olika attribut för att skapa en användare eller grupp. Starta din integrering genom att identifiera de objekt (användare, grupper) och attribut (namn, chef, befattning osv.) som programmet kräver. SCIM-standarden definierar ett schema för hantering av användare och grupper. Kärn användar schemat kräver bara tre attribut: **ID** (Service Provider Defined Identifier), **externalId** (identifierare för den klient som definieras) och **meta** (skrivskyddade metadata som underhålls av tjänst leverantören). Alla andra attribut är valfria. Förutom kärn användar schemat definierar SCIM-standarden ett företags användar tillägg och en modell för utökning av användar schemat för att uppfylla ditt programs behov. Om ditt program till exempel kräver en användares chef kan du använda användar schemat för företag för att samla in användarens chef och kärn schemat för att samla in användarens e-post. Skapa schemat genom att följa stegen nedan:
  1. Lista de attribut som programmet kräver. Det kan vara bra att dela upp dina krav i de attribut som behövs för autentisering (t. ex. loginName och e-post), attribut som behövs för att hantera användarens livs cykel (t. ex. status/aktiv) och andra attribut som behövs för att ditt program ska fungera (t. ex. chef, tagg).
  2. Kontrol lera om dessa attribut redan har definierats i kärn användar schema eller användar schema för företag. Om du har attribut som du behöver och inte omfattas av kärn-eller företags användar scheman, måste du definiera ett tillägg till användar schemat som täcker de attribut som du behöver. I exemplet nedan har vi lagt till ett tillägg till användaren för att tillåta att en "tagg" skapas för en användare. Det är bäst att börja med bara kärn-och företags användar scheman och utöka till ytterligare anpassade scheman senare.  
  3. Mappa SCIM-attributen till användarattribut i Azure AD. Om något av de attribut som du har definierat i SCIM-slutpunkten inte har en tydlig motsvarighet i användar schemat för Azure AD, så är det en stor chans att data inte lagras på användarobjektet på de flesta klienter. Överväg om det här attributet kan vara valfritt för att skapa en användare. Om attributet är kritiskt för att ditt program ska fungera, vägleder klient organisationen att du utökar sitt schema eller använder ett attribut för tillägg som visas nedan för egenskapen "Taggar".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabell 1: disponera de attribut som du behöver 
| Steg 1: Bestäm vilka attribut appen kräver| Steg 2: mappa appens krav till SCIM standard| Steg 3: mappa SCIM-attribut till Azure AD-attributen|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|namn. lastName|lastName|
|workMail|E-postmeddelanden [typ EQ "Work"]. värde|Mail|
|ansvarig|ansvarig|ansvarig|
|tag|urn: IETF: params: scim: schemas: tillägg: 2.0: CustomExtension: tagg|extensionAttribute1|
|status|aktiv|isSoftDeleted (beräknat värde lagras inte på användare)|

Det schema som definierats ovan ska representeras med JSON-nyttolasten nedan. Observera att förutom de attribut som krävs för programmet, innehåller JSON-representationen de obligatoriska attributen "ID,", "externalId" och "meta".

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

### <a name="table-2-default-user-attribute-mapping"></a>Tabell 2: mappning av standardattribut för användare
Du kan sedan använda tabellen nedan för att förstå hur attributen som programmet kräver kan mappas till ett attribut i Azure AD och SCIM RFC. Du kan [Anpassa](customize-application-attributes.md) hur attribut mappas mellan Azure AD och din scim-slutpunkt. Observera att du inte behöver stödja både användare och grupper eller alla attribut som visas nedan. De är en referens för hur attribut i Azure AD ofta mappas till egenskaper i SCIM-protokollet. 

| Azure Active Directory-användare | ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User” |
| --- | --- |
| IsSoftDeleted |aktiv |
|avdelning|urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|
| displayName |displayName |
|Anställnings|urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: employeeNumber|
| Fax TelephoneNumber |phoneNumbers [typ eq ”fax”] .value |
| givenName |name.givenName |
| jobTitle |title |
| e-post |e-postmeddelanden [typ eq ”arbete pågår”] .value |
| mailNickname |externalId |
| ansvarig |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager |
| mobila |phoneNumbers [typ eq ”mobil”] .value |
| Postnummer |adresser typ eq ”arbete pågår” .postalCode |
| Proxy-adresser |e-postmeddelande [Ange eq ”annan”]. Värde |
| fysiska-leverans – OfficeName |adresser [Ange eq ”annan”]. Formaterad |
| streetAddress |adresser typ eq ”arbete pågår” .streetAddress |
| surname |name.familyName |
| Telefonnummer |phoneNumbers [typ eq ”arbete pågår”] .value |
| användaren huvudkontot |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabell 3: mappning av standardmapp-attribut

| Azure Active Directory-grupp | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| e-post |e-postmeddelanden [typ eq ”arbete pågår”] .value |
| mailNickname |displayName |
| medlemmar |medlemmar |
| objekt-ID |externalId |
| proxyAddresses |e-postmeddelande [Ange eq ”annan”]. Värde |

Det finns flera slut punkter definierade i SCIM RFC. Du kan komma igång med/User-slutpunkten och sedan expandera därifrån. /Schemas-slutpunkten är användbar när du använder anpassade attribut eller om schemat ändras ofta. Det gör det möjligt för en klient att hämta det mest uppdaterade schemat automatiskt. /Bulk-slutpunkten är särskilt användbar när du har stöd för grupper. Tabellen nedan beskriver de olika slut punkter som definieras i SCIM-standarden. /Schemas-slutpunkten är användbar när du använder anpassade attribut eller om schemat ändras ofta. Det gör att en klient kan hämta det mest uppdaterade schemat automatiskt. /Bulk-slutpunkten är särskilt användbar när du har stöd för grupper. Tabellen nedan beskriver de olika slut punkter som definieras i SCIM-standarden. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabell 4: Bestäm de slut punkter som du vill utveckla
|ENDPOINT|BESKRIVNING|
|--|--|
|/User|Utföra CRUD-åtgärder på ett användar objekt.|
|/Grupp|Utföra CRUD-åtgärder på ett grupp objekt.|
|/ServiceProviderConfig|Innehåller information om funktionerna i SCIM-standarden som stöds, till exempel de resurser som stöds och autentiseringsmetoden.|
|/ResourceTypes|Anger metadata för varje resurs|
|/Schemas|De attribut som stöds av varje klient och tjänst leverantör kan variera. Även om en tjänst leverantör kan inkludera "namn," "title" och "e-post" medan en annan tjänst leverantör använder "namn", "title" och "phoneNumbers". Schema slut punkten gör det möjligt att identifiera de attribut som stöds.|
|/Bulk|Med Mass åtgärder kan du utföra åtgärder på en stor mängd resurs objekt i en enda åtgärd (t. ex. Uppdatera medlemskap för en stor grupp).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Steg 2: förstå Azure AD SCIM-implementeringen
> [!IMPORTANT]
> Beteendet för Azure AD SCIM-implementeringen uppdaterades senast den 18 december 2018. Information om vad som har ändrats finns i [SCIM 2,0 Protocol Compliance of the Azure AD User Provisioning-tjänsten](../manage-apps/application-provisioning-config-problem-scim-compatibility.md).

Om du skapar ett program som stöder ett SCIM 2,0-API för användar hantering beskrivs i det här avsnittet hur du implementerar Azure AD SCIM-klienten. Det visar också hur du kan modellera hantering och svar för SCIM-protokoll begär Anden. När du har implementerat din SCIM-slutpunkt kan du testa den genom att följa anvisningarna som beskrivs i föregående avsnitt.

I [SCIM 2,0-protokoll specifikationen](http://www.simplecloud.info/#Specification)måste ditt program uppfylla följande krav:

* Har stöd för att skapa användare och eventuellt även grupper, enligt avsnitt [3,3 i scim-protokollet](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Stöder ändring av användare eller grupper med PATCH-begäranden enligt [avsnittet 3.5.2 i scim-protokollet](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Stöder hämtning av en känd resurs för en användare eller grupp som skapats tidigare, enligt [avsnittet 3.4.1 i scim-protokollet](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Har stöd för att skicka frågor till användare eller grupper enligt avsnittet [3.4.2 i scim-protokollet](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Som standard hämtas användare av sina `id` och efter frågas av deras `username` och `externalid`, och grupper efter frågas av `displayName`.  
* Har stöd för att skicka frågor till användare efter ID och chef, enligt avsnittet 3.4.2 i SCIM-protokollet.  
* Har stöd för att skicka frågor till grupper efter ID och per medlem, enligt avsnittet 3.4.2 i SCIM-protokollet.  
* Accepterar en enda Bearer-token för autentisering och auktorisering av Azure AD till ditt program.

Följ dessa allmänna rikt linjer när du implementerar en SCIM-slutpunkt för att säkerställa kompatibilitet med Azure AD:

* `id` är en obligatorisk egenskap för alla resurser. Varje svar som returnerar en resurs måste se till att varje resurs har den här egenskapen, förutom för `ListResponse` med noll medlemmar.
* Svar på en fråga/filter-begäran ska alltid vara en `ListResponse`.
* Grupper är valfria, men stöds endast om SCIM-implementeringen stöder PATCH-begäranden.
* Det är inte nödvändigt att inkludera hela resursen i KORRIGERINGs svaret.
* Microsoft Azure AD använder endast följande operatorer:  
    - `eq`
    - `and`
* Kräv inte Skift läges känslig matchning på strukturella element i SCIM, i synnerhet KORRIGERINGs `op` åtgärds värden, enligt definitionen i https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD avger värdena för "OP" som `Add`, `Replace`och `Remove`.
* Microsoft Azure AD gör begär Anden att hämta en slumpmässig användare och grupp för att säkerställa att slut punkten och autentiseringsuppgifterna är giltiga. Det sker också som en del av **test anslutnings** flödet i [Azure Portal](https://portal.azure.com). 
* Attributet som resurserna kan frågas om på ska anges som ett matchande attribut i programmet i [Azure Portal](https://portal.azure.com). Mer information finns i [Anpassa mappningar för användar etablerings attribut](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>Användar etablering och avetablering

Följande bild visar de meddelanden som Azure Active Directory skickar till en SCIM-tjänst för att hantera livs cykeln för en användare i ditt programs identitets lager.  

![visar sekvensen för användar etablering och avetablering](./media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Användar etablering och avetablerings ordning*

### <a name="group-provisioning-and-deprovisioning"></a>Grupp etablering och avetablering

Grupp etablering och avetablering är valfria. När det implementeras och aktive ras visar följande bild de meddelanden som Azure AD skickar till en SCIM-tjänst för att hantera livs cykeln för en grupp i ditt programs identitets lager.  Dessa meddelanden skiljer sig från meddelanden om användare på två sätt:

* Begär Anden om att hämta grupper anger att attributet members ska uteslutas från alla resurser som tillhandahålls som svar på begäran.  
* Förfrågningar att avgöra om ett referensattribut har ett visst värde är begäranden om attributet medlemmar.  

![visar grupp etablering och avetablerings ordning](./media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Grupp etablering och avetablerings ordning*

### <a name="scim-protocol-requests-and-responses"></a>Begär Anden och svar för SCIM-protokoll
Det här avsnittet innehåller exempel på SCIM-begäranden som har genererats av Azure AD SCIM-klienten och exempel på förväntade svar. För bästa resultat bör du koda appen för att hantera dessa begär anden i det här formatet och generera förväntade svar.

> [!IMPORTANT]
> Information om hur och när Azure AD-tjänsten för användar etablering avger de åtgärder som beskrivs nedan finns i avsnittet [etablerings cykler: initial och stegvisa](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) [anvisningar för hur etablering fungerar](../app-provisioning/how-provisioning-works.md).

[Användar åtgärder](#user-operations)
  - [Skapa användare](#create-user) ([begäran](#request) / [svar](#response))
  - [Hämta användare](#get-user) ([](#request-1) svar / [svar](#response-1))
  - [Hämta användare efter fråga](#get-user-by-query) ([begäran](#request-2) / [svar](#response-2))
  - [Hämta användare efter fråga – noll resultat](#get-user-by-query---zero-results) ([begär](#request-3)
/ [svar](#response-3))
  - [Uppdatera användare [Egenskaper för flera värden]](#update-user-multi-valued-properties) ([begär](#request-4) /  [svar](#response-4))
  - [Uppdatera användare [Egenskaper för enstaka värde]](#update-user-single-valued-properties) ([begär](#request-5)
/ [svar](#response-5)) 
  - [Inaktivera användare](#disable-user) ([begär](#request-14) / 
[svar](#response-14))
  - [Ta bort användare](#delete-user) ([begäran](#request-6) / 
[svar](#response-6))


[Grupp åtgärder](#group-operations)
  - [Skapa grupp](#create-group) ( [begäran](#request-7) / [svar](#response-7))
  - [Hämta grupp](#get-group) ( [begäran](#request-8) / [svar](#response-8))
  - [Hämta grupp efter DisplayName](#get-group-by-displayname) ([begäran](#request-9) / [svar](#response-9))
  - [Uppdaterings grupp [attribut för icke-medlem]](#update-group-non-member-attributes) ([begäran](#request-10) /
 [svar](#response-10))
  - [Uppdaterings grupp [Lägg till medlemmar]](#update-group-add-members) ( [begäran](#request-11) /
[svar](#response-11))
  - [Uppdaterings grupp [ta bort medlemmar]](#update-group-remove-members) ( [begäran](#request-12) /
[svar](#response-12))
  - [Ta bort grupp](#delete-group) ([begäran](#request-13) /
[svar](#response-13))

### <a name="user-operations"></a>Användar åtgärder

* Användare kan frågas genom att `userName` eller `email[type eq "work"]` attribut.  

#### <a name="create-user"></a>Skapa användare

###### <a name="request"></a>Förfrågan

*POST-/users*
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

*HTTP/1.1 201 har skapats*
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

###### <a name="request-1"></a>Anmoda
*Hämta/Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Svar (användaren hittades)
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
*Hämta/Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Svar (användaren hittades inte. Observera att detalj nivån inte är obligatorisk, endast status.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Hämta användare efter fråga

##### <a name="request-2"></a>Anmoda

*Hämta/Users? filter = userName EQ "Test_User_dfeef4c5-5681 -4387-b016-bdf221e82081"*

##### <a name="response-2"></a>Svarade

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

#### <a name="get-user-by-query---zero-results"></a>Hämta användare från fråga – noll resultat

##### <a name="request-3"></a>Anmoda

*Hämta/Users? filter = userName EQ "obefintlig användare"*

##### <a name="response-3"></a>Svarade

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

#### <a name="update-user-multi-valued-properties"></a>Uppdatera användare [Egenskaper för flera värden]

##### <a name="request-4"></a>Anmoda

*KORRIGERING/Users/6764549bef60420686bc HTTP/1.1*
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

##### <a name="response-4"></a>Svarade

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

#### <a name="update-user-single-valued-properties"></a>Uppdatera användare [Egenskaper för enstaka värde]

##### <a name="request-5"></a>Anmoda

*KORRIGERING/Users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response-5"></a>Svarade

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

##### <a name="request-14"></a>Anmoda

*KORRIGERING/Users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response-14"></a>Svarade

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

##### <a name="request-6"></a>Anmoda

*TA bort/users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Svarade

*HTTP/1.1 204 inget innehåll*

### <a name="group-operations"></a>Grupp åtgärder

* Grupper ska alltid skapas med en lista med tomma medlemmar.
* Grupper kan frågas av `displayName`-attributet.
* Uppdateringen av grupp PATCH-begäran ska ge ett *HTTP 204-innehåll* i svaret. Att returnera en brödtext med en lista över alla medlemmar är inte lämpligt.
* Det är inte nödvändigt att stödja att returnera alla medlemmar i gruppen.

#### <a name="create-group"></a>Skapa grupp

##### <a name="request-7"></a>Anmoda

*PUBLICERA/Groups HTTP/1.1*
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

##### <a name="response-7"></a>Svarade

*HTTP/1.1 201 har skapats*
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

##### <a name="request-8"></a>Anmoda

*Hämta/Groups/40734ae655284ad3abcc? excludedAttributes = medlemmar HTTP/1.1*

##### <a name="response-8"></a>Svarade
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

#### <a name="get-group-by-displayname"></a>Hämta grupp efter displayName

##### <a name="request-9"></a>Anmoda
*Hämta/Groups? excludedAttributes = members & filter = displayName EQ "HTTP/1.1*

##### <a name="response-9"></a>Svarade

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

#### <a name="update-group-non-member-attributes"></a>Uppdaterings grupp [attribut för icke-medlem]

##### <a name="request-10"></a>Anmoda

*KORRIGERING/Groups/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response-10"></a>Svarade

*HTTP/1.1 204 inget innehåll*

### <a name="update-group-add-members"></a>Uppdatera grupp [Lägg till medlemmar]

##### <a name="request-11"></a>Anmoda

*KORRIGERING/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response-11"></a>Svarade

*HTTP/1.1 204 inget innehåll*

#### <a name="update-group-remove-members"></a>Uppdaterings grupp [ta bort medlemmar]

##### <a name="request-12"></a>Anmoda

*KORRIGERING/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response-12"></a>Svarade

*HTTP/1.1 204 inget innehåll*

#### <a name="delete-group"></a>Ta bort grupp

##### <a name="request-13"></a>Anmoda

*TA bort/Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>Svarade

*HTTP/1.1 204 inget innehåll*

## <a name="step-3-build-a-scim-endpoint"></a>Steg 3: Bygg en SCIM-slutpunkt

Genom att skapa en SCIM-webb tjänst som gränssnitt med Azure Active Directory, kan du aktivera automatisk användar etablering för i princip alla program eller identitets lager.

Så fungerar här det:

1. Azure AD tillhandahåller ett CLI-bibliotek (Common Language Infrastructure) med namnet Microsoft. SystemForCrossDomainIdentityManagement, som ingår i kod exemplen beskrivs nedan. System integrerare och utvecklare kan använda det här biblioteket för att skapa och distribuera en SCIM webb tjänst slut punkt som kan ansluta Azure AD till ett programs identitets lager.
2. Mappningar implementeras i webbtjänsten för att mappa det standardiserade användarschemat till användarschemat och protokoll som krävs av programmet. 
3. Slutpunkts-URL är registrerad i Azure AD som en del av ett anpassat program i programgalleriet.
4. Användare och grupper har tilldelats det här programmet i Azure AD. Vid tilldelning placeras de i en kö som ska synkroniseras till mål programmet. Hantering av kön synkroniseringsprocessen körs varje 40 minuter.

### <a name="code-samples"></a>Kodexempel

För att göra den här processen enklare, tillhandahålls [kod exempel](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) , som skapar en scim-webbtjänst-slutpunkt och demonstrerar automatisk etablering. Exemplet är en provider som upprätthåller en fil med rader som är kommaavgränsade värden som representerar användare och grupper.

**Förutsättningar**

* Visual Studio 2013 eller senare
* [Azure SDK för .NET](https://azure.microsoft.com/downloads/)
* Windows dator som har stöd för ASP.NET framework 4.5 som ska användas som SCIM-slutpunkt. Den här datorn måste vara tillgänglig från molnet.
* [En Azure-prenumeration med en utvärderings version eller licensierad version av Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Komma igång

Det enklaste sättet att implementera en SCIM-slutpunkt som kan ta emot etableringsbegäranden från Azure AD är att skapa och distribuera kodexemplet som visar de etablerade användarna till en fil med kommaavgränsade värden (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Skapa en exempel SCIM-slutpunkt

1. Ladda ned kod exempel paketet på [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Packa upp paketet och placera den på din Windows-dator på en plats, till exempel C:\AzureAD-BYOA-Provisioning-Samples\.
1. Starta FileProvisioning\Host\FileProvisioningService.csproj-projekt i Visual Studio i den här mappen.
1. Välj **verktyg** > **NuGet Package Manager** > **Package Manager-konsolen**och kör följande kommandon för FileProvisioningService-projektet för att lösa lösnings referenserna:

   ```powershell
    Update-Package -Reinstall
   ```

1. Skapa FileProvisioningService-projektet.
1. Starta kommando tolken i Windows (som administratör) och Använd **CD-** kommandot för att ändra katalogen till **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug** -mappen.
1. Kör följande kommando och ersätt `<ip-address>` med IP-adressen eller domän namnet för Windows-datorn:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. I Windows under **Windows-inställningar** > **nätverks & Internet inställningar**väljer du **Windows-brandväggen** > **Avancerade inställningar**och skapar en **regel för inkommande trafik** som tillåter inkommande åtkomst till port 9000.
1. Om Windows-datorn ligger bakom en router måste routern konfigureras för att köra översättning av nätverks åtkomst mellan port 9000 som exponeras för Internet och port 9000 på Windows-datorn. Den här konfigurationen krävs för att Azure AD ska få åtkomst till den här slut punkten i molnet.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Registrera exempel SCIM-slutpunkten i Azure AD

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com). 
1. Välj **företags program** i det vänstra fönstret. En lista över alla konfigurerade appar visas, inklusive appar som har lagts till från galleriet.
1. Välj **+ ny program** > **alla** > **icke-galleriprogram**.
1. Ange ett namn för ditt program och välj **Lägg till** för att skapa ett app-objekt. Det programobjekt som skapas är avsedd att representera målappen du skulle etablering för och implementera enkel inloggning för och inte bara SCIM-slutpunkten.
1. På skärmen hantering av appar väljer du **etablering** i den vänstra panelen.
1. I menyn **etablerings läge** väljer du **Automatisk**.    
1. I fältet **klient-URL** anger du URL: en för programmets scim-slutpunkt. Exempel: https://api.contoso.com/scim/

1. Om SCIM-slutpunkten kräver en OAuth Bearer-token från en annan utfärdare än Azure AD kopierar du den obligatoriska OAuth Bearer-token till fältet valfritt **hemligt token** . Om det här fältet lämnas tomt innehåller Azure AD en OAuth Bearer-token som utfärdats från Azure AD med varje begäran. Appar som använder Azure AD som en identitetsprovider kan verifiera den här Azure AD-utfärdade token.
1. Välj **test anslutning** för att Azure Active Directory försöka ansluta till scim-slutpunkten. Om försöket Miss lyckas visas fel information.  

    > [!NOTE]
    > **Test anslutning** frågar scim-slutpunkten för en användare som inte finns med ett slumpmässigt GUID som den matchande egenskap som valts i Azure AD-konfigurationen. Det förväntade korrekta svaret är HTTP 200 OK med ett tomt SCIM ListResponse-meddelande

1. Om försöket att ansluta till programmet lyckas väljer du **Spara** för att spara administratörens autentiseringsuppgifter.
1. I avsnittet **mappningar** finns det två valbara uppsättningar av mappningar för attribut: en för användar objekt och en för grupp objekt. Välj var och en att granska de attribut som synkroniseras från Azure Active Directory till din app. Attributen som väljs som **matchande** egenskaper används för att matcha användare och grupper i appen för uppdaterings åtgärder. Välj **Spara** för att genomföra ändringarna.
1. Under **Inställningar**definierar fältet **omfattning** vilka användare och eller grupper som ska synkroniseras. Välj **"synkronisera endast tilldelade användare och grupper** (rekommenderas) för att endast synkronisera användare och grupper som tilldelats på fliken **användare och grupper** .
1. När konfigurationen är klar ställer du in **etablerings statusen** **på på**.
1. Välj **Spara** för att starta Azure AD Provisioning-tjänsten.
1. Om du bara synkroniserar tilldelade användare och grupper (rekommenderas), måste du välja fliken **användare och grupper** och tilldela de användare eller grupper som du vill synkronisera.

När den första cykeln har startat kan du välja **gransknings loggar** i den vänstra panelen för att övervaka förloppet, vilket visar alla åtgärder som utförs av etablerings tjänsten i din app. Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](check-status-user-account-provisioning.md).

Det sista steget i att verifiera exemplet är att öppna filen TargetFile.csv i mappen \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug på din Windows-dator. När etableringen har körts, innehåller den här filen information om alla tilldelade och etablerat användare och grupper.

### <a name="development-libraries"></a>Utvecklingsbibliotek

För att utveckla dina egna webbtjänst som överensstämmer med SCIM-specifikationen först du med att bekanta dig med följande bibliotek som tillhandahålls av Microsoft för att hjälpa dig att påskynda utvecklingen:

* Vanliga språk infrastruktur (CLI) bibliotek erbjuds för användning med språk baserat på den infrastrukturen, till exempel C#. Ett av dessa bibliotek, Microsoft. SystemForCrossDomainIdentityManagement. service, deklarerar ett gränssnitt, Microsoft. SystemForCrossDomainIdentityManagement. IProvider, som visas i följande bild. En utvecklare som använder-biblioteken implementerar gränssnittet med en klass som kan kallas generiskt som en provider. Med biblioteken kan utvecklaren distribuera en webb tjänst som följer SCIM-specifikationen. Webb tjänsten kan antingen ligga inom Internet Information Services eller en körbar CLI-sammansättning. Begäran översätts till anrop till leverantörens metoder som skulle programmeras av utvecklaren att fungera på vissa identitetsarkiv.
  
   ![Detalj nivå: en begäran har översatts till anrop till providerns metoder](./media/use-scim-to-provision-users-and-groups/scim-figure-3.png)
  
* [Express Route-hanterare](https://expressjs.com/guide/routing.html) är tillgängliga för parsning av Node. js-begär ande objekt som representerar anrop (enligt definitionen i scim-specifikationen) som görs till en Node. js-webb tjänst.

### <a name="building-a-custom-scim-endpoint"></a>Skapa en anpassad SCIM-slutpunkt

Utvecklare som använder CLI-biblioteken kan vara värdar för sina tjänster inom valfri körbar CLI-sammansättning eller inom Internet Information Services. Här är exempel kod för att vara värd för en tjänst i en körbar sammansättning, på adressen http://localhost:9000: 

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

```
netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}
```

Här är värdet som angetts för argumentet certhash tumavtrycket för certifikatet, medan värdet som angetts för argumentet appid är ett valfritt globalt unikt ID.  

För att vara värd för tjänsten inom Internet Information Services skapar en utvecklare en CLI-kod biblioteks sammansättning med en klass med namnet start i standard namn området för sammansättningen.  Här är ett exempel på sådana en klass: 

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

Begäranden från Azure Active Directory omfattar en OAuth 2.0-ägartoken.   Alla tjänster som tar emot begäran ska autentisera utfärdaren som Azure Active Directory för den förväntade Azure Active Directory klienten, för åtkomst till Azure Active Directory Graph-webbtjänsten.  I token identifieras utfärdaren av ett ISS-anspråk, till exempel "ISS": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  I det här exemplet är bas adressen för anspråks värdet https://sts.windows.net, identifierar Azure Active Directory som utfärdare, medan det relativa adress segmentet cbb1a5ac-f33b-45fa-9bf5-f37db0fed422 är en unik identifierare för den Azure Active Directory klient organisation som token utfärdades för. Mål gruppen för token är programmets mall-ID för appen i galleriet. Programmets mall-ID för alla anpassade appar är 8adf8e6e-67b2-4cf2-a259-e3dc5476c621. Programmets mall-ID för varje app i galleriet varierar. Kontakta ProvisioningFeedback@microsoft.com för att få frågor om Programmall-ID för ett galleri program. Varje program som registreras i en enda klient organisation kan få samma `iss`-anspråk med SCIM-begäranden.

Utvecklare som använder CLI-biblioteken från Microsoft för att skapa en SCIM-tjänst kan autentisera begär Anden från Azure Active Directory med hjälp av Microsoft. OWIN. Security. ActiveDirectory-paketet genom att följa dessa steg: 

Först i en provider implementerar du egenskapen Microsoft. SystemForCrossDomainIdentityManagement. IProvider. StartupBehavior genom att låta den returnera en metod som anropas när tjänsten startas: 

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

Lägg sedan till följande kod i den metoden för att få en begäran till någon av tjänstens slut punkter som autentiserats som en token som utfärdats av Azure Active Directory för en angiven klient, för åtkomst till Azure AD Graph-webbtjänsten: 

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
        ValidAudience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621"
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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Hantera etablering och avetablering av användare

***Exempel 1. Fråga tjänsten om en matchande användare***

Azure Active Directory frågar tjänsten för en användare med ett externalId-attributvärde som matchar mailNickname-attributvärdet för en användare i Azure AD. Frågan uttrycks som en Hypertext Transfer Protocol (HTTP)-begäran, till exempel det här exemplet, där jyoung är ett exempel på ett smek namn för en användare i Azure Active Directory.

>[!NOTE]
> Detta är endast ett exempel. Alla användare får inte ett smek namn-attribut och värdet som en användare har kanske inte är unikt i katalogen. Dessutom kan attributet som används för matchning (som i det här fallet vara externalId) konfigureras i [mappningar för Azure AD-attribut](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

Om tjänsten har skapats med hjälp av CLI-biblioteken som tillhandahålls av Microsoft för att implementera SCIM-tjänster, översätts begäran till ett anrop till tjänst leverantörens fråge metod.  Här är signaturen för metoden: 

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
* parametrar. AlternateFilters.Count: 1
* parametrar. AlternateFilters.ElementAt(0). AttributePath: ”externalId”
* parametrar. AlternateFilters.ElementAt(0). Jämförelseoperator: ComparisonOperator.Equals
* parametrar. AlternateFilter.ElementAt(0). ComparisonValue: ”jyoung”
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin. RequestId ”] 

***Exempel 2. Etablera en användare***

Om svaret på en fråga till webb tjänsten för en användare med ett externalId-attributvärde som matchar värdet för ett värde för en användares attributvärde inte returnerar några användare, kommer Azure Active Directory begär Anden som tjänsten etablerar en användare som motsvarar den i Azure Active Directory.  Här är ett exempel på en sådan begäran: 

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

CLI-biblioteken som tillhandahålls av Microsoft för att implementera SCIM-tjänster översätter denna begäran till ett anrop till metoden Create för tjänstens Provider.  Skapa-metoden har den här signaturen:

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
   Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
   string correlationIdentifier);
```

Värdet för argumentet resurs är en instans av Microsoft.SystemForCrossDomainIdentityManagement i en begäran att etablera en användare. Core2EnterpriseUser klassen, som definieras i Microsoft.SystemForCrossDomainIdentityManagement.Schemas-biblioteket.  Om begäran att etablera användaren lyckas, förväntas implementeringen av metoden för att returnera en instans av Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser klass med värdet för egenskapen ID angetts till den unika identifieraren för den nyligen etablerade användaren.  

***Exempel 3. Fråga efter användarens aktuella tillstånd*** 

Om du vill uppdatera en användare som är kända i en identitetsarkiv fronted genom en SCIM fortsätter Azure Active Directory genom att begära det aktuella tillståndet för den användaren från tjänsten med en begäran som: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

I en tjänst som skapats med CLI-biblioteken från Microsoft för att implementera SCIM-tjänster, översätts begäran till ett anrop till tjänst leverantörens hämtnings metod.  Här är signaturen för hämta-metoden:

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
  
* ID: ”54D382A4-2050-4C03-94D1-E769F1D15682”
* SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”

***Exempel 4. Fråga värdet för ett referens-attribut som ska uppdateras*** 

Om ett referens-attribut ska uppdateras, så Azure Active Directory frågar tjänsten om det aktuella värdet för referensvärdet i identitets arkivet som är baserat på tjänsten redan matchar värdet för attributet i Azure Active Katalogen. För användare är det enda attributet som efterfrågas det aktuella värdet på det här sättet manager-attribut. Här är ett exempel på en begäran för att avgöra om attributet manager för en viss användare-objektet har ett visst värde: 

Om tjänsten har skapats med hjälp av CLI-biblioteken som tillhandahålls av Microsoft för att implementera SCIM-tjänster, översätts begäran till ett anrop till tjänst leverantörens fråge metod. Värdet för egenskaperna för objektet som tillhandahålls som värde för argumentet parametrar är följande: 
  
* parametrar. AlternateFilters.Count: 2
* parametrar. AlternateFilters.ElementAt(x). AttributePath: ”ID”
* parametrar. AlternateFilters.ElementAt(x). Jämförelseoperator: ComparisonOperator.Equals
* komponentparametrar. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parametrar. AlternateFilters.ElementAt(y). AttributePath: ”manager”
* parametrar. AlternateFilters.ElementAt(y). Jämförelseoperator: ComparisonOperator.Equals
* komponentparametrar. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453A-919d-413861904646"
* parametrar. RequestedAttributePaths.ElementAt(0): ”ID”
* parametrar. SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”

Här kan värdet för index x vara 0 och värdet för indexet y kan vara 1 eller värdet för x kan vara 1 och värdet för y kan vara 0, beroende på ordningen på uttrycken för filter fråge parametern.   

***Exempel 5. Begär från Azure AD till en SCIM-tjänst för att uppdatera en användare*** 

Här är ett exempel på en begäran från Azure Active Directory till en SCIM-tjänsten för att uppdatera en användare: 

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
  
* ResourceIdentifier.Identifier: ”54D382A4-2050-4C03-94D1-E769F1D15682”
* ResourceIdentifier.SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”
* (PatchRequest som PatchRequest2). Operations.Count: 1
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). Path.AttributePath: ”manager”
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.Count: 1
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referens: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest som PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Värde: 2819c223-7f76-453a-919d-413861904646

***Exempel 6. Avetablera en användare***

För att avetablera en användare från ett identitets lager med en SCIM-tjänst skickar Azure AD en begäran som:

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

Det tillhandahållna objektet som värde för argumentet resourceIdentifier har följande egenskaps värden i exemplet på en begäran om att avetablera en användare: 

* ResourceIdentifier.Identifier: ”54D382A4-2050-4C03-94D1-E769F1D15682”
* ResourceIdentifier.SchemaIdentifier: ”urn: ietf:params:scim:schemas:extension:enterprise:2.0:User”

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Steg 4: integrera din SCIM-slutpunkt med Azure AD SCIM-klienten

Azure AD kan konfigureras att automatiskt etablera tilldelade användare och grupper till program som implementerar en särskild profil för [SCIM 2,0-protokollet](https://tools.ietf.org/html/rfc7644). Information om profilen beskrivs i [steg 2: förstå Azure AD scim-implementeringen](#step-2-understand-the-azure-ad-scim-implementation).

Kontrollera med din leverantör av program eller ditt program leverantörens dokumentation för instruktioner för kompatibilitet med dessa krav.

> [!IMPORTANT]
> Azure AD SCIM-implementeringen bygger på Azure AD-tjänsten för användar etablering, som är utformad för att ständigt hålla användarna synkroniserade mellan Azure AD och mål programmet och implementerar en mycket specifik uppsättning standard åtgärder. Det är viktigt att förstå dessa beteenden för att förstå beteendet för Azure AD SCIM-klienten. Mer information finns i avsnittet [etablerings cykler: initial och stegvisa](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) [anvisningar för hur etablering fungerar](../app-provisioning/how-provisioning-works.md).

### <a name="getting-started"></a>Komma igång

Program som stöder SCIM-profilen som beskrivs i den här artikeln kan anslutas till Azure Active Directory med hjälp av funktionen ”icke-galleriprogram” i Azure AD-programgalleriet. När du är ansluten, kör Azure AD en synkroniseringsprocess varje 40 minuter där den frågar programmets SCIM-slutpunkten för tilldelade användare och grupper, och skapar eller ändrar dem enligt tilldelningsinformation.

**Så här ansluter du ett program som stöder SCIM:**

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com). Observera att du kan få åtkomst till en kostnads fri utvärderings version av Azure Active Directory med P2-licenser genom att registrera dig för [programmet för utvecklare](https://developer.microsoft.com/office/dev-program)
2. Välj **företags program** i det vänstra fönstret. En lista över alla konfigurerade appar visas, inklusive appar som har lagts till från galleriet.
3. Välj **+ ny program** > **alla** > **icke-galleriprogram**.
4. Ange ett namn för ditt program och välj **Lägg till** för att skapa ett app-objekt. Den nya appen läggs till i listan över företags program och öppnas på sidan för hantering av appar.

   ![skärm bild som visar Azure AD-programgalleriet](./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Program Galleri för Azure AD*

5. På skärmen hantering av appar väljer du **etablering** i den vänstra panelen.
6. I menyn **etablerings läge** väljer du **Automatisk**.

   ![exempel: etablerings sidan för en app i Azure Portal](./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurera etablering i Azure Portal*

7. I fältet **klient-URL** anger du URL: en för programmets scim-slutpunkt. Exempel: https://api.contoso.com/scim/
8. Om SCIM-slutpunkten kräver en OAuth Bearer-token från en annan utfärdare än Azure AD kopierar du den obligatoriska OAuth Bearer-token till fältet valfritt **hemligt token** . Om det här fältet lämnas tomt innehåller Azure AD en OAuth Bearer-token som utfärdats från Azure AD med varje begäran. Appar som använder Azure AD som en identitetsprovider kan verifiera den här Azure AD-utfärdade token. 
   > [!NOTE]
   > Vi rekommenderar ***inte*** att du lämnar det här fältet tomt och förlitar sig på en token som genereras av Azure AD. Det här alternativet är i första hand tillgängligt i test syfte.
9. Välj **test anslutning** för att Azure Active Directory försöka ansluta till scim-slutpunkten. Om försöket Miss lyckas visas fel information.  

    > [!NOTE]
    > **Test anslutning** frågar scim-slutpunkten för en användare som inte finns med ett slumpmässigt GUID som den matchande egenskap som valts i Azure AD-konfigurationen. Det förväntade korrekta svaret är HTTP 200 OK med ett tomt SCIM ListResponse-meddelande.

10. Om försöket att ansluta till programmet lyckas väljer du **Spara** för att spara administratörens autentiseringsuppgifter.
11. I avsnittet **mappningar** finns det två valbara uppsättningar av [mappningar för attribut](customize-application-attributes.md): en för användar objekt och en för grupp objekt. Välj var och en att granska de attribut som synkroniseras från Azure Active Directory till din app. Attributen som väljs som **matchande** egenskaper används för att matcha användare och grupper i appen för uppdaterings åtgärder. Välj **Spara** för att genomföra ändringarna.

    > [!NOTE]
    > Du kan också inaktivera synkronisering av gruppobjekt genom att inaktivera ”grupper”-mappning.

12. Under **Inställningar**definierar fältet **omfattning** vilka användare och grupper som ska synkroniseras. Välj **Synkronisera endast tilldelade användare och grupper** (rekommenderas) om du bara vill synkronisera användare och grupper som tilldelats på fliken **användare och grupper** .
13. När konfigurationen är klar ställer du in **etablerings statusen** **på på**.
14. Välj **Spara** för att starta Azure AD Provisioning-tjänsten.
15. Om du bara synkroniserar tilldelade användare och grupper (rekommenderas), måste du välja fliken **användare och grupper** och tilldela de användare eller grupper som du vill synkronisera.

När den första cykeln har startats kan du välja **etablerings loggar** i den vänstra panelen för att övervaka förloppet, vilket visar alla åtgärder som utförs av etablerings tjänsten i din app. Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](check-status-user-account-provisioning.md).

> [!NOTE]
> Den första cykeln tar längre tid att utföra än senare synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Steg 5: publicera ditt program i Azure AD-programgalleriet

Om du skapar ett program som ska användas av fler än en klient kan du göra det tillgängligt i Azure AD-programgalleriet. Detta gör det enkelt för organisationer att identifiera programmet och konfigurera etablering. Det är enkelt att publicera din app i Azure AD-galleriet och göra etableringen tillgänglig för andra. Kolla in stegen [här](../develop/howto-app-gallery-listing.md). Microsoft kommer att samar beta med dig för att integrera ditt program i vårt galleri, testa din slut punkt och publicera onboarding- [dokumentation](../saas-apps/tutorial-list.md) för kunder att använda. 


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Auktorisering för etablering av anslutningar i program galleriet
SCIM-specifikationen definierar inte ett SCIM schema för autentisering och auktorisering. Den förlitar sig på användningen av befintliga bransch standarder. Azure AD Provisioning-klienten har stöd för två autentiseringsmetoder för program i galleriet. 

|Verifieringsmetod|Experter|Nackdelar|Support|
|--|--|--|--|
|Användar namn och lösen ord (rekommenderas inte eller stöds inte av Azure AD)|Lätt att implementera|Osäker – [din pa $ $Word spelar ingen roll](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Stöds från fall till fall för Galleri-appar. Stöds inte för appar som inte är gallerier.|
|Token med lång livs längd (stöds av Azure AD för närvarande)|Token för lång livs längd kräver inte att en användare finns. De är enkla för administratörer att använda vid konfigurations etablering.|Token för lång livs längd kan vara svårt att dela med en administratör utan att använda oskyddade metoder, till exempel e-post. |Stöds för Galleri-och appar som inte är gallerier. |
|Bevilja OAuth-auktoriseringskod (stöds av Azure AD för närvarande)|Åtkomst-token är mycket kortare än lösen ord och har en automatiserad uppdaterings funktion som används av token med lång livs längd.  En riktig användare måste närvara vid den första auktoriseringen och lägga till en ansvars nivå. |Kräver att en användare är närvarande. Om användaren lämnar organisationen är token ogiltig och auktoriseringen måste slutföras igen.|Stöds för Galleri-appar. Stöd för icke-Gallery-appar pågår.|
|Beviljande av OAuth-klientautentiseringsuppgifter (stöds inte i vår översikt)|Åtkomst-token är mycket kortare än lösen ord och har en automatiserad uppdaterings funktion som används av token med lång livs längd. Både behörigheten Tillåt och klientens autentiseringsuppgifter skapar samma typ av åtkomsttoken, så att flytta mellan dessa metoder är transparent för API: et.  Etableringen kan vara helt automatiserad och nya token kan utföras tyst utan användar interaktion. ||Stöds inte för Galleri-och appar som inte är gallerier. Support finns i vår efter släpning.|

**Utfärdande flöde för OAuth-auktoriseringskod:** Etablerings tjänsten har stöd för [beviljande av auktoriseringskod](https://tools.ietf.org/html/rfc6749#page-24). När du har skickat din begäran om att publicera din app i galleriet, kommer vårt team att samar beta med dig för att samla in följande information:
*  URL för auktorisering: en URL som klienten kan använda för att få behörighet från resurs ägaren via omdirigering av användar agent. Användaren omdirigeras till denna URL för att ge åtkomst. 
*  URL för token Exchange: en URL till klienten som utbyter en auktorisering för en åtkomsttoken, vanligt vis med klientautentisering.
*  Klient-ID: auktoriseringsservern utfärdar den registrerade klienten ett klient-ID, vilket är en unik sträng som representerar den registrerings information som tillhandahålls av klienten.  Klient-ID: n är inte en hemlighet. den exponeras för resurs ägaren och **får inte** användas separat för klientautentisering.  
*  Klient hemlighet: klient hemligheten är en hemlighet som genereras av auktoriseringsservern. Det bör vara ett unikt värde som endast är känt för auktoriseringsservern. 

Observera att OAuth v1 inte stöds på grund av exponering av klient hemligheten. OAuth v2 stöds.  

Metod tips (rekommenderas men krävs inte):
* Stöder flera omdirigerings-URL: er. Administratörer kan konfigurera etablering från både "portal.azure.com" och "aad.portal.azure.com". Genom att stödja flera omdirigerings-URL: er ser du till att användarna kan auktorisera åtkomst från portalen.
* Stöd för flera hemligheter för att säkerställa en smidig hemlig förnyelse utan drift avbrott. 

**Långa OAuth-token för OAuth-förlängd:** Om ditt program inte stöder OAuth-auktoriseringsvärdet för OAuth-auktoriseringskod kan du också generera en lång livs längd för OAuth Bearer-token som en administratör kan använda för att konfigurera etablerings integrationen. Token ska vara beständig, annars placeras etablerings jobbet i [karantän](application-provisioning-quarantine-status.md) när token upphör att gälla. Denna token måste vara lägre 1 KB i storlek.  

Om du vill ha ytterligare metoder för autentisering och auktorisering kan du berätta för oss på [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Tillåt IP-adresser som används av Azure AD Provisioning-tjänsten för att göra SCIM-begäranden

Vissa appar tillåter inkommande trafik till appen. För att Azure AD Provisioning-tjänsten ska fungera som förväntat måste de IP-adresser som används vara tillåtna. En lista över IP-adresser för varje service tag/region finns i JSON-filen – [Azure IP-intervall och service märken – offentligt moln](https://www.microsoft.com/download/details.aspx?id=56519). Du kan hämta och program mera dessa IP-adresser i brand väggen efter behov. Du hittar de reserverade IP-intervallen för Azure AD-etablering under "AzureActiveDirectoryDomainServices".

## <a name="related-articles"></a>Relaterade artiklar

* [Automatisera användar etablering och avetablering för SaaS-appar](user-provisioning.md)
* [Anpassa mappningar av attribut för användar etablering](customize-application-attributes.md)
* [Skriver uttryck för mappningar av attribut](../app-provisioning/functions-for-customizing-application-data.md)
* [Omfångs filter för användar etablering](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
* [Meddelanden om konto etablering](user-provisioning.md)
* [Lista över självstudier om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)
