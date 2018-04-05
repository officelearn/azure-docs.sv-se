---
title: Snabbstartsguide för Azure AD Graph API | Microsoft Docs
description: Azure Active Directory Graph API ger programmatisk åtkomst till Azure AD via OData REST API-slutpunkter. Program kan använda Azure AD Graph API för att utföra skapa, läsa, uppdatera och ta bort CRUD-åtgärder i katalogdata och objekt.
services: active-directory
documentationcenter: n/a
author: viv-liu
manager: mtillman
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: viviali
ms.custom: aaddev
ms.openlocfilehash: 6869fc8ec582ea857128e91f1f127e9255e77865
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="quickstart-for-the-azure-ad-graph-api"></a>Snabbstartsguide för Azure AD Graph API
Azure Active Directory (AD) Graph API ger programmatisk åtkomst till Azure AD via OData REST API-slutpunkter. Program kan använda Azure AD Graph API för att utföra skapa, läsa, uppdatera och ta bort CRUD-åtgärder i katalogdata och objekt. Du kan till exempel använda Azure AD Graph API för att skapa en ny användare, visa eller uppdatera användarens egenskaper, ändra användarens lösenord, kontrollera gruppmedlemskap för rollbaserad åtkomst, inaktivera eller ta bort användaren. Läs mer om Azure AD Graph API-funktioner och Programscenarier [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) och [krav för Azure AD Graph API](https://msdn.microsoft.com/library/hh974476.aspx). 

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder [Microsoft Graph](https://developer.microsoft.com/graph) i stället för Azure AD Graph API för att komma åt resurser i Azure Active Directory. Vårt utvecklingsarbete koncentreras nu till Microsoft Graph och inga fler förbättringar planeras för Azure AD Graph API. Det finns ett begränsat antal scenarier där Azure AD Graph API fortfarande kan vara lämpligt. Mer information finns i blogginlägget [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) (Microsoft Graph eller Azure AD Graph) i Office Dev Center.
> 
> 

## <a name="how-to-construct-a-graph-api-url"></a>Hur du skapar en Graph API-URL
I Graph API för att komma åt katalogdata och objekt (med andra ord resurser eller entiteter) mot vilken du vill utföra CRUD-åtgärder kan du använda URL-adresser baserat på Open Data (OData)-protokollet. URL: er som används i Graph API består av fyra delar: tjänsten rot, klient-ID, resursens sökväg och frågealternativ sträng: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Ta exempel på följande URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Tjänsten rot**: I Azure AD Graph API service-roten är alltid https://graph.windows.net.
* **Identifierare för innehavare**: det här avsnittet kan vara en verifierad (registrerade) domännamn, i föregående exempel är contoso.com. Det kan också vara en klient-ID för objektet eller ”ut” eller ”me” alias. Mer information finns i [adressering entiteter och åtgärder i Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Resurssökvägen**: det här avsnittet för en URL som identifierar resurs för att vara har åtgärdat med (användare, grupper, en viss användare eller en viss grupp, etc.) I exemplet ovan är det översta nivån ”grupper” till adressen som angetts för resursen. Du kan även lösa en specifik enhet, till exempel ”användare / {objectId}” eller ”användare/userPrincipalName”.
* **Fråga parametrar**: ett frågetecken (?) separerar avsnittet sökvägen från avsnittet parametrar i frågan. ”Api-version”-frågeparameter krävs för alla förfrågningar i Azure AD Graph API. Azure AD Graph API stöder även följande alternativ för OData-frågan: **$filter**, **$orderby**, **$expand**, **$top**, och **$format**. Följande fråga alternativ stöds inte för närvarande: **$count**, **$inlinecount**, och **$skip**. Mer information finns i [stöds frågor, filter och växling alternativ i Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Graph API-versioner
Du kan ange version för en begäran för Graph API i ”api-version”-frågeparameter. För version 1.5 och senare använda du ett numeriskt versionsvärde. API-version = 1.6. För tidigare versioner måste använda du en datumsträng som stämmer överens med formatet ÅÅÅÅ-MM-DD; till exempel api-version = 2013-11-08. Förhandsgranskningsfunktioner, använda i strängen ”betaversionen”; till exempel api-version = beta. Mer information om skillnaderna mellan Graph API-versioner finns [Azure AD Graph API versionshantering](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Graph API-metadata
Om du vill returnera metadatafil Azure AD Graph API lägger du till ”$metadata”-segment efter klient-ID i URL: en för exempel, returnerar följande URL metadata för ett företag som demo: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Du kan ange URL: en i adressfältet i en webbläsare om du vill se metadata. Metadatadokumentet CSDL returnerade beskriver entiteterna och komplexa typer, deras egenskaper och funktioner och åtgärder som exponeras av versionen av Graph API du begärt. Utelämna parametern api-version returnerar metadata för den senaste versionen.

## <a name="common-queries"></a>Vanliga frågor
[Azure AD Graph API vanliga frågor](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) visar en lista över vanliga frågor som kan användas med Azure AD-diagram, inklusive frågor som kan användas för att komma åt översta resurser i din katalog och frågor för att utföra åtgärder i din katalog.

Till exempel `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` returnerar företagsinformation för directory contoso.com.

Eller `https://graph.windows.net/contoso.com/users?api-version=1.6` visar en lista över alla objekt i katalogen contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Med hjälp av Azure AD Graph Explorer
Du kan använda Azure AD Graph Explorer för Azure AD Graph API för att fråga directory-data när du skapar ditt program.

Följande skärmbild är utdata visas om du skulle gå till Azure AD Graph Explorer, logga in och ange `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` att visa alla användare i den inloggade användarens katalog:

![Azure AD graph api explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Läsa in i Azure AD Graph Explorer**: Gå till för att läsa in verktyget [ https://graphexplorer.azurewebsites.net/ ](https://graphexplorer.azurewebsites.net/). Klicka på **inloggning** och logga in med dina autentiseringsuppgifter för Azure AD för att köra Azure AD Graph Explorer mot din klient. Om du kör Azure AD Graph Explorer mot en egen klient, måste du eller din administratör tillstånd under inloggning. Om du har en Office 365-prenumeration kan ha du automatiskt en Azure AD-klient. De autentiseringsuppgifter du använder för att logga in på Office 365 i själva verket är Azure AD-konton, och du kan använda dessa autentiseringsuppgifter med Azure AD Graph Explorer.

**Köra en fråga**: Om du vill köra en fråga, Skriv in frågan i textrutan begäran och klickar på **hämta** eller klicka på den **ange** nyckel. Resultatet visas i rutan svar. Till exempel `https://graph.windows.net/myorganization/groups?api-version=1.6` visar en lista över alla gruppera objekt i katalogen för den inloggade användaren.

Observera följande funktioner och begränsningar i Azure AD Graph Explorer:

* Anger kapaciteten för Komplettera automatiskt på resursen. Klicka på rutan begäran (där företagets URL visas) om du vill se den här funktionen. Du kan välja en resurs från den nedrullningsbara listan.
* Förfrågningshistorik.
* Har stöd för ”me” och ”ut” adressering alias. Du kan till exempel använda `https://graph.windows.net/me?api-version=1.6` att returnera användarobjektet för den inloggade användaren eller `https://graph.windows.net/myorganization/users?api-version=1.6` att returnera alla användare i den inloggade användarens katalog.
* Har stöd för fullständig CRUD-åtgärder mot egna directory med hjälp av `POST`, `GET`, `PATCH` och `DELETE`.
* Ett svar huvuden avsnitt. Det här avsnittet kan användas för att felsöka problem som uppstår när du kör frågor.
* En JSON-visningsprogram för svar med visa och Dölj funktioner.
* Inget stöd för att visa eller ladda upp en miniatyrbild.

## <a name="using-fiddler-to-write-to-the-directory"></a>Med Fiddler för att skriva till katalogen
Du kan använda Fiddler Web felsökaren prova skriva åtgärder mot Azure AD-katalogen för den här snabbstartsguide. Du kan till exempel skaffa och ladda upp en användares profilfoto (som inte är möjligt med Azure AD Graph Explorer). Mer information och installera Fiddler finns [ http://www.telerik.com/fiddler ](http://www.telerik.com/fiddler).

I exemplet nedan använder du Fiddler Web felsökare för att skapa en ny säkerhetsgrupp 'MyTestGroup' i Azure AD-katalogen.

**Hämta en åtkomst-token**: för att komma åt Azure AD Graph klienter som krävs för att kunna autentisera till Azure AD först. Mer information finns i [Autentiseringsscenarier för Azure AD](active-directory-authentication-scenarios.md).

**Skapa och köra en fråga**: utför följande steg:

1. Öppna Fiddler Web felsökare och växla till den **Composer** fliken.
2. Eftersom du vill skapa en ny säkerhetsgrupp markerar **efter** som HTTP-metoden från den nedrullningsbara menyn. Mer information om åtgärder och behörigheter på ett gruppobjekt finns [grupp](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) inom den [Azure AD Graph REST API-referens](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. I fältet bredvid **Post**, ange följande URL för begäran: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > Med namnet på din egen Azure AD-katalog måste du ersätta {mytenantdomain}.
   > 
   > 
4. I fältet direkt under Post nedrullningsbara skriver du följande HTTP-huvud:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Ersätt din &lt;åtkomst-token&gt; med åtkomsttoken för Azure AD-katalogen.
   > 
   > 
5. I den **Begärandetext** anger du följande JSON:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Mer information om hur du skapar grupper finns [Skapa grupp](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Mer information om Azure AD-entiteter och typer som visas av diagram och information om åtgärder som kan utföras på dem med Graph finns [Azure AD Graph REST API-referens](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om den [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Lär dig mer om [Azure AD Graph API Behörighetsomfattningen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

