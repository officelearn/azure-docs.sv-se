---
title: Översikt över Azure Table Storage | Microsoft Docs
description: Lagra strukturerade data i molnet med hjälp av Azure Table Storage, en NoSQL-databas.
services: cosmos-db
documentationcenter: .net
author: SnehaGunda
manager: kfile
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2017
ms.author: sngun
ms.openlocfilehash: 6047f105fc3948ad5ed51ddb172ca7ffa9153cd8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-table-storage-overview"></a>Översikt över Azure Table Storage

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage är en tjänst som lagrar strukturerade NoSQL-data i molnet och som ger tillgång till ett nyckel-/attributlager med en schemalös design. Eftersom Table Storage är schemalös är det enkelt att anpassa dina data i takt med att programmets behov förändras. Åtkomsten till data i Table Storage är snabb och kostnadseffektiv för många typer av program, och medför normalt lägre kostnad än traditionell SQL för liknande datavolymer.

Du kan använda Table Storage för att lagra flexibla datauppsättningar som användardata för webbprogram, adressböcker, enhetsinformation eller andra typer av metadata som din tjänst kräver. Du kan lagra valfritt antal enheter i en tabell, och ett lagringskonto kan innehålla valfritt antal tabeller, upp till lagringskontots kapacitetsgräns.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>Nästa steg

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.

* [Komma igång med Azure Table Storage i .NET](table-storage-how-to-use-dotnet.md)

* Fullständig information om tillgängliga API:er finns i referensdokumentationen för tabelltjänsten:

    * [Storage-klientbibliotek för .NET-referens](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [REST API-referens](http://msdn.microsoft.com/library/azure/dd179355)
