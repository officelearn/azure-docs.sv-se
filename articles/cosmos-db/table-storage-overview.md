---
title: Översikt över Azure Table Storage
description: Lär dig hur du använder Azure Table Storage för att lagra flexibla data uppsättningar som användar data för webb program, adress böcker, enhets information eller andra typer av metadata.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: overview
ms.date: 05/20/2019
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: abd88c3cdb54747c21ad8772860a3ed6929dbd15
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91318729"
---
# <a name="azure-table-storage-overview"></a>Översikt över Azure Table Storage

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage är en tjänst som lagrar strukturerade NoSQL-data i molnet och som ger tillgång till ett nyckel-/attributlager med en schemalös design. Eftersom Table Storage är schemalös är det enkelt att anpassa dina data i takt med att programmets behov förändras. Åtkomsten till data i Table Storage är snabb och kostnadseffektiv för många typer av program, och medför normalt lägre kostnad än traditionell SQL för liknande datavolymer.

Du kan använda Table Storage för att lagra flexibla datauppsättningar som användardata för webbprogram, adressböcker, enhetsinformation eller andra typer av metadata som din tjänst kräver. Du kan lagra valfritt antal enheter i en tabell, och ett lagringskonto kan innehålla valfritt antal tabeller, upp till lagringskontots kapacitetsgräns.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Nästa steg

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.

* [Kom igång med tabell-API för Azure Cosmos DB och Azure Table Storage med .NET SDK](table-storage-how-to-use-dotnet.md)

* Fullständig information om tillgängliga API:er finns i referensdokumentationen för tabelltjänsten:

    * [Storage-klientbibliotek för .NET-referens](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [Referens för REST-API](https://msdn.microsoft.com/library/azure/dd179355)
