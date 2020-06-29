---
title: Konfigurera inställningar för nätverks routning (för hands version)
titleSuffix: Azure Storage
description: Konfigurera inställningar för nätverks routning (för hands version) för ditt Azure Storage-konto för att ange hur nätverks trafik dirigeras till ditt konto från klienter via Internet.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: santoshc
ms.reviewer: tamram
ms.subservice: common
ms.openlocfilehash: 5b4a1b1f27dff059090d78e24a6a0eca0bbbf01f
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2020
ms.locfileid: "85514198"
---
# <a name="configure-network-routing-preference-for-azure-storage-preview"></a>Konfigurera inställningar för nätverks routning för Azure Storage (för hands version)

Du kan konfigurera inställningar för nätverks [routning](../../virtual-network/routing-preference-overview.md) (för hands version) för ditt Azure Storage-konto för att ange hur nätverks trafik dirigeras till ditt konto från klienter via Internet. Som standard dirigeras trafik från Internet till den offentliga slut punkten för ditt lagrings konto över [Microsofts globala nätverk](../../networking/microsoft-global-network.md). Azure Storage innehåller fler alternativ för att konfigurera hur trafik dirigeras till ditt lagrings konto.

Genom att konfigurera cirkulations inställningen får du flexibiliteten att optimera trafiken antingen för förstklassiga nätverks prestanda eller för kostnad. När du konfigurerar en cirkulations inställning anger du hur trafiken ska dirigeras till den offentliga slut punkten för ditt lagrings konto som standard. Du kan också publicera dirigerade slut punkter för ditt lagrings konto.

## <a name="microsoft-global-network-versus-internet-routing"></a>Microsoft globalt nätverk jämfört med Internet routning

Som standard har klienter utanför Azure-miljön åtkomst till ditt lagrings konto över Microsofts globala nätverk. Microsofts globala nätverk är optimerat för val av sökväg med låg latens för att leverera förstklassiga nätverks prestanda med hög tillförlitlighet. Både inkommande och utgående trafik dirigeras genom den POP (Point of närvaro) som är närmast klienten. Den här standardroutnings konfigurationen säkerställer att trafik till och från ditt lagrings konto passerar över Microsofts globala nätverk för den stora sökvägen, vilket maximerar nätverkets prestanda.

Du kan ändra konfigurationen för routning för ditt lagrings konto så att både inkommande och utgående trafik dirigeras till och från klienter utanför Azure-miljön via den POP som ligger närmast lagrings kontot. Den här vägen minimerar genom gången av din trafik över Microsofts globala nätverk, som lämnar den till överförings leverantören så snart som möjligt. Användning av den här konfigurationen sänker nätverks kostnaderna.

Följande diagram visar hur trafiken flödar mellan klienten och lagrings kontot för varje flödes inställning:

![Översikt över alternativ för routning för Azure Storage](media/network-routing-preference/routing-options-diagram.png)

Mer information om routnings inställningar i Azure finns i [Vad är cirkulations inställningar (för hands version)?](../../virtual-network/routing-preference-overview.md).

## <a name="routing-configuration"></a>Konfiguration av Routning

Du kan välja mellan Microsofts globala nätverk och Internet routning som standardinställning för routning för den offentliga slut punkten för ditt lagrings konto. Inställningen standard routning gäller för all trafik från klienter utanför Azure och påverkar slut punkterna för Azure Data Lake Storage Gen2, Blob Storage, Azure Files och statiska webbplatser. Det går inte att konfigurera cirkulations inställningar för Azure-köer eller Azure-tabeller.

Du kan också publicera dirigerade slut punkter för ditt lagrings konto. När du publicerar cirkulations bara slut punkter skapar Azure Storage nya offentliga slut punkter för ditt lagrings konto som dirigerar trafik över den önskade sökvägen. Den här flexibiliteten gör det möjligt att dirigera trafik till ditt lagrings konto över en viss väg utan att ändra standard inställningarna för routning.

Om du till exempel publicerar en Internet Route-bestämd slut punkt för "StorageAccountA" kommer att publicera följande slut punkter för ditt lagrings konto:

| Lagrings tjänst        | Dirigerad slut punkt                                  |
| :--------------------- | :------------------------------------------------------- |
| Blob Service           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| Fil tjänst           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| Statiska webbplatser        | `StorageAccountA-internetrouting.web.core.windows.net`   |

Om du har en Read-Access Geo-redundant lagring (RA-GRS) eller ett lagrings konto med Läs åtkomst för geo-zon-redundant lagring (RA-GZRS) skapas automatiskt de motsvarande slut punkterna i den sekundära regionen för Läs behörighet.

| Lagrings tjänst        | Dirigerad skrivskyddad sekundär slut punkt                        |
| :--------------------- | :----------------------------------------------------------------- |
| Blob Service           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| Fil tjänst           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| Statiska webbplatser        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

Anslutnings strängarna för de publicerade flödes bara slut punkterna kan kopieras via [Azure Portal](https://portal.azure.com). Dessa anslutnings strängar kan användas för auktorisering av delad nyckel med alla befintliga Azure Storage SDK: er och API: er.

## <a name="about-the-preview"></a>Om för hands versionen

Dirigerings inställningar för Azure Storage är tillgänglig i följande regioner:

- Frankrike, södra
- USA, norra centrala
- USA, västra centrala

Följande kända problem påverkar förhands granskningen av inställningarna för routning för Azure Storage:

- Åtkomst begär Anden för den dirigerade slut punkten för det globala Microsoft-nätverket fungerar inte med HTTP-fel 404 eller motsvarande. Routning över Microsofts globala nätverk fungerar som förväntat när det har angetts som standard cirkulations inställningar för den offentliga slut punkten.

## <a name="pricing-and-billing"></a>Priser och fakturering

Information om priser och fakturering finns i avsnittet **prissättning** i [Vad är cirkulations inställningar (för hands version)?](../../virtual-network/routing-preference-overview.md#pricing).

## <a name="next-steps"></a>Nästa steg

- [Vad är cirkulations inställningar (för hands version)?](../../virtual-network/routing-preference-overview.md)
- [Konfigurera Azure Storage brand väggar och virtuella nätverk](storage-network-security.md)
- [Säkerhets rekommendationer för Blob Storage](../blobs/security-recommendations.md)
