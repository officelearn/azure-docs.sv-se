---
title: Så här använder du Azure AD-Graph API
description: Azure Active Directory (Azure AD) Graph API ger program mässig åtkomst till Azure AD via OData REST API-slutpunkter. Program kan använda Azure AD Graph API för att utföra åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) på katalog data och objekt.
services: active-directory
documentationcenter: n/a
author: rwike77
manager: CelesteDG
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92a88b1e17812b9dc99fd1d5b391d95ba541f48a
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533069"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Gör så här: använda Azure AD-Graph API

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder [Microsoft Graph](https://developer.microsoft.com/graph) i stället för Azure AD Graph API för att få åtkomst till Azure Active Directory (Azure AD)-resurser. Vårt utvecklingsarbete koncentreras nu till Microsoft Graph och inga fler förbättringar planeras för Azure AD Graph API. Det finns ett begränsat antal scenarier där Azure AD Graph API fortfarande är lämpligt. Mer information finns i blogg inlägget [Microsoft Graph eller Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) och [migrera Azure AD Graph-appar till Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Azure AD Graph API ger program mässig åtkomst till Azure AD via OData REST API-slutpunkter. Program kan använda Azure AD Graph API för att utföra åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) på katalog data och objekt. Du kan till exempel använda Azure AD Graph API för att skapa en ny användare, Visa eller uppdatera användarens egenskaper, ändra användarens lösen ord, kontrol lera grupp medlemskap för rollbaserad åtkomst, inaktivera eller ta bort användaren. Mer information om Azure AD Graph API funktioner och program scenarier finns i [Azure ad Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) och [Azure AD Graph API-krav](https://msdn.microsoft.com/library/hh974476.aspx). Azure AD Graph API fungerar bara med arbets-eller skol-/organisations konton.

Den här artikeln gäller Azure AD Graph API. Liknande information som rör Microsoft Graph-API finns i [använda Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

## <a name="how-to-construct-a-graph-api-url"></a>Så här skapar du en Graph API-URL

För att få åtkomst till katalog data och objekt (med andra ord, resurser eller entiteter) i Graph API som du vill utföra CRUD-åtgärder på, kan du använda webb adresser som baseras på protokollet Open data (OData). URL: erna som används i Graph API består av fyra huvud delar: tjänst roten, klient-ID, resurs Sök väg och alternativ för frågesträng: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Ta exemplet på följande URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Tjänst rot**: i Azure AD Graph API är tjänst roten alltid https://graph.windows.net.
* **Klient-ID**: det här avsnittet kan vara ett verifierat (registrerat) domän namn i föregående exempel, contoso.com. Det kan också vara ett klient objekt-ID eller "min organisation" eller "Me"-alias. Mer information finns i [adressera entiteter och åtgärder i Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Resurs Sök väg**: det här avsnittet av en URL identifierar den resurs som ska interageras med (användare, grupper, en viss användare eller en viss grupp osv.) I exemplet ovan är det den översta nivån "grupper" för att adressera den resurs uppsättningen. Du kan också adressera en speciell entitet, till exempel "Users/{objectId}" eller "Users/userPrincipalName".
* **Frågeparametrar**: ett frågetecken (?) avgränsar avsnittet resurs Sök väg från avsnittet frågeparametrar. Frågeparametern "API-version" krävs för alla förfrågningar i Azure AD Graph API. Azure AD Graph API stöder även följande alternativ för OData-frågor: **$filter**, **$OrderBy**, **$Expand**, **$Top**och **$format**. Följande frågealternativ stöds inte för närvarande: **$Count**, **$inlinecount**och **$Skip**. Mer information finns i [frågor, filter och växlings alternativ som stöds i Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Graph API versioner

Du anger versionen för en Graph API förfrågan i frågeparametern "API-version". I version 1,5 och senare använder du ett numeriskt versions värde. API-version = 1.6. För tidigare versioner använder du en datum sträng som följer formatet ÅÅÅÅ-MM-DD; till exempel API-version = 2013-11-08. Använd strängen "beta" för för hands versions funktioner. till exempel API-version = beta. Mer information om skillnader mellan Graph API versioner finns i [versions hantering för Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Graph API metadata

Om du vill returnera Azure AD Graph API-metadatafilen lägger du till "$metadata"-segmentet efter klient-ID i URL: en, till exempel följande URL Returnerar metadata för ett demo företag: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Du kan ange den här webb adressen i adress fältet i en webbläsare för att se metadata. CSDL-Metadatadokumentet som returneras beskriver entiteter och komplexa typer, deras egenskaper och de funktioner och åtgärder som exponeras av den version av Graph API du begärde. Om du utelämnar parametern API-version returneras metadata för den senaste versionen.

## <a name="common-queries"></a>Vanliga frågor

[Azure ad Graph API vanliga frågor](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) som visar vanliga frågor som kan användas med Azure AD graf, inklusive frågor som kan användas för att få åtkomst till toppnivå resurser i din katalog och frågor för att utföra åtgärder i din katalog.

`https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` returnerar till exempel företags information för katalogen contoso.com.

Eller `https://graph.windows.net/contoso.com/users?api-version=1.6` visar en lista över alla användar objekt i katalogen contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Använda Azure AD Graph Explorer
Du kan använda Azure AD Graph Explorer för Azure AD-Graph API för att fråga katalog data när du skapar programmet.

Följande skärm bild är de utdata du ser om du skulle navigera till Azure AD Graph Explorer, logga in och ange `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` för att visa alla användare i den inloggade användarens katalog:

![Exempel på utdata i Azure AD Graph API Explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Läs in Azure AD Graph Explorer**: om du vill läsa in verktyget går du till [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/). Klicka på **Logga in** och logga in med dina autentiseringsuppgifter för Azure AD-kontot för att köra Azure AD Graph Explorer mot din klient. Om du kör Azure AD Graph Explorer mot din egen klient måste du eller din administratör godkänna under inloggningen. Om du har en Office 365-prenumeration har du automatiskt en Azure AD-klient. De autentiseringsuppgifter du använder för att logga in på Office 365 är i själva verket Azure AD-konton och du kan använda dessa autentiseringsuppgifter med Azure AD Graph Explorer.

**Kör en fråga**: om du vill köra en fråga skriver du din fråga i text rutan begäran och klickar på **Hämta** eller klicka på **RETUR** -tangenten. Resultaten visas i svars rutan. `https://graph.windows.net/myorganization/groups?api-version=1.6` visar till exempel alla grupp objekt i den inloggade användarens katalog.

Observera följande funktioner och begränsningar i Azure AD Graph Explorer:

* Funktionen Komplettera automatiskt på resurs uppsättningar. Om du vill se den här funktionen klickar du på text rutan för begäran (där företagets URL visas). Du kan välja en resurs uppsättning i list rutan.
* Begär ande historik.
* Stöder adresserings Ali Aset "mig" och "min organisation". Du kan till exempel använda `https://graph.windows.net/me?api-version=1.6` för att returnera användar objektet för den inloggade användaren eller `https://graph.windows.net/myorganization/users?api-version=1.6` för att returnera alla användare i den inloggade användarens katalog.
* Har stöd för fullständiga CRUD-åtgärder mot din egen katalog med hjälp av `POST``GET``PATCH` och `DELETE`.
* Avsnittet svarshuvuden. Det här avsnittet kan användas för att felsöka problem som inträffar när du kör frågor.
* Ett JSON-visningsprogram för svaret med utökade och dolda funktioner.
* Inget stöd för att visa eller ladda upp ett miniatyr foto.

## <a name="using-fiddler-to-write-to-the-directory"></a>Använda Fiddler för att skriva till katalogen

I den här snabb starts guiden kan du använda Fiddler webb fel sökning för att öva på att utföra "Skriv"-åtgärder mot din Azure AD-katalog. Du kan till exempel hämta och ladda upp en användares profil bild (vilket inte är möjligt med Azure AD Graph Explorer). Mer information om hur du installerar Fiddler finns i [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler).

I exemplet nedan använder du Fiddler webb fel sökning för att skapa en ny säkerhets grupp "MyTestGroup" i Azure AD-katalogen.

**Hämta en**åtkomsttoken: för att få åtkomst till Azure AD Graph måste klienterna autentiseras för att kunna autentisera till Azure AD först. Mer information finns i [autentiserings scenarier för Azure AD](v1-authentication-scenarios.md).

**Skriv och kör en fråga**: utför följande steg:

1. Öppna Fiddler-Webbfelsökning och växla till fliken **Composer** .
2. Eftersom du vill skapa en ny säkerhets grupp väljer du **post** som http-metod i den nedrullningsbara menyn. Mer information om åtgärder och behörigheter för ett grupp objekt finns i [gruppen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) i [Azure AD Graph REST API referens](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. I fältet bredvid **post**anger du följande URL för begäran: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > Du måste ersätta {mytenantdomain} med domän namnet för din egen Azure AD-katalog.

4. Skriv följande HTTP-huvud i fältet direkt under post-pull:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Ersätt &lt;din åtkomsttoken&gt; med åtkomsttoken för din Azure AD-katalog.

5. I fältet **begär ande text** skriver du följande JSON:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Mer information om hur du skapar grupper finns i [Skapa grupp](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Mer information om Azure AD-entiteter och typer som exponeras av Graph och information om de åtgärder som kan utföras på dem med Graph finns i [referens för Azure AD-diagram REST API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure AD-Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Läs mer om [behörighets omfattningar för Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
