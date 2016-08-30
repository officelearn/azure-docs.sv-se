<properties
    pageTitle="Skicka frågor mot ditt Azure Search-index med hjälp av Azure Portal | Microsoft Azure | Värdbaserad söktjänst i molnet"
    description="Skicka en sökfråga i Sökutforskaren på Azure Portal."
    services="search"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="ashmaka"
/>
# Skicka frågor mot ditt Azure Search-index med hjälp av Azure Portal
> [AZURE.SELECTOR]
- [Översikt](search-query-overview.md)
- [Portalen](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Den här guiden beskriver hur du skickar frågor mot ditt Azure Search-index på Azure Portal.

Innan du påbörjar den här genomgången bör du redan ha [skapat ett Azure Search-index](search-what-is-an-index.md) och [fyllt det med data](search-what-is-data-import.md).

## I. Gå till ditt Azure Search-blad
1. Klicka på ”Alla resurser” på menyn på vänster sida av [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. Välj din Azure Search-tjänst.

## II. Välj det index som du vill söka i
1. Välj det index som du vill söka i från indexpanelen.

![](./media/search-explorer/pick-index.png)

## III. Klicka på panelen ”Sökutforskaren”
![](./media/search-explorer/search-explorer-tile.png)

## III. Starta sökningen
1. Börja söka i Azure Search-indexet genom att skriva i fältet ”*Frågesträng*” och tryck på ”**Sök**”.
 * När du använder Sökutforskaren kan du ange valfria [frågeparametrar](https://msdn.microsoft.com/library/dn798927.aspx)

2. I avsnittet ”*Resultat*” visas frågans resultat i samma JSON-rådataformat som du får i en HTTP-svarstext när du skickar sökförfrågningar mot REST-API:et för Azure Search.
3. Frågesträngen parsas automatiskt till rätt begärande-URL för att sedan skicka en HTTP-begäran mot REST-API:et för Azure Search.

![](./media/search-explorer/search-bar.png)



<!--HONumber=jun16_HO2-->


