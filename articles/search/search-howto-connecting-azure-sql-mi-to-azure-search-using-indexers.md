---
title: Azure SQL-hanterad instans anslutning för Sök indexering – Azure Search
description: Aktivera offentlig slut punkt för att tillåta anslutningar till SQL-hanterade instanser från en indexerare på Azure Search.
author: vl8163264128
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: victliu
ms.openlocfilehash: 07bc1a55c0222fda87b28acbaa8bfe552fb8e6ed
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186650"
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-managed-instance"></a>Konfigurera en anslutning från en Azure Search indexerare till SQL-hanterad instans
Som anges i [ansluta Azure SQL Database för att Azure Search med hjälp av indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), stöds att skapa indexerare mot **SQL-hanterade instanser** av Azure Search via den offentliga slut punkten.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Skapa en Azure SQL-hanterad instans med offentlig slut punkt
Skapa en SQL-hanterad instans med alternativet **Aktivera offentlig slut punkt** valt.

   ![Aktivera offentlig slut punkt](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Aktivera offentlig slut punkt")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Aktivera offentlig slut punkt för Azure SQL Managed instance
Du kan också aktivera offentlig slut punkt på en befintligSQL-hanterad instans under**offentlig slut punkt** > för **säkerhet** > **virtuellt nätverk** > .

   ![Aktivera offentlig slut punkt](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Aktivera offentlig slut punkt")

## <a name="verify-nsg-rules"></a>Verifiera NSG-regler
Kontrol lera att nätverks säkerhets gruppen har rätt **inkommande säkerhets regler** som tillåter anslutningar från Azure-tjänster.

   ![NSG inkommande säkerhets regel](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG inkommande säkerhets regel")

## <a name="get-public-endpoint-connection-string"></a>Hämta anslutnings sträng för offentlig slut punkt
Se till att du använder anslutnings strängen för den **offentliga slut punkten** (port 3342, inte port 1433).

   ![Anslutnings sträng för offentlig slut punkt](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Anslutnings sträng för offentlig slut punkt")

## <a name="next-steps"></a>Nästa steg
Med konfigurationen av kan du nu ange en SQL-hanterad instans som data källa för en Azure Search indexerare med hjälp av antingen portalen eller REST API. Mer information finns i [ansluta Azure SQL Database till Azure Search med indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .
