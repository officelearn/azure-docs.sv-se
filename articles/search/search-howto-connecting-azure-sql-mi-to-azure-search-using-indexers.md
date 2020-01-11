---
title: Azure SQL-hanterad instans anslutning för Sök indexering
titleSuffix: Azure Cognitive Search
description: Aktivera offentlig slut punkt för att tillåta anslutningar till SQL-hanterade instanser från en indexerare på Azure Kognitiv sökning.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0f91775e0175b4b4af9b57fa96e389c3a2a22564
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863129"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Konfigurera en anslutning från en Azure Kognitiv sökning-indexerare till SQL-hanterad instans

Som anges i [ansluta Azure SQL Database till Azure kognitiv sökning med hjälp av indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), stöds att skapa indexerare mot **SQL-hanterade instanser** av Azure kognitiv sökning via den offentliga slut punkten.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Skapa en Azure SQL-hanterad instans med offentlig slut punkt
Skapa en SQL-hanterad instans med alternativet **Aktivera offentlig slut punkt** valt.

   ![Aktivera offentlig slut punkt](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Aktivera offentlig slut punkt")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Aktivera offentlig slut punkt för Azure SQL Managed instance
Du kan också aktivera offentlig slut punkt på en befintlig SQL-hanterad instans under **säkerhets** > **virtuella nätverk** > **offentliga slut punkter** > **Aktivera**.

   ![Aktivera offentlig slut punkt](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Aktivera offentlig slut punkt")

## <a name="verify-nsg-rules"></a>Verifiera NSG-regler
Kontrol lera att nätverks säkerhets gruppen har rätt **inkommande säkerhets regler** som tillåter anslutningar från Azure-tjänster.

   ![NSG inkommande säkerhets regel](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG inkommande säkerhets regel")

> [!NOTE]
> Du kan välja att vara mer restriktiv i den inkommande åtkomsten till din hanterade SQL-instans genom att ersätta den aktuella regeln (`public_endpoint_inbound`) med 2 regler:
>
> * Tillåta inkommande åtkomst från `AzureCognitiveSearch` [service tag gen](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) ("källa" = `AzureCognitiveSearch`)
>
> * Tillåter inkommande åtkomst från IP-adressen för Sök tjänsten, som kan hämtas genom att pinga det fullständigt kvalificerade domän namnet (t. ex. `<your-search-service-name>.search.windows.net`). ("Källa" = `IP address`)

## <a name="get-public-endpoint-connection-string"></a>Hämta anslutnings sträng för offentlig slut punkt
Se till att du använder anslutnings strängen för den **offentliga slut punkten** (port 3342, inte port 1433).

   ![Anslutnings sträng för offentlig slut punkt](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Anslutnings sträng för offentlig slut punkt")

## <a name="next-steps"></a>Nästa steg
Med konfigurationen av kan du nu ange en SQL-hanterad instans som data källa för en Azure Kognitiv sökning-indexerare med hjälp av antingen portalen eller REST API. Mer information finns i [ansluta Azure SQL Database till Azure kognitiv sökning med indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .
