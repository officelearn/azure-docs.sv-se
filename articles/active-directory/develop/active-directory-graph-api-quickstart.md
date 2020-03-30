---
title: Så här använder du Azure AD Graph API
description: Azure Active Directory (Azure AD) Graph API ger programmatisk åtkomst till Azure AD via OData REST API-slutpunkter. Program kan använda Azure AD Graph API för att utföra åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) på katalogdata och objekt.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 9fd5fa943468924c289587285fe7986a73c21dba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161349"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Så här: Använd Azure AD Graph API

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder [Microsoft Graph](https://developer.microsoft.com/graph) i stället för Azure AD Graph API för att komma åt Azure Active Directory (Azure AD) resurser. Vårt utvecklingsarbete koncentreras nu till Microsoft Graph och inga fler förbättringar planeras för Azure AD Graph API. Det finns ett mycket begränsat antal scenarier som Azure AD Graph API fortfarande kan vara lämpligt för. Mer information finns i [Microsoft Graph eller Azure AD Graph-blogginlägget](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) och migrera Azure AD [Graph-appar till Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Azure AD Graph API ger programmatisk åtkomst till Azure AD via OData REST API-slutpunkter. Program kan använda Azure AD Graph API för att utföra åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) på katalogdata och objekt. Du kan till exempel använda Azure AD Graph API för att skapa en ny användare, visa eller uppdatera användarens egenskaper, ändra användarens lösenord, kontrollera gruppmedlemskap för rollbaserad åtkomst, inaktivera eller ta bort användaren. Mer information om Azure AD Graph API-funktioner och programscenarier finns i Azure [AD Graph API-](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) och [Azure AD Graph API-förutsättningar](https://msdn.microsoft.com/library/hh974476.aspx). Azure AD Graph API fungerar bara med jobb- eller skol-/organisationskonton.

Den här artikeln gäller Azure AD Graph API. Liknande information om Microsoft Graph API finns [i Använda Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

## <a name="how-to-construct-a-graph-api-url"></a>Så här skapar du en GRAPH API-URL

I Graph API, för att komma åt katalogdata och objekt (med andra ord resurser eller entiteter) som du vill utföra CRUD-åtgärder mot, kan du använda webbadresser baserat på OData-protokollet (Open Data). Webbadresserna som används i Graph API består av fyra huvuddelar: tjänstrot, klientidentifierare, resurssökväg och frågesträngalternativ: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Ta exemplet med följande `https://graph.windows.net/contoso.com/groups?api-version=1.6`WEBBADRESS: .

* **Tjänstrot:** I Azure AD Graph API https://graph.windows.netär tjänstroten alltid .
* **Klientidentifierare**: Det här avsnittet kan vara ett verifierat (registrerat) domännamn, i föregående exempel, contoso.com. Det kan också vara ett klientobjekt-ID eller aliaset "minorganisation" eller "mig". Mer information finns i [Adresseringsentiteter och åtgärder i Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Resurssökväg**: Det här avsnittet i en URL identifierar den resurs som ska interageras med (användare, grupper, en viss användare eller en viss grupp osv.) I exemplet ovan är det den översta nivån "grupper" för att ta itu med den resursuppsättningen. Du kan också adressera en viss entitet, till exempel "användare/{objectId}" eller "användare/userPrincipalName".
* **Frågeparametrar**: Ett frågetecken (?) skiljer resurssökvägsavsnittet från avsnittet frågeparametrar. Frågeparametern "api-version" krävs på alla begäranden i Azure AD Graph API. Azure AD Graph API stöder också följande OData-frågealternativ: **$filter**, **$orderby**, **$expand**, **$top**och **$format**. Följande frågealternativ stöds för närvarande inte: **$count**, **$inlinecount**och **$skip**. Mer information finns i [Frågor, filter och växlingsalternativ som stöds i Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Api-versioner för diagram

Du anger versionen för en Graph API-begäran i frågeparametern "api-version". För version 1.5 och senare använder du ett numeriskt versionsvärde. api-version=1.6. För tidigare versioner använder du en datumsträng som följer formatet YYYY-MM-DD. till exempel api-version=2013-11-08. För förhandsgranskningsfunktioner använder du strängen "beta"; till exempel api-version=beta. Mer information om skillnader mellan Graph API-versioner finns i [Azure AD Graph API-versionshantering](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Api-metadata för diagram

Om du vill returnera metadatafilen för Azure AD Graph API lägger du till segmentet "$metadata" efter klientidentifieraren `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`i URL:en Till exempel returnerar följande URL metadata för ett demoföretag: . Du kan ange den här webbadressen i adressfältet i en webbläsare för att se metadata. CsDL-metadatadokumentet som returneras beskriver entiteter och komplexa typer, deras egenskaper och de funktioner och åtgärder som exponeras av den version av Graph API som du begärde. Om du utelämnar parametern api-version returneras metadata för den senaste versionen.

## <a name="common-queries"></a>Vanliga frågor

[Vanliga frågor i Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) visar vanliga frågor som kan användas med Azure AD Graph, inklusive frågor som kan användas för att komma åt resurser på högsta nivå i katalogen och frågor för att utföra åtgärder i katalogen.

Returnerar `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` till exempel företagsinformation för katalog contoso.com.

Eller `https://graph.windows.net/contoso.com/users?api-version=1.6` visar alla användarobjekt i katalogen contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Använda Utforskaren för Azure AD Graph
Du kan använda Azure AD Graph Explorer för Azure AD Graph API för att fråga katalogdata när du skapar ditt program.

Följande skärmbild är utdata som du skulle se om du skulle navigera `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` till Azure AD Graph Explorer, logga in och ange för att visa alla användare i den inloggade användarens katalog:

![Exempel på utdata i Azure AD Graph API Explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Läs in Azure AD Graph Explorer:** Om [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)du vill läsa in verktyget navigerar du till . Klicka på **Logga in** och logga in med dina Azure AD-kontouppgifter för att köra Azure AD Graph Explorer mot din klient. Om du kör Azure AD Graph Explorer mot din egen klient måste antingen du eller administratören godkänna under inloggningen. Om du har en Office 365-prenumeration har du automatiskt en Azure AD-klientorganisation. De autentiseringsuppgifter du använder för att logga in på Office 365 är i själva verket Azure AD-konton och du kan använda dessa autentiseringsuppgifter med Azure AD Graph Explorer.

**Kör en fråga:** Om du vill köra en fråga skriver du frågan i textrutan för begäran och klickar på **HÄMTA** eller klickar på returtangenten. **enter** Resultaten visas i svarsrutan. Till exempel `https://graph.windows.net/myorganization/groups?api-version=1.6` visar alla gruppobjekt i den inloggade användarens katalog.

Observera följande funktioner och begränsningar i Azure AD Graph Explorer:

* Komplettera automatiskt på resursuppsättningar. Om du vill se den här funktionen klickar du på textrutan för begäran (där företagets URL visas). Du kan välja en resursuppsättning i listrutan.
* Begär historik.
* Stöder "jag" och "myorganisation" som behandlar alias. Du kan till `https://graph.windows.net/me?api-version=1.6` exempel använda för att returnera den `https://graph.windows.net/myorganization/users?api-version=1.6` inloggade användarens användarobjekt eller för att returnera alla användare i den inloggade användarens katalog.
* Stöder fullständiga CRUD-åtgärder mot `POST` `GET`din `PATCH` `DELETE`egen katalog med , och .
* Ett avsnitt med svarsrubriker. Det här avsnittet kan användas för att felsöka problem som uppstår när frågor körs.
* En JSON-visning för svaret med expandera och komprimera funktioner.
* Inget stöd för att visa eller ladda upp ett miniatyrfoto.

## <a name="using-fiddler-to-write-to-the-directory"></a>Använda Fiddler för att skriva till katalogen

I den här snabbstartsguiden kan du använda Fiddler Web Debugger för att öva på att utföra "write"-åtgärder mot din Azure AD-katalog. Du kan till exempel hämta och ladda upp en användares profilfoto (vilket inte är möjligt med Azure AD Graph Explorer). Mer information och hur du [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler)installerar Fiddler finns i .

I exemplet nedan använder du Fiddler Web Debugger för att skapa en ny säkerhetsgrupp "MyTestGroup" i din Azure AD-katalog.

**Hämta en åtkomsttoken:** För att komma åt Azure AD Graph krävs klienter för att autentisera till Azure AD först. Mer information finns i [Autentiseringsscenarier för Azure AD](authentication-scenarios.md).

**Komponera och kör en fråga:** Slutför följande steg:

1. Öppna Fiddler Web Debugger och växla till fliken **Kompositör.**
2. Eftersom du vill skapa en ny säkerhetsgrupp väljer du **Bokför** som HTTP-metod på rullbara menyn. Mer information om åtgärder och behörigheter för ett gruppobjekt finns i [Gruppera](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) i [Azure AD Graph REST API-referensen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. Skriv in följande URL för begäran i `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`fältet bredvid **Bokför:**.
   
   > [!NOTE]
   > Du måste ersätta {mytenantdomain} med domännamnet för din egen Azure AD-katalog.

4. Skriv följande HTTP-huvud i fältet direkt under rulldragningen Efter:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Ersätt &lt;din åtkomsttoken&gt; med åtkomsttoken för din Azure AD-katalog.

5. Skriv följande JSON i fältet **Begäran:**
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Mer information om hur du skapar grupper finns i [Skapa grupp](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Mer information om Azure AD-entiteter och -typer som exponeras av Graph och information om de åtgärder som kan utföras på dem med Graph finns i [Azure AD Graph REST API-referens](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Läs mer om [Azure AD Graph API-behörighetsomfattningar](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
