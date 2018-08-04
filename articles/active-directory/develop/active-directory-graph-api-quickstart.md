---
title: Snabbstart för Azure AD Graph API | Microsoft Docs
description: Azure Active Directory Graph API ger Programmeringsåtkomst till Azure AD via REST-API för OData-slutpunkter. Program kan använda Azure AD Graph API för att utföra skapa, läsa, uppdatera och ta bort (CRUD)-åtgärder på katalogdata och objekt.
services: active-directory
documentationcenter: n/a
author: mtillman
manager: mtillman
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: 3298b39fc92f6e5867900ed151149ff936e2733c
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492637"
---
# <a name="quickstart-for-the-azure-ad-graph-api"></a>Snabbstart för Azure AD Graph API
Azure Active Directory (AD) Graph API ger Programmeringsåtkomst till Azure AD via REST-API för OData-slutpunkter. Program kan använda Azure AD Graph API för att utföra skapa, läsa, uppdatera och ta bort (CRUD)-åtgärder på katalogdata och objekt. Du kan till exempel använda Azure AD Graph API för att skapa en ny användare, visa eller uppdatera användarens egenskaper, ändra användarens lösenord, kontrollera medlemskapet för rollbaserad åtkomst, inaktivera eller ta bort användaren. Läs mer om Azure AD Graph API-funktionerna och Programscenarier [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) och [krav för Azure AD Graph API](https://msdn.microsoft.com/library/hh974476.aspx). 

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder [Microsoft Graph](https://developer.microsoft.com/graph) i stället för Azure AD Graph API för att komma åt resurser i Azure Active Directory. Vårt utvecklingsarbete koncentreras nu till Microsoft Graph och inga fler förbättringar planeras för Azure AD Graph API. Det finns ett begränsat antal scenarier där Azure AD Graph API fortfarande kan vara lämpligt. Mer information finns i blogginlägget [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) (Microsoft Graph eller Azure AD Graph) i Office Dev Center.
> 
> 

## <a name="how-to-construct-a-graph-api-url"></a>Hur du skapar en Graph API-URL
Om du vill få åtkomst till katalogdata och objekt (d.v.s. resurser eller entiteter) som du vill utföra CRUD-åtgärder i Graph API använder webbadresserna baserat på det öppna Data (OData)-protokollet. URL: er som används i Graph API består av fyra huvudsakliga delar: tjänsten rot, klient-ID, resurssökväg och alternativ med frågesträngar: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Ta exempel på följande URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Tjänsten rot**: I Azure AD Graph API service-rot är alltid https://graph.windows.net.
* **Klient-ID: t**: det här avsnittet kan vara ett verifierat (registrerad) domännamn, i föregående exempel contoso.com. Det kan också vara ett klient-ID för objektet eller ”ut” eller ”me” alias. Mer information finns i [adressering entiteter och åtgärder i Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Resurssökväg**: det här avsnittet av en URL som identifierar resursen om du vill att interagera med (användare, grupper, en viss användare eller en viss grupp osv.) I exemplet ovan är den högsta nivån ”grupper” till adressen som angetts för resursen. Du kan även lösa en specifik enhet, till exempel ”användare / {objekt-ID}” eller ”användare/userPrincipalName”.
* **Frågeparametrar**: ett frågetecken (?) avgränsar avsnittet sökvägen från avsnittet fråga parametrar. Frågeparametern ”api-version” krävs för alla förfrågningar i Azure AD Graph API. Azure AD Graph API har också stöd för följande OData-frågealternativ: **$filter**, **$orderby**, **$expand**, **$top**, och **$format**. Följande fråga alternativ stöds inte för närvarande: **$count**, **$inlinecount**, och **$skip**. Mer information finns i [stöds frågor, filter och sidindelning alternativen i Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Graph API-versioner
Du anger versionen för en Graph API-begäran i Frågeparametern ”api-version”. För version 1.5 och senare kan använda du ett numeriskt versionsvärde. API-version = 1.6. För tidigare versioner måste använda du en datumsträng som följer formatet ÅÅÅÅ-MM-DD; till exempel api-version = 2013-11-08. Funktioner i förhandsversion använda strängen ”beta”; till exempel api-version = beta. Mer information om skillnaderna mellan versionerna för Graph API finns i [Azure AD Graph API-versionshantering](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Graph API-metadata
Om du vill returnera metadatafilen Azure AD Graph API lägger du till ”$metadata”-segment efter klient-ID i URL: en för exempel, följande URL returnerar metadata för ett företag som demo: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Du kan ange denna URL i adressfältet i en webbläsare för att se metadata. CSDL metadatadokument returnerade beskriver entiteterna och komplexa typer, deras egenskaper och funktioner och åtgärder som exponeras av versionen av Graph API som du har begärt. Om du utesluter parametern api-versionen returnerar metadata för den senaste versionen.

## <a name="common-queries"></a>Vanliga frågor
[Azure AD Graph API vanliga frågor om](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) visar en lista över vanliga frågor som kan användas med Azure AD Graph, inklusive frågor som kan användas för att komma åt översta resurser i din katalog och frågor för att utföra åtgärder i din katalog.

Till exempel `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` returnerar företagsinformation för directory contoso.com.

Eller `https://graph.windows.net/contoso.com/users?api-version=1.6` visar en lista över alla användarobjekt i katalogen contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Med hjälp av Azure AD Graph Explorer
Du kan använda Azure AD Graph-testaren för Azure AD Graph API för att fråga directory-data när du skapar ditt program.

Skärmbilden nedan är utdata visas om du skulle gå till Azure AD Graph Explorer, logga in och ange `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` att visa alla användare i den inloggade användarens katalog:

![Azure AD graph api-Utforskaren](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Läsa in Azure AD Graph-testaren**: Läs in verktyget genom att gå till [ https://graphexplorer.azurewebsites.net/ ](https://graphexplorer.azurewebsites.net/). Klicka på **inloggning** och logga in med din Azure AD-autentiseringsuppgifter för att köra Azure AD Graph-testaren mot din klient. Om du kör Azure AD Graph-testaren mot din egen klientorganisation måste du eller din administratör att godkänna under inloggning. Om du har en Office 365-prenumeration kan har du automatiskt en Azure AD-klient. Autentiseringsuppgifterna som du använde för att logga in på Office 365 i själva verket är Azure AD-konton, och du kan använda dessa autentiseringsuppgifter med Azure AD Graph Explorer.

**Köra en fråga**: Skriv din fråga i textrutan begäran för att köra en fråga, och klicka på **hämta** eller klicka på den **ange** nyckel. Resultaten visas i rutan svar. Till exempel `https://graph.windows.net/myorganization/groups?api-version=1.6` visar en lista över alla gruppobjekt i den inloggade användarens katalog.

Observera följande funktioner och begränsningar i Azure AD Graph-Utforskaren:

* Funktionen för automatisk komplettering på resursen anger. Klicka på textrutan begäran (där företagets URL visas) om du vill se den här funktionen. Du kan välja en resurs som angetts i listrutan.
* Förfrågningshistorik.
* Har stöd för ”me” och ”ut” adressering alias. Du kan till exempel använda `https://graph.windows.net/me?api-version=1.6` att returnera för user-objektet för den inloggade användaren eller `https://graph.windows.net/myorganization/users?api-version=1.6` att returnera alla användare i den inloggade användarens katalog.
* Har stöd för fullständig CRUD-åtgärder mot dina egna katalog med hjälp av `POST`, `GET`, `PATCH` och `DELETE`.
* Ett avsnitt för sidhuvuden av svar. Det här avsnittet kan användas för att felsöka problem som uppstår när du kör frågor.
* Ett JSON-visningsprogram för svaret med visa och Dölj funktioner.
* Inget stöd för att visa eller ladda upp en miniatyrbild.

## <a name="using-fiddler-to-write-to-the-directory"></a>Med hjälp av Fiddler för att skriva till katalogen
Du kan använda Web-felsökare Fiddler för att prova skriva-åtgärder mot Azure AD-katalogen för den här snabbstartsguiden. Du kan till exempel hämta och överföra en användares profilfoto (som inte är möjligt med Azure AD Graph Explorer). Mer information och för att installera Fiddler Se [ http://www.telerik.com/fiddler ](http://www.telerik.com/fiddler).

I exemplet nedan använder du Fiddler Web felsökare för att skapa en ny säkerhetsgrupp 'MyTestGroup ”i Azure AD-katalogen.

**Hämta en åtkomsttoken**: för att komma åt Azure AD Graph klienter måste kunna autentisera till Azure AD först. Mer information finns i [Autentiseringsscenarier för Azure AD](authentication-scenarios.md).

**Skapa och köra en fråga**: utför följande steg:

1. Öppna Fiddler Web felsökare och växla till den **Composer** fliken.
2. Eftersom du vill skapa en ny säkerhetsgrupp markerar **Post** som HTTP-metoden från den nedrullningsbara menyn. Mer information om åtgärder och behörigheter på ett gruppobjekt finns [grupp](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) inom den [Azure AD Graph REST API-referens](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. I fältet bredvid **Post**, typ i följande URL för begäran: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > Med domännamnet för din egen Azure AD-katalog måste du ersätta {mytenantdomain}.
   > 
   > 
4. I fältet direkt under inlägget nedrullningsbara, skriver du följande HTTP-huvud:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Ersätt din &lt;din åtkomsttoken&gt; med åtkomsttoken för Azure AD-katalogen.
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
   
    Mer information om hur du skapar grupper finns i [Skapa grupp](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Mer information om Azure AD-entiteter och typer som visas av diagram och information om de åtgärder som kan utföras på dem med Graph finns i [Azure AD Graph REST API-referens](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Nästa steg
* Läs mer om den [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Läs mer om [Azure AD Graph API-Behörighetsomfattningar](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

