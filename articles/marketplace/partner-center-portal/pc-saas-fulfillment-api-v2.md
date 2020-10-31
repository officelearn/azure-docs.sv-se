---
title: 'API: er för SaaS-utförande v2 i Microsofts kommersiella marknads platser'
description: 'Lär dig hur du skapar och hanterar ett SaaS-erbjudande på Microsoft AppSource och Azure Marketplace genom att använda API: erna version 2.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 06a2a5bbe637cd2366dbdf218c0278cd683635df
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130042"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>SaaS-API version 2 på den kommersiella marknaden

Den här artikeln beskriver de API: er som gör det möjligt för partner att sälja sina SaaS-erbjudanden i Microsoft AppSource och Azure Marketplace. En utgivare krävs för att implementera integrering med dessa API: er för att publicera ett SaaS-erbjudande som går att använda i Partner Center.

## <a name="managing-the-saas-subscription-life-cycle"></a>Hantera livs cykeln för SaaS-prenumeration

Den kommersiella Marketplace hanterar hela livs cykeln för en SaaS-prenumeration efter köpet av slut kunden.  Den använder landnings sidan, API: er för utförande, åtgärds-API: er och webhook som en mekanism för att driva den faktiska aktiveringen och användningen av SaaS-prenumerationer, uppdateringar och prenumerations avbrott.  Slut kundens faktura baseras på den SaaS-prenumeration som Microsoft underhåller. 

### <a name="states-of-a-saas-subscription"></a>Tillstånd för en SaaS-prenumeration

Tillstånden för en SaaS-prenumeration och tillämpliga åtgärder visas.

