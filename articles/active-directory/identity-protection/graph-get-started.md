---
title: Microsoft Graph för Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur du frågar Microsoft Graph för en lista över riskhändelser och tillhörande information från Azure Active Directory.
services: active-directory
keywords: identitetsskydd för Azure active directory, riskhändelse, säkerhetsproblem, säkerhetsprinciper, Microsoft Graph
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
ms.reviewer: sahandle
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3357cfd5e845346534f263c768b5cf6b6a38ea4e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58793993"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Kom igång med Azure Active Directory Identity Protection och Microsoft Graph

Microsoft Graph är Microsofts enhetlig API-slutpunkt och hem för [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) API: er. Det finns tre API: er som visar information om riskfylld användare och inloggningar. Den första API **identityRiskEvents**, kan du fråga Microsoft Graph för en lista över [riskhändelser](../reports-monitoring/concept-risk-events.md) och tillhörande information. Andra API: et **riskyUsers**, kan du fråga Microsoft Graph för information om användare Identity Protection har identifierats som risk. Den tredje API **inloggning från**, kan du fråga Microsoft Graph information om Azure AD-inloggningar med specifika egenskaper som rör risktillstånd, information och nivå. Den här artikeln hjälper dig att komma igång med att ansluta till Microsoft Graph och frågor till dessa API: er. En djupgående introduktion, fullständig dokumentation och åtkomst till Graph-testaren finns i den [Microsoft Graph plats](https://graph.microsoft.io/) eller specifika referensdokumentationen för dessa API: er:

* [identityRiskEvents API](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta)
* [riskyUsers API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [inloggning från API: et](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)


## <a name="connect-to-microsoft-graph"></a>Ansluta till Microsoft graph

Det finns fyra steg för att komma åt Identity Protection data via Microsoft Graph:

1. Hämta ditt domännamn.
2. Skapa en ny appregistrering. 
3. Använd den här hemligheten och några andra typer av information för att autentisera till Microsoft Graph, där du får en autentiseringstoken. 
4. Använd denna token för att göra begäranden till API-slutpunkt och får tillbaka Identity Protection-data.

Innan du börjar behöver du:

* Administratörsbehörighet för att skapa programmet i Azure AD
* Namnet på din klient-domänen (exempel: contoso.onmicrosoft.com)


## <a name="retrieve-your-domain-name"></a>Hämta ditt domännamn 

1. [Logga in](https://portal.azure.com) till din Azure-portalen som administratör. 

2. I det vänstra navigeringsfönstret klickar du på **Active Directory**. 
   
    ![Skapa ett program](./media/graph-get-started/41.png)


3. I den **hantera** klickar du på **egenskaper**.

    ![Skapa ett program](./media/graph-get-started/42.png)

4. Kopiera ditt domännamn.


## <a name="create-a-new-app-registration"></a>Skapa en ny appregistrering

1. På den **Active Directory** sidan den **hantera** klickar du på **appregistreringar**.

    ![Skapa ett program](./media/graph-get-started/42.png)


2. Klicka på menyn längst upp **ny programregistrering**.
   
    ![Skapa ett program](./media/graph-get-started/43.png)

3. På den **skapa** utför följande steg:
   
    ![Skapa ett program](./media/graph-get-started/44.png)

    a. I den **namn** textrutan anger du ett namn för ditt program (till exempel: AADIP Risk händelse-API-program).
   
    b. Som **typ**väljer **webbprogram och / eller webb-API**.
   
    c. I den **inloggnings-URL** textrutan typ `http://localhost`.

    d. Klicka på **Skapa**.

4. Öppna den **inställningar** , i listan med program klickar du på din nyligen skapade app för registrering. 

5. Kopiera den **program-ID**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Ge ditt program behörighet att använda API: et

1. På den **inställningar** klickar du på **behörigheter som krävs för**.
   
    ![Skapa ett program](./media/graph-get-started/15.png)

2. På den **nödvändiga behörigheter** , i verktygsfältet högst upp, klickar du på **Lägg till**.
   
    ![Skapa ett program](./media/graph-get-started/16.png)
   
3. På den **Lägg till API-åtkomst** klickar du på **Välj en API**.
   
    ![Skapa ett program](./media/graph-get-started/17.png)

4. På den **Välj en API** väljer **Microsoft Graph**, och klicka sedan på **Välj**.
   
    ![Skapa ett program](./media/graph-get-started/18.png)

5. På den **Lägg till API-åtkomst** klickar du på **Välj behörigheter**.
   
    ![Skapa ett program](./media/graph-get-started/19.png)

6. På den **Aktivera åtkomst** klickar du på **läsa alla risker identitetsinformation**, och klicka sedan på **Välj**.
   
    ![Skapa ett program](./media/graph-get-started/20.png)

7. På den **Lägg till API-åtkomst** klickar du på **klar**.
   
    ![Skapa ett program](./media/graph-get-started/21.png)

8. På den **nödvändiga behörigheter** klickar du på **bevilja behörigheter**, och klicka sedan på **Ja**.
   
    ![Skapa ett program](./media/graph-get-started/22.png)



## <a name="get-an-access-key"></a>Hämta en åtkomstnyckel

1. På den **inställningar** klickar du på **nycklar**.
   
    ![Skapa ett program](./media/graph-get-started/23.png)

2. På den **nycklar** utför följande steg:
   
    ![Skapa ett program](./media/graph-get-started/24.png)

    a. I den **nyckelbeskrivning** textrutan anger du en beskrivning (till exempel *AADIP Riskhändelsen*).
    
    b. Som **varaktighet**väljer **i 1 år**.

    c. Klicka på **Spara**.
   
    d. Kopiera nyckelvärdet och klistra in den i en säker plats.   
   
   > [!NOTE]
   > Om du förlorar den här nyckeln kan behöver du gå tillbaka till det här avsnittet och skapa en ny nyckel. Förvara nyckeln en hemlighet: alla som har det kan komma åt dina data.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Autentisera Microsoft Graph och fråga efter händelse-API identitet Risk

Du bör nu ha:

- Namnet på din klient-domänen

- Klient-ID 

- Nyckeln 


För att autentisera, skicka en post-begäran till `https://login.microsoft.com` med följande parametrar i texten:

- _typ av beviljande ”:**client_credentials**”

-  resursen: `https://graph.microsoft.com`

- client_id: \<klient-ID\>

- client_secret: \<din nyckel\>


Om detta lyckas, returneras en autentiseringstoken.  
För att anropa API: et, skapar du ett huvud med följande parameter:

    `Authorization`=”<token_type> <access_token>"


När du autentiserar, hittar tokentypen och åtkomst-token i den returnerade token.

Skicka den här rubriken som en begäran till följande API-URL: `https://graph.microsoft.com/beta/identityRiskEvents`

Svaret, är om detta lyckas, en samling av identitet, riskhändelser och associerade data i OData JSON-format, som kan parsas och hanteras som du vill.

Här är exempelkod för autentisering och anropa API med hjälp av PowerShell.  
Lägg till klient-ID, den hemliga nyckeln och klient-domänen.

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

## <a name="query-the-apis"></a>Fråga efter API: er

Dessa tre API: er ger en mängd möjligheter att hämta information om riskfylld användare och inloggningar i din organisation. Nedan visas några vanliga användningsområden för dessa API: er och de associerade exempelförfrågan. Du kan köra dessa frågor med hjälp av exempelkoden ovan eller med hjälp av [Graph-testaren](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-the-high-risk-and-medium-risk-events-identityriskevents-api"></a>Hämta händelser (identityRiskEvents API) med hög risk och medel risk

Medel och med hög risk händelser representerar de som kan ha möjlighet att utlösaren Identity Protection-inloggningen eller användarrisk principer. Eftersom de har medel eller hög sannolikhet att den användare som försöker logga in inte är ägare legitima identitet, ska åtgärda de här händelserna vara en prioritet. 

```
GET https://graph.microsoft.com/beta/identityRiskEvents?`$filter=riskLevel eq 'high' or riskLevel eq 'medium'" 
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Hämta alla användare som har klarat en MFA-kontrollen som utlöses av princip för riskfyllda inloggningar (riskyUsers API)

För att förstå effekten Identity Protection riskbaserade principer har på din organisation kan du fråga efter alla användare som har klarat en MFA-kontrollen som utlöses av en princip för riskfyllda inloggningar. Den här informationen kan hjälpa dig att förstå vilka användare Identity Protection kan har felaktigt har identifierats på som risker och vilka av dina behöriga användare kan utföra åtgärder som AI bedömer riskfyllda.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Hämta alla riskfyllda inloggningar för en viss användare (inloggning från API: et)

Du kan bättre förstå tillståndet för riskerna genom att hämta alla sina riskfyllda inloggningar när du tycker att en användare kan ha komprometterats. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```




## <a name="next-steps"></a>Nästa steg

Grattis, du har skapat just ditt första anrop till Microsoft Graph!  
Nu kan du fråga identitet riskhändelser och använda data men du vill.


Om du vill veta mer om Microsoft Graph och hur du skapar program med Graph API kan ta en titt på [dokumentation](https://docs.microsoft.com/graph/overview) och mycket mer på den [Microsoft Graph plats](https://developer.microsoft.com/graph). 


Relaterad information finns i:

-  [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)

-  [Typer av riskhändelser som identifierats av Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)

- [Microsoft Graph](https://developer.microsoft.com/graph/)

- [Översikt över Microsoft Graph](https://developer.microsoft.com/graph/docs)

- [Azure AD Identity Protection Service Root](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)