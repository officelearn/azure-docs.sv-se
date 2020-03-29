---
title: Microsoft Graph API för Azure Active Directory Identity Protection
description: Lär dig hur du frågar Microsoft Graph-riskidentifieringar och tillhörande information från Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6fd62ccab4d6e32b23835d280732797e133ada8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671621"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Komma igång med Azure Active Directory Identity Protection och Microsoft Graph

Microsoft Graph är Microsoft Unified API-slutpunkt och hemmet för [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) API:er. Det finns fyra API:er som exponerar information om riskfyllda användare och inloggningar. Med det första **API:et, riskDetection,** kan du fråga Microsoft Graph efter en lista över både användar- och inloggningslänkade riskidentifieringar och tillhörande information om identifieringen. Med det andra **API:et, riskyUsers,** kan du fråga Microsoft Graph efter information om användare Identity Protection som identifierats som risk. Med det tredje API:et, **signIn,** kan du fråga Microsoft Graph efter information om Azure AD-inloggningar med specifika egenskaper relaterade till risktillstånd, detaljrikedom och nivå. Med det fjärde **API:et, identityRiskEvents,** kan du fråga Microsoft Graph efter en lista över [riskidentifieringar](../reports-monitoring/concept-risk-events.md) och tillhörande information. API:et för identityRiskEvents kommer att inaktuella den 10 januari 2020. Vi föreslår att du använder **API:et för riskDetections** istället. I den här artikeln börjar du med att ansluta till Microsoft Graph och fråga dessa API:er. En djupgående introduktion, fullständig dokumentation och åtkomst till Graph Explorer finns på [Microsoft Graph-webbplatsen](https://graph.microsoft.io/) eller den specifika referensdokumentationen för dessa API:er:

* [api för riskdetection](/graph/api/resources/riskdetection?view=graph-rest-beta)
* [riskyUsers API](/graph/api/resources/riskyuser?view=graph-rest-beta)
* [signIn API](/graph/api/resources/signin?view=graph-rest-beta)
* [identityRiskEvents API](/graph/api/resources/identityriskevent?view=graph-rest-beta) *kommer att vara inaktuella 10 januari 2020*

## <a name="connect-to-microsoft-graph"></a>Ansluta till Microsoft-diagram

Det finns fyra steg för att komma åt identitetsskyddsdata via Microsoft Graph:

1. Hämta ditt domännamn.
2. Skapa en ny appregistrering. 
3. Använd den här hemligheten och några andra information för att autentisera till Microsoft Graph, där du får en autentiseringstoken. 
4. Använd den här token för att göra begäranden till API-slutpunkten och få identity protection-data tillbaka.

Innan du börjar behöver du:

* Administratörsbehörighet för att skapa programmet i Azure AD
* Namnet på din klientdomän (till exempel contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>Hämta ditt domännamn 

1. [Logga in på](https://portal.azure.com) din Azure-portal som administratör. 
1. Klicka på **Active Directory**i det vänstra navigeringsfönstret . 

   ![Skapa ett program](./media/howto-identity-protection-graph-api/41.png)

1. Klicka på **Egenskaper**i avsnittet **Hantera.**

   ![Skapa ett program](./media/howto-identity-protection-graph-api/42.png)

1. Kopiera ditt domännamn.

## <a name="create-a-new-app-registration"></a>Skapa en ny appregistrering

1. Klicka på **Appregistreringar**i avsnittet **Hantera** på sidan **Active Directory.**

   ![Skapa ett program](./media/howto-identity-protection-graph-api/42.png)

1. Klicka på **Ny programregistrering**högst upp.

   ![Skapa ett program](./media/howto-identity-protection-graph-api/43.png)

1. Gör följande på sidan **Skapa:**

   ![Skapa ett program](./media/howto-identity-protection-graph-api/44.png)

   1. Skriv ett namn för ditt program i textrutan **Namn** (till exempel Azure AD Risk Detection API Application).

   1. Som **typ**väljer du **Webbprogram och / Eller webb-API**.

   1. Skriv `http://localhost`i textrutan **Logga in url** .

   1. Klicka på **Skapa**.
1. Om du vill öppna sidan **Inställningar** klickar du på registreringen av den nyskapade appen i programlistan. 
1. Kopiera **program-ID.**

## <a name="grant-your-application-permission-to-use-the-api"></a>Bevilja ditt program behörighet att använda API:et

1. Klicka på Obligatoriska **behörigheter**på sidan **Inställningar.**

   ![Skapa ett program](./media/howto-identity-protection-graph-api/15.png)

1. Klicka på **Lägg till**i **verktygsfältet** högst upp på sidan Obligatoriska behörigheter .

   ![Skapa ett program](./media/howto-identity-protection-graph-api/16.png)

1. Klicka på Välj ett **API**på **sidan Lägg till API.**

   ![Skapa ett program](./media/howto-identity-protection-graph-api/17.png)

1. På sidan **Välj ett API** väljer du Microsoft **Graph**och klickar sedan på **Markera**.

   ![Skapa ett program](./media/howto-identity-protection-graph-api/18.png)

1. Klicka på **Välj behörigheter**på sidan **Lägg till API-åtkomst.**

   ![Skapa ett program](./media/howto-identity-protection-graph-api/19.png)

1. Klicka på **Läs all identitetsriskinformation**på sidan **Aktivera åtkomst** och klicka sedan på **Välj**.

   ![Skapa ett program](./media/howto-identity-protection-graph-api/20.png)

1. Klicka på **Klar**på **sidan Lägg till API-åtkomst.**

   ![Skapa ett program](./media/howto-identity-protection-graph-api/21.png)

1. Klicka på **Bevilja behörigheter**på sidan Obligatoriska **behörigheter** och klicka sedan på **Ja**.

   ![Skapa ett program](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Hämta en åtkomstnyckel

1. Klicka på **Nycklar**på sidan **Inställningar** .

   ![Skapa ett program](./media/howto-identity-protection-graph-api/23.png)

1. Gör följande på sidan **Nycklar:**

   ![Skapa ett program](./media/howto-identity-protection-graph-api/24.png)

   1. Skriv en beskrivning i textrutan **Nyckelbeskrivning** (till exempel *Azure AD Risk Detection*).
   1. Som **Varaktighet**väljer du **I 1 år**.
   1. Klicka på **Spara**.
   1. Kopiera nyckelvärdet och klistra sedan in det på en säker plats.   
   
   > [!NOTE]
   > Om du förlorar den här nyckeln måste du gå tillbaka till det här avsnittet och skapa en ny nyckel. Håll den här nyckeln hemlig: alla som har den kan komma åt dina data.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Autentisera till Microsoft Graph och fråga API:et för identitetsriskidentifieringar

Vid denna punkt bör du ha:

- Namnet på din klientdomän
- Klient-ID 
- Nyckeln 

Om du vill autentisera `https://login.microsoft.com` skickar du en inläggsbegäran till med följande parametrar i brödtexten:

- grant_type: "**client_credentials**"
- Resurs:`https://graph.microsoft.com`
- client_id: \<ditt klient-ID\>
- client_secret: \<din nyckel\>

Om det lyckas returneras en autentiseringstoken.  
Så här anropar du API:et genom att skapa ett huvud med följande parameter:

```
`Authorization`="<token_type> <access_token>"
```

När du autentiserar kan du hitta tokentypen och åtkomsttoken i den returnerade token.

Skicka det här huvudet som en begäran till följande API-URL:`https://graph.microsoft.com/beta/identityRiskEvents`

Svaret, om det lyckas, är en samling identitetsriskidentifieringar och associerade data i OData JSON-formatet, som kan tolkas och hanteras som du vill.

Här är exempelkod för att autentisera och anropa API:et med PowerShell.  
Lägg bara till ditt klient-ID, den hemliga nyckeln och klientdomänen.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Fråga API:erna

Dessa tre API:er ger en mängd möjligheter att hämta information om riskfyllda användare och inloggningar i organisationen. Nedan finns några vanliga användningsfall för dessa API:er och tillhörande exempelbegäranden. Du kan köra dessa frågor med exempelkoden ovan eller genom att använda [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Hämta alla offlineriskidentifieringar (riskDetection API)

Med principer för inloggningsrisk för identitetsskydd kan du använda villkor när risk upptäcks i realtid. Men hur är det med upptäckter som upptäcks offline? Om du vill förstå vilka identifieringar som har inträffat offline och därmed inte skulle ha utlöst principen för inloggningsrisk kan du fråga riskDetection API.To understand what detections occurred offline, and thus would not have triggered the sign-in risk policy, you can query the riskDetection API.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Få alla användare som framgångsrikt klarat en MFA-utmaning som utlöses av en policy för riskfyllda inloggningar (riskyUsers API)

För att förstå vilken inverkan Identity Protection riskbaserade principer har på din organisation, kan du fråga alla användare som framgångsrikt klarat en MFA-utmaning som utlöses av en riskabel inloggningsprincip. Den här informationen kan hjälpa dig att förstå vilka användare Identity Protection felaktigt kan ha upptäckts som risk och vilka av dina legitima användare som kan utföra åtgärder som AI anser vara riskabelt.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Få alla riskfyllda inloggningar för en viss användare (signIn API)

När du tror att en användare kan ha komprometterats kan du bättre förstå risken genom att hämta alla deras riskfyllda inloggningar. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Nästa steg

Grattis, du gjorde just ditt första samtal till Microsoft Graph!  
Nu kan du fråga identitetsriskidentifieringar och använda data som du tycker passar.

Mer information om Microsoft Graph och hur du skapar program med Graph API finns i [dokumentationen](/graph/overview) och mycket mer på [Webbplatsen Microsoft Graph](https://developer.microsoft.com/graph). 

Relaterad information finns i:

- [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)
- [Typer av riskidentifieringar som upptäckts av Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Översikt över Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Azure AD-identitetsskyddstjänstrot](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