![Livs cykel för en SaaS-prenumeration i Marketplace](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Köpt men ännu inte aktive rad ( *PendingFulfillmentStart* )

När en slutanvändare (eller CSP) köper ett SaaS-erbjudande i Marketplace bör utgivaren meddelas om köpet så att ett nytt SaaS-konto skapas och konfigureras för slutanvändaren på utgivarens sida.

För att skapa konton:

1. Kunden måste klicka på knappen **Konfigurera** som är tillgänglig för ett SaaS-erbjudande efter det att köpet i Microsoft AppSource eller Azure Portal. Eller i e-postmeddelandet att kunden får kort efter köpet.
2. Sedan meddelar Microsoft partnern om köpet genom att öppna den nya webb sidan URL: en med parametern token (den kommersiella marknads platsen köpa identifierings-token).

Ett exempel på ett sådant anrop är `https://contoso.com/signup?token=<blob>` att URL: en för landnings sidan för det här SaaS-erbjudandet i Partner Center har kon figurer ATS som `https://contoso.com/signup` . Denna token tillhandahåller utgivaren ett ID som unikt identifierar SaaS inköp och kunden.

>[!NOTE]
>Utgivaren kommer inte att meddelas om SaaS-köpet förrän kunden har initierat konfigurations processen från Microsoft-sidan.

Landnings sidans URL måste vara igång dygnet runt och redo att ta emot nya samtal från Microsoft hela tiden. Om landnings sidan blir otillgänglig kan kunderna inte registrera sig för SaaS-tjänsten och börja använda den.

Sedan måste *token* skickas tillbaka till Microsoft från utgivaren genom att anropa [SaaS lösnings-API](#resolve-a-purchased-subscription): t som värde för `x-ms-marketplace-token header` parametern header.  Till följd av anropet till API-anropet, utbyts token för information om SaaS-inköpet, till exempel unikt ID för köpet, inköpt erbjudande-ID, inköpt plan-ID osv.

På landnings sidan ska kunden vara inloggad på det nya eller befintliga SaaS-kontot via Azure Active Directory (Azure AD) enkel inloggning (SSO).

Utgivaren bör implementera SSO-inloggning för att tillhandahålla den användar upplevelse som krävs av Microsoft för det här flödet.  Se till att använda Azure AD-program med flera innehavare, Tillåt både arbets-och skol konton eller personliga Microsoft-konton när du konfigurerar SSO.  Detta krav gäller endast landnings sidan och för användare som omdirigeras till SaaS-tjänsten när de redan har loggat in med Microsoft-autentiseringsuppgifter. Den gäller inte för alla inloggningar till SaaS-tjänsten.

> [!NOTE]
>Om SSO-inloggning kräver att en administratör beviljar behörighet till en app, måste beskrivningen av erbjudandet i Partner Center avslöja att åtkomst på administratörs nivå krävs. Detta är att följa de [kommersiella certifierings principerna för marknaden](/legal/marketplace/certification-policies#10003-authentication-options).

När du har loggat in bör kunden fylla i SaaS-konfigurationen på utgivarens sida. Sedan måste utgivaren anropa [API för att aktivera prenumeration](#activate-a-subscription) för att skicka en signal till Marketplace om att etableringen av SaaS-kontot har slutförts.
Detta kommer att starta kundens fakturerings period. Om API-anropet för aktivering av prenumeration inte lyckas debiteras kunden inte för köpet.


![API-anrop för ett etablerings scenario](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Aktiv (prenumererad)

Det här läget är det stabila läget för en etablerad SaaS-prenumeration. När det [aktiva prenumerations-API](#activate-a-subscription) -anropet bearbetas på Microsoft-sidan markeras SaaS-prenumerationen som prenumererad. SaaS-tjänsten är nu redo att användas av kunden på utgivarens sida och kunden faktureras.

När SaaS-prenumerationen redan är aktiv och kunden väljer att starta **Hantera** SaaS-upplevelse från Azure Portal-eller M365 administrations Center, anropas **URL: en för landnings sidan** av Microsoft med parametern *token* , samma som i det aktiva flödet.  Utgivaren bör skilja mellan nya köp och hantering av befintliga SaaS-konton och hantera URL-anropet för landnings sidan.

#### <a name="being-updated-subscribed"></a>Uppdateras (prenumererar)

Den här åtgärden innebär att en uppdatering av en befintlig aktiv SaaS-prenumeration bearbetas av både Microsoft och utgivaren. En sådan uppdatering kan initieras av:

- kunden från den kommersiella marknads platsen.
- CSP: n från den kommersiella marknads platsen.
- kunden från utgivarens SaaS-webbplats (gäller inte för KRYPTOGRAFIPROVIDERs som har gjorts inköp).

Det finns två typer av uppdateringar för en SaaS-prenumeration:

- Uppdatera plan när kunden väljer ett annat abonnemang för prenumerationen.
- Uppdatera kvantitet när kunden ändrar antalet köpta platser för prenumerationen

Det går bara att uppdatera en aktiv prenumeration. När prenumerationen uppdateras är dess tillstånd fortfarande aktivt på Microsoft-sidan.

##### <a name="update-initiated-from-the-marketplace"></a>Uppdatering initierad från Marketplace

I det här flödet ändrar kunden prenumerations planen eller antalet platser från M365 administrations Center.  

1. När en uppdatering har angetts anropar Microsoft utgivarens webhook-URL, konfigurerad i **anslutningens webhook** -fält i Partner Center, med ett lämpligt värde för *åtgärder* och andra relevanta parametrar.  
1. Utgivarens sida bör göra nödvändiga ändringar i SaaS-tjänsten och meddela Microsoft när ändringen har slutförts genom att anropa [uppdaterings status för åtgärds-API](#update-the-status-of-an-operation).
1. Om korrigerings filen skickas med statusen Misslyckad, slutförs inte uppdaterings processen på Microsoft-sidan.  SaaS-prenumerationen kommer att lämnas med befintlig plan och antal platser.

Sekvensen med API-anrop för ett uppdaterings scenario som initieras av Marketplace visas nedan.

![API-anrop för en uppdatering av Marketplace som initierats](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>Uppdatering initierad från utgivaren

I det här flödet ändrar kunden prenumerations planen eller antalet platser som köpts från själva SaaS-tjänsten. 

1. Utgivar koden måste anropa API för [ändrings plan](#change-the-plan-on-the-subscription) och/eller [ändra antal API](#change-the-quantity-of-seats-on-the-saas-subscription) innan du gör den begärda ändringen på utgivarens sida. 

1. Microsoft kommer att tillämpa ändringen på prenumerationen och sedan meddela utgivaren via **anslutningens webhook** att tillämpa samma ändring.  

1. Sedan bör utgivaren göra den nödvändiga ändringen i SaaS-prenumerationen och meddela Microsoft när ändringen görs genom att anropa [uppdaterings status för åtgärds-API](#update-the-status-of-an-operation).

Sekvensen med API-anrop för det initierade uppdaterings scenariot vid publicerings sidan.

![API-anrop för en initierad uppdatering av Publisher-Sidan](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Pausad ( *inaktive* rad)

Det här läget anger att kundens betalning för SaaS-tjänsten inte har tagits emot. Utgivaren kommer att meddelas om den här ändringen i SaaS prenumerations status av Microsoft. Meddelandet görs via ett anrop till webhook med *Åtgärds* parametern inställd på *pausad* .

Utgivaren kan eventuellt göra ändringar i SaaS-tjänsten på utgivarens sida. Vi rekommenderar att utgivaren gör informationen tillgänglig för den inaktiverade kunden och begränsar eller blockerar kundens åtkomst till SaaS-tjänsten.  Det finns en sannolikhet att betalningen aldrig tas emot.

Microsoft ger kunden en 30-dagars Grace-period innan prenumerationen avbryts automatiskt. När en prenumeration är i vänte läge:

* SaaS-kontot måste behållas i ett återställnings Bart tillstånd av ISV. Du kan återställa fullständiga funktioner utan att förlora data eller inställningar.
* Vi förväntar dig att få en återskapande begäran för den här prenumerationen om betalningen tas emot under respitperioden, eller om en inetablerings förfrågan i slutet av grace-perioden sker, både via webhook-mekanismen.

Prenumerations tillståndet ändras till inaktive rad på Microsoft-sidan innan utgivaren vidtar åtgärder. Det går bara att pausa aktiva prenumerationer.

#### <a name="reinstated-suspended"></a>Återupprättad ( *inaktive* rad)

Prenumerationen återställs.

Den här åtgärden anger att kundens betalnings instrument blev giltigt igen och att en betalning görs för SaaS-prenumerationen.  Prenumerationen återställs. Om så är fallet: 

1. Microsoft anropar webhook med en *Åtgärds* parameter inställd på värdet *Återställ* .  
1. Utgivaren ser till att den här prenumerationen fungerar igen på utgivarens sida.
1. Utgivaren anropar [API: et för korrigerings åtgärden](#update-the-status-of-an-operation) med statusen lyckades.  
1. Sedan kommer återförsöket att lyckas och Kunden debiteras igen för SaaS-prenumerationen. 
1. Om korrigeringen skickas med statusen Misslyckad, slutförs inte återställnings processen på Microsoft-sidan. Prenumerationen är fortfarande inaktive rad.

Om korrigeringen skickas med statusen Misslyckad, slutförs inte återställnings processen på Microsoft-sidan.  Prenumerationen är fortfarande inaktive rad.

Endast en pausad prenumeration kan återställas.  När en SaaS-prenumeration återställs förblir tillståndet pausat.  När den här åtgärden har slutförts aktive ras prenumerationens status.

#### <a name="renewed-subscribed"></a>Förnyad ( *prenumererad* )

I slutet av prenumerations perioden (efter en månad eller ett år) förnyas SaaS-prenumerationen automatiskt av Microsoft.  Standardinställningen för automatisk förnyelse är *True* för alla SaaS-prenumerationer. Aktiva SaaS-prenumerationer fortsätter att förnyas med vanliga takt. Microsoft meddelar inte utgivaren när en prenumeration förnyas. En kund kan inaktivera automatisk förnyelse för en SaaS-prenumeration via M365-administrationskonsolen eller via Azure Portal.  I det här fallet avbryts SaaS-prenumerationen automatiskt när den aktuella fakturerings perioden är slut.  Kunder kan också avbryta SaaS-prenumerationen vid varje tidpunkt.

Endast aktiva prenumerationer förnyas automatiskt.  Prenumerationerna förblir aktiva under förnyelse processen och om automatisk förnyelse har slutförts.  Efter förnyelsen kommer start-och slutdatumen för prenumerations perioden att uppdateras till den nya termen datum.

Om en automatisk förnyelse Miss lyckas på grund av ett problem med betalningen kommer prenumerationen att inaktive ras.  Utgivaren kommer att meddelas.

#### <a name="canceled-unsubscribed"></a>Avbruten (avbruten av *prenumeration* ) 

Prenumerationerna når detta tillstånd som svar på antingen en explicit kund-eller CSP-åtgärd genom att avbryta en prenumeration från utgivarens webbplats, Azure Portal eller M365 administrations Center.  En prenumeration kan också avbrytas implicit, på grund av inbetalning av avgifter, efter att ha varit i pausat tillstånd i 30 dagar.

När du tar emot ett uppsägnings samtal för webhook bör utgivaren behålla kund information för återställning på begäran under minst sju dagar. Sedan kan kund information tas bort.

En SaaS-prenumeration kan avbrytas när som helst i dess livs cykel. En prenumeration kan inte återaktiveras när den har avbrutits.

## <a name="api-reference"></a>API-referens

I det här avsnittet dokumenteras API: er för SaaS-prenumeration och åtgärder.

**Prenumerations-API: er** ska användas för att hantera livs cykeln för SaaS-prenumeration från köpet till annullering.

**API: er för åtgärder** ska användas för att:

* verifiera och bekräfta de bearbetade webhook-anropen
* Hämta en lista över väntande program åtgärder som väntar på att godkännas av utgivaren

### <a name="enforcing-tls-12-note"></a>Framtvingar TLS 1,2-anteckning

TLS version 1,2-versionen kommer att verkställas snart den lägsta versionen för HTTPS-kommunikation. Se till att du använder den här TLS-versionen i din kod.  TLS version 1,0 och 1,1 kommer snart att bli föråldrad.

### <a name="subscription-apis"></a>Prenumerations-API: er

#### <a name="resolve-a-purchased-subscription"></a>Lösa en inköpt prenumeration

Med hjälp av matchnings slut punkten kan utgivaren byta identifierings-token för Marketplace (kallas *token* i [inköpt men ännu inte aktive rad](#purchased-but-not-yet-activated-pendingfulfillmentstart)) till ett beständigt inköpt SaaS prenumerations-ID och dess information.

När en kund omdirigeras till partnerns URL för landnings sidan skickas kundens ID-token som *token* -parameter i detta URL-anrop. Partnern förväntas använda denna token och göra en begäran om att lösa den. Matchnings-API-svaret innehåller prenumerations-ID: t för SaaS och annan information som unikt identifierar köpet. Den *token* som tillhandahålls med landnings SIDANs URL-anrop är vanligt vis giltig i 24 timmar. Om den *token* du tar emot redan har gått ut rekommenderar vi att du ger följande vägledning för slut kunden:

"Det gick inte att identifiera det här köpet, öppna SaaS-prenumerationen igen i Azure Portal eller i M365 administrations Center och klicka på knappen" Konfigurera konto "eller" hantera konto "igen."

Om du anropar lösnings-API returneras prenumerations information och status för SaaS-prenumerationer i alla status värden som stöds.

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Skicka`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Använd 2018-08-31.   |

*Begärandehuvuden:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  `x-ms-correlationid` |  Ett unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `authorization`     |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop. Formatet är `"Bearer <accessaccess_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post). |
|  `x-ms-marketplace-token`  | Parametern för att identifiera *token* för Marketplace-inköp.  Token skickas i landnings sidans URL-anrop när kunden omdirigeras till SaaS-partnerns webbplats (till exempel: `https://contoso.com/signup?token=<token><authorization_token>` ). <br> <br>  *Obs:* Det *token* -värde som kodas är en del av URL: en för landnings sidan och måste avkodas innan den används som en parameter i detta API-anrop.  <br> <br> Exempel på en kodad sträng i URL: en ser ut så här: `contoso.com/signup?token=ab%2Bcd%2Fef` , där token är `ab%2Bcd%2Fef` .  Samma token avkodad är: `Ab+cd/ef` |
| | |

*Svars koder:*

Kod: 200 returnerar unika prenumerations-ID: n för SaaS baserat på den `x-ms-marketplace-token` angivna.

Exempel på svars text:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Felkod: 400 Felaktig begäran. `x-ms-marketplace-token` saknas, är felaktigt, ogiltigt eller har upphört att gälla.

Kod: 403 förbjuden. Autentiseringstoken är ogiltig, förfallen eller har inte angetts.  Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som användes för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt.

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

#### <a name="activate-a-subscription"></a>Aktivera en prenumeration

När SaaS-kontot har kon figurer ATS för en slut kund måste utgivaren anropa API för aktiverings prenumeration på Microsoft.  Kunden debiteras inte om inte API-anropet lyckas.

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Skicka`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Använd 2018-08-31.   |
| `subscriptionId` | En unik identifierare för den köpta SaaS-prenumerationen.  Detta ID hämtas när du har löst in Marketplace-autentiseringstoken med hjälp av [lösnings-API: et](#resolve-a-purchased-subscription).
 |

*Begärandehuvuden:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| `x-ms-correlationid` |  Ett unikt sträng värde för åtgärden på klienten.  Den här strängen korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| `authorization`      |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop. Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exempel på begäran om nytto last:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Svars koder:*

Kod: 200 prenumerationen har marker ATS som prenumererad på Microsoft-sidan.

Det finns ingen svars text för det här anropet.

Felkod: 400 Felaktig begäran: verifieringen misslyckades.

* `planId` finns inte i nytto lasten för begäran.
* `planId` i nytto lasten för begäran matchar inte den som har köpts.
* `quantity` i nytto lasten för begäran matchar inte den som har köpts
* SaaS-prenumerationen är inaktive rad eller inaktive rad.

Kod: 403 förbjuden. Autentiseringstoken är ogiltig, upphört eller har inte angetts. Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som användes för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt.

Kod: 404 hittades inte. SaaS-prenumerationen har inte prenumerations tillstånd.

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

#### <a name="get-list-of-all-subscriptions"></a>Hämta lista över alla prenumerationer

Hämtar en lista över alla inköpta SaaS-prenumerationer för alla erbjudanden som publicerats av utgivaren på Marketplace.  SaaS-prenumerationer i alla möjliga statusar returneras. Prenumerationer som inte prenumererar på SaaS returneras också, eftersom den här informationen inte raderas på Microsoft-sidan.

Detta API returnerar sid brytnings resultat. Sid storleken är 100.

##### <a name="gethttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Ta`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Använd 2018-08-31.  |
| `continuationToken`  | Valfri parameter. Lämna tomt om du vill hämta den första resultat sidan.  Använd värdet som returnerades i `@nextLink` parametern för att hämta nästa sida. |

*Begärandehuvuden:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| `x-ms-correlationid` |  Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| `authorization`      |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop.  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Svars koder:*

Kod: 200 returnerar listan över alla befintliga prenumerationer för alla erbjudanden från den här utgivaren, baserat på utgivarens autentiseringstoken.

*Exempel på svars text:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Om det inte finns några inköpta SaaS-prenumerationer för den här utgivaren returneras tom svars text.

Kod: 403 förbjuden. Autentiseringstoken är inte tillgänglig, ogiltig eller har upphört att gälla.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt. 

Kod: 500 internt Server fel. Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

#### <a name="get-subscription"></a>Hämta prenumeration

Hämtar en angiven inköpt SaaS-prenumeration för ett SaaS-erbjudande som publicerats på Marketplace av utgivaren. Använd det här anropet för att hämta all tillgänglig information för en speciell SaaS-prenumeration med ID i stället för att anropa API: n för att hämta lista över alla prenumerationer.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Ta `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Använd 2018-08-31. |
| `subscriptionId`     |  En unik identifierare för den köpta SaaS-prenumerationen.  Detta ID hämtas när du har löst in Marketplace-autentiseringstoken med hjälp av lösnings-API: et. |

*Begärandehuvuden:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  `x-ms-correlationid` |  Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  `authorization`     | En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop. Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Svars koder:*

Kod: 200 returnerar information om en SaaS-prenumeration baserat på den `subscriptionId` angivna.

*Exempel på svars text:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

Kod: 403 förbjuden. Autentiseringstoken är ogiltig, upphört och har inte angetts. Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som används för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt. 

Kod: 404 hittades inte.  Det går inte att hitta SaaS-prenumerationen med det angivna värdet `subscriptionId` .

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

#### <a name="list-available-plans"></a>Lista tillgängliga planer

Hämtar alla planer för ett SaaS-erbjudande som identifieras av `subscriptionId` ett speciellt köp av det här erbjudandet.  Använd det här anropet för att hämta en lista över alla privata och offentliga planer som mottagaren av en SaaS-prenumeration kan uppdatera för prenumerationen.  De inköps planer som returneras är tillgängliga i samma geografi som den redan köpta planen.

Det här anropet returnerar en lista över planer som är tillgängliga för kunden utöver det som redan har köpts.  Listan kan visas för en slut kund på utgivarens webbplats.  En slutanvändare kan ändra prenumerations planen till något av planerna i den returnerade listan.  Det går inte att ändra planen till en lista som inte visas i listan.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Ta `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Använd 2018-08-31.  |
|  `subscriptionId`    |  En unik identifierare för den köpta SaaS-prenumerationen.  Detta ID hämtas när du har löst in Marketplace-autentiseringstoken med hjälp av lösnings-API: et. |

*Begärandehuvuden:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  `x-ms-correlationid`  |  Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  `authorization`     |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop.  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Svars koder:*

Kod: 200 returnerar en lista med alla tillgängliga planer för en befintlig SaaS-prenumeration, inklusive den som redan har köpts.

Exempel på svars text:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

Om `subscriptionId` inte hittas returneras tom svars text.

Kod: 403 förbjuden. Autentiseringstoken är ogiltig, förfallen eller har inte angetts.  Begäran kanske försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som används för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt. 

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

#### <a name="change-the-plan-on-the-subscription"></a>Ändra planen för prenumerationen

Uppdatera den befintliga prenumerationen som har köpts för en SaaS-prenumeration till ett nytt abonnemang (offentligt eller privat).  Utgivaren måste anropa detta API när en plan ändras på utgivarens sida för en SaaS-prenumeration som har köpts i Marketplace.

Detta API kan endast anropas för aktiva prenumerationer.  Eventuella planer kan ändras till alla befintliga prenumerationer (offentliga eller privata) men inte till sig själva.  För privata planer måste kundens klient organisation definieras som en del av planens mål grupp i Partner Center.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>9.0a `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Använd 2018-08-31.  |
| `subscriptionId`     | En unik identifierare för den köpta SaaS-prenumerationen.  Detta ID hämtas när du har löst in Marketplace-autentiseringstoken med hjälp av lösnings-API: et. |

*Begärandehuvuden:*
 
|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `x-ms-correlationid`  | Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `authorization`     |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop.  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exempel på begäran om nytto last:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Svars koder:*

Kod: 202 begäran om ändrings plan har accepterats och hanteras asynkront.  Partnern förväntas söka i **URL: en för åtgärds platsen** för att fastställa om begäran om ändrings planen lyckades eller inte.  Avsökningen bör göras var några: e sekund tills den slutliga statusen misslyckades, lyckades eller krocken tas emot för åtgärden.  Den slutgiltiga åtgärds statusen returneras snabbt, men det kan ta flera minuter i vissa fall.

Partnern får också ett webhook-meddelande när åtgärden är redo att slutföras på Marketplace-sidan.  Därefter bör utgivaren göra planen oförändrad på utgivarens sida.

*Svars rubriker:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  URL för att hämta åtgärdens status.  Till exempel `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Felkod: 400 Felaktig begäran: verifierings fel.

* Den nya planen finns inte eller är inte tillgänglig för den här SaaS-prenumerationen.
* Försöker ändra till samma plan.
* Prenumerations status för SaaS prenumererar inte.
* Uppdaterings åtgärden för en SaaS-prenumeration ingår inte i `allowedCustomerOperations` .

Kod: 403 förbjuden. Autentiseringstoken är ogiltig, förfallen eller har inte angetts.  Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som används för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt.

Kod: 404 hittades inte.  Det gick inte att hitta SaaS-prenumerationen `subscriptionId` .

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

>[!NOTE]
>Du kan ändra antingen planen eller antalet platser samtidigt, inte båda.

>[!Note]
>Detta API kan endast anropas när explicit godkännande har hämtats från slut kunden för ändringen.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Ändra antalet platser i SaaS-prenumerationen

Uppdatera (öka eller minska) antalet platser som har köpts för en SaaS-prenumeration.  Utgivaren måste anropa detta API när antalet platser ändras från utgivarens sida för en SaaS-prenumeration som har skapats i Marketplace.

Antalet platser får inte vara mer än vad som tillåts i den aktuella planen.  I det här fallet bör planen ändras innan du ändrar kvantiteten.

##### <a name="patchhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Använd 2018-08-31.  |
|  `subscriptionId`     | En unik identifierare för den köpta SaaS-prenumerationen.  Detta ID hämtas när du har löst in Marketplace-autentiseringstoken med hjälp av lösnings-API: et.  |

*Begärandehuvuden:*
 
|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `x-ms-correlationid`  | Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `authorization`     | En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop.  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Exempel på begäran om nytto last:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Svars koder:*

Kod: 202 begäran om att ändra kvantitet har accepterats och hanteras asynkront. Partnern förväntas söka på **Åtgärds platsens URL** för att fastställa om begäran om ändrings antal lyckades eller inte.  Avsökningen bör göras var några: e sekund tills den slutliga statusen misslyckades, lyckades eller krocken tas emot för åtgärden.  Den slutgiltiga åtgärds statusen returneras snabbt men kan ta flera minuter i vissa fall.

Partnern får också ett webhook-meddelande när åtgärden är redo att slutföras på Marketplace-sidan.  Därefter bör utgivaren göra ändringen på utgivarens sida.

*Svars rubriker:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Länka till en resurs för att få åtgärdens status.  Till exempel `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Felkod: 400 Felaktig begäran: verifierings fel.

* Den nya kvantiteten är större eller lägre än den aktuella plan gränsen.
* Den nya kvantiteten saknas.
* Försöker ändra till samma kvantitet.
* Prenumerations status för SaaS prenumererar inte.
* Uppdaterings åtgärden för en SaaS-prenumeration ingår inte i `allowedCustomerOperations` .

Kod: 403 förbjuden.  Autentiseringstoken är ogiltig, förfallen eller har inte angetts.  Begäran försöker komma åt en prenumeration som inte tillhör den aktuella utgivaren.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt. 

Kod: 404 hittades inte.  Det gick inte att hitta SaaS-prenumerationen `subscriptionId` .

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

>[!Note]
>Det går bara att ändra en plan eller kvantitet åt gången, inte båda.

>[!Note]
>Detta API kan endast anropas när explicit godkännande har hämtats från slut kunden för ändringen.

#### <a name="cancel-a-subscription"></a>Avbryta en prenumeration

Avbryt prenumerationen på en angiven SaaS-prenumeration.  Utgivaren behöver inte använda detta API och vi rekommenderar att kunderna dirigeras till Marketplace, för att avbryta SaaS-prenumerationer.

Om utgivaren beslutar att implementera annulleringen av SaaS-prenumerationen som har köpts i Marketplace på Publisher-sidan, måste han anropa detta API.  När det här anropet har slutförts kommer prenumerationens status att *avbrytas* på Microsoft-sidan.

Om en prenumeration annulleras inom följande Grace-perioder debiteras inte kunden:

* 24 timmar för en månatlig prenumeration efter aktiveringen.
* 14 dagar för en års prenumeration efter aktiveringen.

Kunden debiteras om en prenumeration annulleras efter ovanstående Grace-perioder.  När annulleringen lyckas kommer kunden omedelbart att förlora åtkomsten till SaaS-prenumerationen på Microsoft-sidan.

##### <a name="deletehttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Ta bort`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Använd 2018-08-31.  |
|  `subscriptionId`     | En unik identifierare för den köpta SaaS-prenumerationen.  Detta ID hämtas när du har löst in Marketplace-autentiseringstoken med hjälp av lösnings-API: et.  |

*Begärandehuvuden:*
 
|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `x-ms-correlationid`  | Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `authorization`     |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop.  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Svars koder:*

Kod: 202 begäran om att avbryta prenumerationen har accepterats och hanteras asynkront.  Partnern förväntas söka i **URL: en för åtgärds platsen** för att fastställa om begäran lyckades eller inte.  Avsökningen bör göras var några: e sekund tills den slutliga statusen misslyckades, lyckades eller krocken tas emot för åtgärden.  Den slutgiltiga åtgärds statusen returneras snabbt men kan ta flera minuter i vissa fall.

Partnern får också ett webhook-meddelande när åtgärden har slutförts på Marketplace-sidan.  Sedan bör utgivaren avbryta prenumerationen på utgivarens sida.

*Svars rubriker:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Länka till en resurs för att få åtgärdens status.  Till exempel `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Felkod: 400 Felaktig begäran.  Ta bort finns inte i `allowedCustomerOperations` listan för den här SaaS-prenumerationen.

Kod: 403 förbjuden.  Autentiseringstoken är ogiltig, går ut eller är inte tillgänglig. Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som används för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt.

Kod: 404 hittades inte.  Det gick inte att hitta SaaS-prenumerationen `subscriptionId` .

Kod: 500 internt Server fel. Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

### <a name="operations-apis"></a>API: er för åtgärder

#### <a name="list-outstanding-operations"></a>Lista över utestående åtgärder 

Hämta lista över väntande åtgärder för den angivna SaaS-prenumerationen.  Returnerade åtgärder bör bekräftas av utgivaren genom att anropa [API för åtgärds korrigering](#update-the-status-of-an-operation).

För närvarande returneras bara **åtgärder** som svar på det här API-anropet.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Ta `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Använd 2018-08-31.         |
|    `subscriptionId` | En unik identifierare för den köpta SaaS-prenumerationen.  Detta ID hämtas när du har löst in Marketplace-autentiseringstoken med hjälp av lösnings-API: et.  |

*Begärandehuvuden:*
 
|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `x-ms-correlationid` |  Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `authorization`     |  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Svars koder:*

Kod: 200 returnerar väntande återställnings åtgärd på den angivna SaaS-prenumerationen.

*Exempel på svars nytto last:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

Returnerar tom JSON om inga återställnings åtgärder väntar.

Felkod: 400 Felaktig begäran: verifierings fel.

Kod: 403 förbjuden. Autentiseringstoken är ogiltig, förfallen eller har inte angetts.  Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som används för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt. 

Kod: 404 hittades inte.  Det gick inte att hitta SaaS-prenumerationen `subscriptionId` .

Kod: 500 internt Server fel. Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

#### <a name="get-operation-status"></a>Hämta åtgärds status

Gör att utgivaren kan spåra statusen för den angivna asynkrona åtgärden:  **Avsluta prenumeration** , **ChangePlan** eller **ChangeQuantity** .

`operationId`För det här API-anropet kan hämtas från det värde som returneras av **Åtgärds plats** , Hämta väntande åtgärder API-anrop eller det `<id>` parameter värde som togs emot i ett webhook-anrop.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Ta `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Använd 2018-08-31.  |
|  `subscriptionId`    |  En unik identifierare för den köpta SaaS-prenumerationen.  Detta ID hämtas när du har löst in Marketplace-autentiseringstoken med hjälp av lösnings-API: et. |
|  `operationId`       |  Den unika identifieraren för den åtgärd som hämtas. |

*Begärandehuvuden:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  `x-ms-correlationid` |  Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `authorization`     |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop.  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Svars koder:*

Kod: 200 hämtar information om den angivna SaaS-åtgärden. 

*Exempel på svars nytto last:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeed, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Kod: 403 förbjuden. Autentiseringstoken är ogiltig, förfallen eller har inte angetts.  Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som används för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt. 

Kod: 404 hittades inte.  

* `subscriptionId`Det gick inte att hitta prenumerationen.
* Åtgärden med `operationId` hittades inte.

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

#### <a name="update-the-status-of-an-operation"></a>Uppdatera status för en åtgärd

Uppdatera statusen för en väntande åtgärd för att ange att åtgärden lyckades eller misslyckades på utgivarens sida.

`operationId`För det här API-anropet kan hämtas från det värde som returneras av **Åtgärds plats** , Hämta väntande åtgärder API-anrop eller `<id>` parameter värde som tagits emot i ett webhook-anrop.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>9.0a `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Använd 2018-08-31.  |
|   `subscriptionId`   |  En unik identifierare för den köpta SaaS-prenumerationen.  Detta ID hämtas när du har löst in Marketplace-autentiseringstoken med hjälp av lösnings-API: et.  |
|   `operationId`      |  Den unika identifieraren för åtgärden som slutförs. |

*Begärandehuvuden:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|   `x-ms-correlationid` |  Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  `authorization`     |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop.  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exempel på begäran om nytto last:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Svars koder:*

Kod: 200 ett anrop för att informera om att en åtgärd slutförts på partner sidan.  Svaret kan till exempel signalera att ändringar av platser eller planer på utgivarens sida har ändrats.

Kod: 403 förbjuden.  Autentiseringstoken är inte tillgänglig, ogiltig eller har upphört att gälla. Begäran kanske försöker komma åt en prenumeration som inte tillhör den aktuella utgivaren.
Ej tillåtet.  Autentiseringstoken är ogiltig, förfallen eller har inte angetts.  Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som används för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt.

Kod: 404 hittades inte.

* `subscriptionId`Det gick inte att hitta prenumerationen.
* Åtgärden med `operationId` hittades inte.

Kod: 409-konflikt.  Till exempel är en nyare uppdatering redan uppfylld.

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementera en webhook på SaaS-tjänsten

När du skapar ett transactable SaaS-erbjudande i Partner Center tillhandahåller partnern den **anslutning-webhook** -URL som ska användas som en http-slutpunkt.  Den här webhooken anropas av Microsoft med POST HTTP-anropet för att meddela utgivarens sida om följande händelser som inträffar på Microsoft-sidan:

* När SaaS-prenumerationen är i prenumerations status:
    * ChangePlan 
    * ChangeQuantity
    * Suspend
    * Avbryt prenumeration
* När SaaS-prenumerationen har statusen pausad:
    * Återinför
    * Avbryt prenumeration

Utgivaren måste implementera en webhook i SaaS-tjänsten för att SaaS-prenumerationens status ska vara konsekvent med Microsoft-sidan.  SaaS-tjänsten krävs för att anropa API: et get-åtgärd för att verifiera och auktorisera webhook-anropet och nytto Last data innan du vidtar åtgärder baserat på webhook-aviseringen.  Utgivaren ska returnera HTTP 200 till Microsoft så snart webhook-anropet bearbetas.  Det här värdet bekräftar att webhook-anropet har tagits emot av utgivaren.

>[!Note]
>Webb adress tjänsten för webhook måste vara igång dygnet runt och redo att ta emot nya samtal från Microsoft tid.  Microsoft har en princip för återförsök för webhook-anropet (500 nya försök över 8 timmar), men om utgivaren inte accepterar anropet och returnerar ett svar, kommer åtgärden som webhooken att meddela sig att sluta fungera på Microsoft-sidan.

*Exempel på webhook-nytto last:*

```json
// end customer changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

```json
// end customer's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": " 20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress"
}
```

## <a name="development-and-testing"></a>Utveckling och testning

För att starta utvecklings processen rekommenderar vi att du skapar dummy API-svar på utgivarens sida.  Svaren kan baseras på exempel svar som anges i det här dokumentet.

När utgivaren är klar för testningen av slut punkt till slut punkt:

* Publicera ett SaaS-erbjudande till en begränsad förhands gransknings publik och behåll den i för hands versions fasen.
* Det här erbjudandet bör ha en plan med 0 pris, så du bör inte utlösa faktisk fakturerings kostnad under testningen.  Ett annat alternativ är att ange ett pris som inte är noll och avbryta alla test inköp inom 24 timmar.
* Se till att alla flöden anropas från slut punkt till slut punkt, precis som en kund köper erbjudandet.
* Om partnern vill testa hela inköps-och fakturerings flödet gör du det med erbjudandet som kostar över $0.  Inköpet faktureras och en faktura skapas.

Ett inköps flöde kan utlösas från Azure Portal eller Microsoft AppSource platser, beroende på var erbjudandet publiceras.

Åtgärder för att *ändra plan* , *ändra antal* och *avbryta prenumerationer* testas från utgivarens sida.  Från Microsoft kan *avbryta prenumerationen* utlösas från både Azure Portal-och administrations Center (portalen där Microsoft AppSource inköp hanteras).  *Ändra kvantitet och plan* kan bara utlösas från administrations Center.

## <a name="get-support"></a>Få support

Se [Support för det kommersiella Marketplace-programmet i Partner Center](../support.md) for Publisher support options.

## <a name="next-steps"></a>Nästa steg

Mer alternativ för SaaS-erbjudanden finns i den kommersiella Marketplace för [API: er för avläsning av marknads](marketplace-metering-service-apis.md) plats.

Granska och Använd [SaaS SDK](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK) som är byggd ovanpå de API: er som beskrivs i det här dokumentet.