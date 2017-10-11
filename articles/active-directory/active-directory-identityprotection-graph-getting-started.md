---
title: "Kom igång med Azure Active Directory Identity Protection och Microsoft Graph | Microsoft Docs"
description: "Innehåller en introduktion till frågan Microsoft Graph en lista över riskhändelser och tillhörande information från Azure Active Directory."
services: active-directory
keywords: "Azure active directory identitetsskydd, risk händelse, säkerhetsproblem, säkerhetsprinciper, Microsoft Graph"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 9b01ff86da6a1fd4a439a6ba59ea15ed6480cdad
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Kom igång med Azure Active Directory Identity Protection och Microsoft Graph
Microsoft Graph är Microsoft unified API-slutpunkt och hem för [Azure Active Directory Identity Protection](active-directory-identityprotection.md) API: er. Det första API **identityRiskEvents**, kan du fråga Microsoft Graph en lista över [riskerar händelser](active-directory-identityprotection-risk-events-types.md) och tillhörande information. Den här artikeln hjälper dig att komma igång frågar detta API. En detaljerad introduktion, fullständig dokumentation och åtkomst till diagrammet Explorer finns på [Microsoft Graph plats](https://graph.microsoft.io/).

> [!IMPORTANT]
> Microsoft rekommenderar att du hanterar Azure AD via [Azure AD administratörscenter](https://aad.portal.azure.com) på Azure Portal istället för via den klassiska Azure-portalen som nämns i den här artikeln.

Det finns tre steg för att komma åt identitetsskydd data via Microsoft Graph:

1. Lägg till ett program med en klienthemlighet. 
2. Använd den här hemligheten och några andra typer av information för att autentisera till Microsoft Graph, där du får en token för autentisering. 
3. Använda denna token för att göra förfrågningar till API-slutpunkten och komma identitetsskydd data.

Innan du börjar behöver du:

* Administratörsbehörighet för att skapa programmet i Azure AD
* Namnet på din klient domän (till exempel contoso.onmicrosoft.com)

## <a name="add-an-application-with-a-client-secret"></a>Lägg till ett program med en klienthemlighet
1. [Logga in](https://manage.windowsazure.com) till din Azure klassiska portal som administratör. 
2. I det vänstra navigeringsfönstret, klicka på **Active Directory**. 
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)
3. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
4. Klicka på menyn högst upp **program**.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)
5. Klicka på **Lägg till** längst ned på sidan.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)
6. På den **vad vill du göra** dialogrutan klickar du på **Lägg till ett program som min organisation utvecklar**.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)
7. På den **berätta om tillämpningsprogrammet** dialogrutan, utför följande steg:
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)
   
    a. I den **namn** textruta, ange ett namn för ditt program (t.ex.: AADIP Risk händelse API-program).
   
    b. Som **typen**väljer **webbprogram och / eller webb-API**.
   
    c. Klicka på **Nästa**.
8. På den **appegenskaper** dialogrutan, utför följande steg:
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)
   
    a. I den **inloggnings-URL** textruta typen `http://localhost`.
   
    b. I den **App-ID URI** textruta typen `http://localhost`.
   
    c. Klicka på **Complete** (Slutför).

Kan nu konfigurera ditt program.

![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)

## <a name="grant-your-application-permission-to-use-the-api"></a>Ge ditt program behörighet att använda API: et
1. På ditt program i menyn överst, klickar du på **konfigurera**. 
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)
2. I den **behörigheter för andra program** klickar du på **lägga till program**.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)
3. På den **behörigheter för andra program** dialogrutan, utför följande steg:
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)
   
    a. Välj **Microsoft Graph**.
   
    b. Klicka på **Complete** (Slutför).
4. Klicka på **behörigheter för program: 0**, och välj sedan **läsa alla risker händelse identitetsinformation**.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)
5. Klicka på **Spara** längst ned på sidan.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

## <a name="get-an-access-key"></a>Hämta en åtkomstnyckel
1. På sidan för ditt program i den **nycklar** väljer 1 år som varaktighet.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)
2. Klicka på **Spara** längst ned på sidan.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)
3. Kopiera värdet för den nya nyckeln i avsnittet nycklar och klistra in den i en säker plats.
   
    ![Skapa ett program](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)
   
   > [!NOTE]
   > Om du tappar bort den här nyckeln kan behöver du gå tillbaka till det här avsnittet och skapa en ny nyckel. Behåll den här nyckeln en hemlighet: alla som har åtkomst till data.
   > 
   > 
4. I den **egenskaper** avsnittet, kopiera den **klient-ID**, och klistra in den i en säker plats. 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Autentisera till Microsoft Graph och frågor identitet Risk händelser API
Du bör nu ha:

* Klient-ID som du kopierade ovan
* Den nyckel som du kopierade ovan
* Namnet på domänen för din klient

Skicka en post-begäran till för att autentisera, `https://login.microsoft.com` med följande parametrar i brödtext:

* grant_type ”:**client_credentials**”
* resurs ”:**https://graph.microsoft.com**”
* client_id:<your client ID>
* client_secret:<your key>

> [!NOTE]
> Du måste ange värden för den **client_id** och **client_secret** parameter.
> 
> 

Om det lyckas, returneras en autentiseringstoken.  
För att anropa API: et, skapar du en rubrik med följande parameter:

    `Authorization`=”<token_type> <access_token>"


När autentisering, hittar du tokentypen och åtkomst-token i den returnerade token.

Skicka detta huvud som en begäran till följande API-URL:`https://graph.microsoft.com/beta/identityRiskEvents`

Svaret, är om det lyckas, en samling av riskhändelser identitet och associerade data i OData JSON-format, som kan parsas och hanteras som passar behoven.

Här är exempelkod för autentisering och anropa API: et med hjälp av Powershell.  
Lägg till en klient-ID nyckeln och domän för innehavare.

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

## <a name="additional-resources"></a>Ytterligare resurser
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
* [Typer av riskhändelser som identifieras av Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md)
* [Microsoft Graph](https://graph.microsoft.io/)
* [Översikt över Microsoft Graph](https://graph.microsoft.io/docs)
* [Azure AD Identity Protection Service rot](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

