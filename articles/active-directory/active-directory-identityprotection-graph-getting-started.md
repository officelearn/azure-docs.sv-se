---
title: Microsoft Graph för Azure Active Directory identitetsskydd | Microsoft Docs
description: Lär dig hur man frågar Microsoft Graph en lista över riskhändelser och tillhörande information från Azure Active Directory.
services: active-directory
keywords: Azure active directory identitetsskydd, risk händelse, säkerhetsproblem, säkerhetsprinciper, Microsoft Graph
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.openlocfilehash: c6c59285fbb2c81e5e20d28171abfe806bd51122
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713209"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Kom igång med Azure Active Directory Identity Protection och Microsoft Graph
Microsoft Graph är Microsoft unified API-slutpunkt och hem för [Azure Active Directory Identity Protection](active-directory-identityprotection.md) API: er. Det första API **identityRiskEvents**, kan du fråga Microsoft Graph en lista över [riskerar händelser](active-directory-identityprotection-risk-events-types.md) och tillhörande information. Den här artikeln hjälper dig att komma igång frågar detta API. En detaljerad introduktion, fullständig dokumentation och åtkomst till diagrammet Explorer finns på [Microsoft Graph plats](https://graph.microsoft.io/).


Det finns fyra steg för att komma åt identitetsskydd data via Microsoft Graph:

1. Hämta ditt domännamn.
2. Skapa en ny appregistrering. 
2. Använd den här hemligheten och några andra typer av information för att autentisera till Microsoft Graph, där du får en token för autentisering. 
3. Använda denna token för att göra förfrågningar till API-slutpunkten och komma identitetsskydd data.

Innan du börjar behöver du:

* Administratörsbehörighet för att skapa programmet i Azure AD
* Namnet på din klient domän (till exempel contoso.onmicrosoft.com)


## <a name="retrieve-your-domain-name"></a>Hämta ditt domännamn 

1. [Logga in](https://portal.azure.com) till din Azure portal som administratör. 

2. I det vänstra navigeringsfönstret klickar du på **Active Directory**. 
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/41.png)


3. I den **hantera** klickar du på **egenskaper**.

    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/42.png)

4. Kopiera ditt domännamn.


## <a name="create-a-new-app-registration"></a>Skapa en ny appregistrering

1. På den **Active Directory** sidan den **hantera** klickar du på **App registreringar**.

    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/42.png)


2. Klicka på menyn högst upp **nya appregistrering**.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/43.png)

3. På den **skapa** utför följande steg:
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/44.png)

    a. I den **namn** textruta, ange ett namn för ditt program (t.ex.: AADIP Risk händelse API-program).
   
    b. Som **typen**väljer **webbprogram och / eller webb-API**.
   
    c. I den **inloggnings-URL** textruta typen `http://localhost`.

    d. Klicka på **Skapa**.

4. Öppna den **inställningar** i listan med program klickar du på din nya appregistrering. 

5. Kopiera den **program-ID**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Ge ditt program behörighet att använda API: et

1. På den **inställningar** klickar du på **nödvändiga behörigheter**.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/15.png)

2. På den **nödvändiga behörigheter** i verktygsfältet högst upp på sidan klickar du på **Lägg till**.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/16.png)
   
3. På den **lägga till API-åtkomst** klickar du på **väljer en API**.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/17.png)

4. På den **väljer en API** väljer **Microsoft Graph**, och klicka sedan på **Välj**.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/18.png)

5. På den **lägga till API-åtkomst** klickar du på **Välj behörigheter**.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/19.png)

6. På den **Aktivera åtkomst** klickar du på **läsa alla identitetsinformation risk**, och klicka sedan på **Välj**.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/20.png)

7. På den **lägga till API-åtkomst** klickar du på **klar**.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/21.png)

8. På den **nödvändiga behörigheter** klickar du på **bevilja med**, och klicka sedan på **Ja**.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/22.png)



## <a name="get-an-access-key"></a>Hämta en åtkomstnyckel

1. På den **inställningar** klickar du på **nycklar**.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/23.png)

2. På den **nycklar** utför följande steg:
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/24.png)

    a. I den **nyckeln beskrivning** textruta skriver du en beskrivning (till exempel *AADIP Risk händelse*).
    
    b. Som **varaktighet**väljer **i 1 års**.

    c. Klicka på **Spara**.
   
    d. Kopiera värdet för nyckeln och klistra in den i en säker plats.   
   
   > [!NOTE]
   > Om du tappar bort den här nyckeln kan behöver du gå tillbaka till det här avsnittet och skapa en ny nyckel. Behåll den här nyckeln en hemlighet: alla som har åtkomst till data.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Autentisera till Microsoft Graph och frågor identitet Risk händelser API
Du bör nu ha:

- Namnet på domänen för din klient

- Klient-ID 

- Nyckeln 


Skicka en post-begäran till för att autentisera, `https://login.microsoft.com` med följande parametrar i brödtext:

- grant_type ”:**client_credentials**”

-  resurs ”:**https://graph.microsoft.com**”

- client_id: \<klient-ID\>

- client_secret: \<din nyckel\>


Om det lyckas, returneras en autentiseringstoken.  
För att anropa API: et, skapar du en rubrik med följande parameter:

    `Authorization`=”<token_type> <access_token>"


När autentisering, hittar du tokentypen och åtkomst-token i den returnerade token.

Skicka detta huvud som en begäran till följande API-URL: `https://graph.microsoft.com/beta/identityRiskEvents`

Svaret, är om det lyckas, en samling av riskhändelser identitet och associerade data i OData JSON-format, som kan parsas och hanteras som du vill.

Här är exempelkod för autentisering och anropa API: et med hjälp av PowerShell.  
Lägg till klient-ID, den hemliga nyckeln och klient-domän.

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


## <a name="next-steps"></a>Nästa steg

Grattis, du skapat din första anropet till Microsoft Graph!  
Nu kan du fråga identitet riskhändelser och använda data men det är lämpligt.

Om du vill veta mer om Microsoft Graph och hur du skapar program med hjälp av Graph API kan ta en titt på [dokumentationen](https://graph.microsoft.io/docs) och mycket mer på den [Microsoft Graph plats](https://graph.microsoft.io/). Kontrollera också att skapa ett bokmärke i [Azure AD Identity Protection API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) sida som visar en lista över Identity Protection API: er finns i diagrammet. När vi lägger till nya sätt att arbeta med Identity Protection via API ser dem på sidan.

Mer information finns i:

-  [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

-  [Typer av riskhändelser som identifieras av Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Översikt över Microsoft Graph](https://graph.microsoft.io/docs)

- [Azure AD Identity Protection Service rot](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

