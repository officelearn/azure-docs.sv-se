---
title: Azure SQL-anslutning för hanterad instans för sökindexering
titleSuffix: Azure Cognitive Search
description: Aktivera offentlig slutpunkt för att tillåta anslutningar till SQL-hanterade instanser från en indexerare på Azure Cognitive Search.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964897"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Konfigurera en anslutning från en Azure Cognitive Search-indexerare till SQL-hanterad instans

Som anges i [Ansluta Azure SQL Database till Azure Cognitive Search med indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)stöds skapa indexerare mot **SQL-hanterade instanser** av Azure Cognitive Search via den offentliga slutpunkten.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Skapa Azure SQL-hanterad instans med offentlig slutpunkt
Skapa en SQL-hanterad instans med alternativet **Aktivera offentlig slutpunkt** markerat.

   ![Aktivera offentlig slutpunkt](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Aktivera offentlig slutpunkt")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Aktivera offentlig slutpunkt för Azure SQL-hanterad instans
Du kan också aktivera offentlig slutpunkt på en befintlig SQL-hanterad instans under > **Säkerhetsvirtiskt** >  **Security** > nätverk**Offentlig slutpunkt****Aktivera**.

   ![Aktivera offentlig slutpunkt](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Aktivera offentlig slutpunkt")

## <a name="verify-nsg-rules"></a>Verifiera NSG-regler
Kontrollera att nätverkssäkerhetsgruppen har rätt **inkommande säkerhetsregler** som tillåter anslutningar från Azure-tjänster.

   ![Säkerhetsregel för NSG-inkommande](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Säkerhetsregel för NSG-inkommande")

> [!NOTE]
> Indexerare kräver fortfarande att SQL Managed Instance konfigureras med en offentlig slutpunkt för att läsa data.
> Du kan dock välja att begränsa inkommande åtkomst till den offentliga`public_endpoint_inbound`slutpunkten genom att ersätta den aktuella regeln ( ) med följande två regler:
>
> * Tillåt inkommande åtkomst `AzureCognitiveSearch` från [tjänstmärket](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) ("KÄLLA" `AzureCognitiveSearch`= `cognitive_search_inbound`, "NAME" = )
>
> * Tillåter inkommande åtkomst från IP-adressen för söktjänsten, som kan erhållas genom att `<your-search-service-name>.search.windows.net`pinga sitt fullt kvalificerade domännamn (t.ex.. ). ("KÄLLA" `IP address`= , "NAMN" = `search_service_inbound`)
>
> För var och en av dessa två `3342`regler anger `TCP`du "PORT" `Any`= , "PROTOKOLL" = , "DESTINATION" = , "ACTION" =`Allow`

## <a name="get-public-endpoint-connection-string"></a>Hämta den offentliga anslutningssträngen för slutpunkt
Se till att du använder anslutningssträngen för den **offentliga slutpunkten** (port 3342, inte port 1433).

   ![Anslutningssträng för offentlig slutpunkt](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Anslutningssträng för offentlig slutpunkt")

## <a name="next-steps"></a>Nästa steg
Med konfiguration ur vägen kan du nu ange en SQL-hanterad instans som datakälla för en Azure Cognitive Search-indexerare med hjälp av antingen portalen eller REST API. Mer information [finns i Ansluta Azure SQL Database till Azure Cognitive Search med indexerare.](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
