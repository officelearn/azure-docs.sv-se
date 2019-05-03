---
title: Förhandsversion av REST API för Azure Search 2019-05-06-förhandsversion – Azure Search
description: Azure Search Service REST API-versionen 2019-05-06-förhandsversion innehåller experimentella funktioner, till exempel knowledge store och kundhanterad krypteringsnycklar.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 91c58507d8758a65772110afba71354deecd3b12
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024274"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Azure Search Service REST api-versionen 2019-05-06-förhandsversion
Den här artikeln beskriver den `api-version=2019-05-06-Preview` versionen av Azure Search service REST API, erbjuder experimentella funktioner som inte ännu är allmänt tillgänglig.

> [!NOTE]
> Förhandsversionsfunktioner är tillgängliga för testning och experimentering med målet att samla in feedback och kan komma att ändras. Vi att rekommenderar starkt mot använder förhandsversionen av API: er i produktionsprogram.


## <a name="new-in-2019-05-06-preview"></a>Nytt i 2019-05-06-förhandsversion

[**Knowledge store** ](knowledge-store-concept-intro.md) är ett nytt mål för en pipeline för AI-baserade funktioner. Förutom ett index kan du nu behålla fylls i automatiskt datastrukturer som skapades under indexering i Azure storage. Du kan styra fysiska strukturer data med element i en kompetens, inklusive hur data formas om data lagras i tabellagring eller Blob storage och huruvida det finns flera vyer.

[**Kundhanterad krypteringsnycklar** ](search-security-manage-encryption-keys.md) för tjänstsidan kryptering vid vila är också en ny förhandsgranskningsfunktion. Förutom de inbyggda kryptering vid vila hanteras av Microsoft, kan du använda ett extra lager av kryptering där du är ensam ägare till nycklarna.

## <a name="other-preview-features"></a>Andra funktioner i förhandsversion

Funktioner som vi tillkännagav i tidigare förhandsversioner är fortfarande i offentlig förhandsversion. Om du anropar ett API med en tidigare förhandsversion api-version, kan du fortsätta att använda den här versionen eller växla till `2019-05-06-Preview` utan ändringar i förväntat beteende.

+ [moreLikeThis frågeparameter](search-more-like-this.md) söker efter dokument som är relevanta för ett visst dokument. Den här funktionen har varit i tidigare förhandsversioner. 


## <a name="how-to-call-a-preview-api"></a>Hur du anropar en förhandsversionen av API

Äldre förhandsversioner är fortfarande fungerar men blir inaktuella över tid. Om din kod anropar `api-version=2016-09-01-Preview` eller `api-version=2017-11-11-Preview`, anropen fortfarande är giltiga. Dock har endast den senaste förhandsversionen uppdaterats med förbättringar. 

Följande exempel illustrerar ett anrop till förhandsversionen av API-version.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Search-tjänsten är tillgänglig i flera versioner. Mer information finns i [API-versioner](search-api-versions.md).

## <a name="next-steps"></a>Nästa steg

Läs dokumentationen för Azure Search Service REST API-referens. Om du får problem kan be oss hjälp på [StackOverflow](https://stackoverflow.com/) eller [supporten](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Search service REST API-referens](https://docs.microsoft.com/rest/api/searchservice/)