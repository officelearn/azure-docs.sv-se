---
title: Ange en krypteringsnyckel på en begäran till Blob-lagring
titleSuffix: Azure Storage
description: Klienter som gör begäranden mot Azure Blob-lagring har möjlighet att ange en krypteringsnyckel per begäran (förhandsversion). Inklusive krypteringsnyckeln på begäran ger detaljerad kontroll över krypteringsinställningar för Blob-lagringsåtgärder.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c8a5555c5c33255fdc5902a115e7e9103a4e936f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79410069"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage-preview"></a>Ange en krypteringsnyckel på en begäran till Blob-lagring (förhandsversion)

Klienter som gör begäranden mot Azure Blob-lagring har möjlighet att ange en krypteringsnyckel per begäran (förhandsversion). Inklusive krypteringsnyckeln på begäran ger detaljerad kontroll över krypteringsinställningar för Blob-lagringsåtgärder. Nycklar som tillhandahålls av kunden kan lagras i Azure Key Vault eller i en annan nyckelbutik.

## <a name="encrypting-read-and-write-operations"></a>Kryptera läs- och skrivåtgärder

När ett klientprogram tillhandahåller en krypteringsnyckel på begäran utför Azure Storage kryptering och dekryptering transparent när du läser och skriver blob-data. Azure Storage skriver en SHA-256-hash av krypteringsnyckeln tillsammans med blobens innehåll. Hash används för att verifiera att alla efterföljande åtgärder mot bloben använder samma krypteringsnyckel.

Azure Storage lagrar eller hanterar inte krypteringsnyckeln som klienten skickar med begäran. Nyckeln ignoreras på ett säkert sätt så snart krypterings- eller dekrypteringsprocessen är klar.

När en klient skapar eller uppdaterar en blob med hjälp av en kundad nyckel på begäran, måste efterföljande läs- och skrivbegäranden för den bloben också ange nyckeln. Om nyckeln inte tillhandahålls på en begäran om en blob som redan har krypterats med en kund-som nyckel, misslyckas begäran med felkod 409 (Konflikt).

Om klientprogrammet skickar en krypteringsnyckel på begäran och lagringskontot också krypteras med en Microsoft-hanterad nyckel eller en kundhanterad nyckel, använder Azure Storage nyckeln som tillhandahålls på begäran om kryptering och dekryptering.

Om du vill skicka krypteringsnyckeln som en del av begäran måste en klient upprätta en säker anslutning till Azure Storage med HTTPS.

Varje blob-ögonblicksbild kan ha sin egen krypteringsnyckel.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Begär rubriker för att ange nycklar som tillhandahålls av kunden

För REST-anrop kan klienter använda följande rubriker för att på ett säkert sätt vidarebefordra krypteringsnyckelinformation på en begäran till Blob-lagring:

|Begärandehuvud | Beskrivning |
|---------------|-------------|
|`x-ms-encryption-key` |Krävs för både skriv- och läsbegäranden. Ett Base64-kodat AES-256-krypteringsnyckelvärde. |
|`x-ms-encryption-key-sha256`| Krävs för både skriv- och läsbegäranden. Krypteringsnyckeln Base64-kodad SHA256. |
|`x-ms-encryption-algorithm` | Krävs för skrivbegäranden, valfria för läsbegäranden. Anger vilken algoritm som ska användas när data krypteras med den angivna nyckeln. Måste vara AES256. |

Det är valfritt att ange krypteringsnycklar på begäran. Men om du anger ett av rubrikerna som anges ovan för en skrivåtgärd måste du ange dem alla.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Blob-lagringsåtgärder som stöder nycklar som tillhandahålls av kunder

Följande Blob-lagringsåtgärder stöder att skicka krypteringsnycklar som tillhandahålls av kunder på en begäran:

- [Placera blob](/rest/api/storageservices/put-blob)
- [Placera blocklista](/rest/api/storageservices/put-block-list)
- [Sätt block](/rest/api/storageservices/put-block)
- [Placera Block från URL](/rest/api/storageservices/put-block-from-url)
- [Placera sida](/rest/api/storageservices/put-page)
- [Placera sida från URL](/rest/api/storageservices/put-page-from-url)
- [Lägg till block](/rest/api/storageservices/append-block)
- [Ange blob-egenskaper](/rest/api/storageservices/set-blob-properties)
- [Ange Blob-metadata](/rest/api/storageservices/set-blob-metadata)
- [Hämta Blob](/rest/api/storageservices/get-blob)
- [Hämta blobegenskaper](/rest/api/storageservices/get-blob-properties)
- [Hämta Blob-metadata](/rest/api/storageservices/get-blob-metadata)
- [Ta ögonblicksbild av blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Rotera nycklar som tillhandahålls av kunden

Om du vill rotera en krypteringsnyckel som användes för att kryptera en blob hämtar du blobben och laddar sedan upp den igen med den nya krypteringsnyckeln.

> [!IMPORTANT]
> Azure-portalen kan inte användas för att läsa från eller skriva till en behållare eller blob som är krypterad med en nyckel som tillhandahålls på begäran.
>
> Var noga med att skydda krypteringsnyckeln som du anger på en begäran till Blob-lagring i ett säkert nyckelarkiv som Azure Key Vault. Om du försöker skriva en skrivåtgärd på en behållare eller blob utan krypteringsnyckeln misslyckas åtgärden och du förlorar åtkomsten till objektet.

## <a name="next-steps"></a>Nästa steg

- [Ange en kundklämmen på en begäran till Blob-lagring med .NET](../blobs/storage-blob-customer-provided-key.md)
- [Azure Storage-kryptering för data i vila](storage-service-encryption.md)
