---
title: Skapa och hantera Azure Database för PostgreSQL-VNet-tjänstslutpunkter och regler med hjälp av Azure portal | Microsoft Docs
description: Skapa och hantera Azure Database för PostgreSQL-VNet-tjänstslutpunkter och regler med hjälp av Azure portal
services: postgresql
author: mbolz
ms.author: mbolz
manager: kfile
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 08/15/2018
ms.openlocfilehash: af5df507bd7a825ddfaaa7518691cb5fbec34d47
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42058820"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Skapa och hantera Azure Database för PostgreSQL-VNet-tjänstslutpunkter och VNet-regler med hjälp av Azure portal
Virtuella nätverk (VNet) services-slutpunkter och regler kan du utöka det privata adressutrymmet för ett virtuellt nätverk till Azure Database för PostgreSQL-server. En översikt över Azure Database för PostgreSQL-VNet-tjänstslutpunkter, inklusive begränsningar, finns i [Azure Database for PostgreSQL Server VNet-tjänstslutpunkter](concepts-data-access-and-security-vnet.md). VNet-tjänstslutpunkter är tillgängliga i alla regioner som stöds för Azure Database for PostgreSQL.

> [!NOTE]
> Stöd för VNet-tjänstslutpunkter är endast för generell användning och Minnesoptimerad servrar.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Skapa en VNet-regel och aktivera tjänstslutpunkter i Azure portal

1. På sidan PostgreSQL-server under inställningar klickar du **anslutningssäkerhet** att öppna fönstret anslutningssäkerhet för Azure Database för PostgreSQL. Klicka på **+ att lägga till befintligt virtuellt nätverk**. Om du inte har ett befintligt VNet kan du klicka **+ Skapa nytt virtuellt nätverk** att skapa en. Se [Snabbstart: skapa ett virtuellt nätverk med Azure portal](../virtual-network/quick-create-portal.md)

   ![Azure portal – Klicka på anslutningssäkerhet](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Ange ett VNet-Regelnamn, Välj prenumeration, virtuellt nätverk och namn på undernät och klicka sedan på **aktivera**. Detta aktiverar automatiskt VNet-tjänstslutpunkter på undernät med den **Microsoft.SQL** tjänsttaggen.

   ![Azure portal – konfigurera virtuellt nätverk](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > Vi rekommenderar starkt att den här artikeln om tjänsten endpoint konfigurationer och överväganden innan du konfigurerar tjänstslutpunkter. **Tjänstslutpunkt för virtuellt nätverk:** A [tjänstslutpunkt för virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät som egenskaper innehåller en eller flera formella Azure-tjänst-typnamn. Services-slutpunkter för virtuellt nätverk använder namnet på tjänsten **Microsoft.Sql**, som refererar till Azure-tjänsten med namnet SQL-databas. Den här tjänsttaggen gäller även för Azure SQL Database, Azure Database för PostgreSQL och MySQL-tjänster. Det är viktigt att tänka på när du använder den **Microsoft.Sql** servicetagg till en VNet-tjänstslutpunkt konfigurerar den för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure Database for PostgreSQL-tjänstens slutpunkt trafik och Azure Database for MySQL-servrar i undernät. 
   > 

3. När du har aktiverat, klickar du på **OK** så ser du att VNet-tjänstslutpunkter aktiveras tillsammans med en VNet-regel.

   ![VNet-tjänstslutpunkter och VNet-regel skapas](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Nästa steg
- På samma sätt kan du skapa skript till [aktivera VNet tjänstens slutpunkter och skapa en VNET-regel för Azure Database för PostgreSQL med Azure CLI](howto-manage-vnet-using-cli.md).
- Hjälp med att ansluta till en Azure Database for PostgreSQL-server finns i [anslutningsbibliotek för Azure Database for PostgreSQL](./concepts-connection-libraries.md)
