---
title: Skapa och hantera Azure-databas för PostgreSQL VNet slutpunkter och regler som använder Azure portal | Microsoft Docs
description: Skapa och hantera Azure-databas för PostgreSQL VNet slutpunkter och regler som använder Azure portal
services: postgresql
author: mbolz
ms.author: mbolz
manager: kfile
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 8199bd98b9ae091ff2b27efa8334e8e763879359
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757373"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Skapa och hantera Azure-databas för slutpunkter för PostgreSQL VNet och VNet regler med hjälp av Azure portal
Virtuella nätverk (VNet) services-slutpunkter och regler kan du utöka det privata adressutrymmet i ett virtuellt nätverk till din Azure-databas för PostgreSQL-servern. En översikt över Azure-databas för PostgreSQL VNet slutpunkter, inklusive begränsningar, finns i [Azure-databas för PostgreSQL Server VNet Tjänsteslutpunkter](concepts-data-access-and-security-vnet.md). Slutpunkter för virtuella nätverk är tillgänglig som förhandsversion i alla regioner som stöds för Azure-databas för PostgreSQL.

> [!NOTE]
> Stöd för VNet slutpunkter stöds endast för generella och Minnesoptimerade servrar.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Skapa en regel för virtuella nätverk och aktivera slutpunkter i Azure-portalen

1. På sidan PostgreSQL-servern under inställningarna rubrik, klickar du på **anslutningssäkerhet** att öppna fönstret anslutningssäkerhet för Azure-databas för PostgreSQL. Klicka på **+ befintliga virtuellt nätverks läggs**. Om du inte har ett befintligt virtuellt nätverk kan du klicka på **+ Skapa nytt virtuellt nätverk** att skapa en. Se [Snabbstart: skapa ett virtuellt nätverk med Azure-portalen](../virtual-network/quick-create-portal.md)

   ![Azure portal – Klicka på anslutningssäkerhet](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Ange ett namn för VNet-regeln, Välj prenumerationen, virtuella nätverk och undernätsnamn och klicka sedan på **aktivera**. Detta aktiverar automatiskt VNet slutpunkter på undernätet med den **Microsoft.SQL** tjänsten taggen.

   ![Azure portal – konfigurera VNet](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > Vi rekommenderar starkt att den här artikeln om tjänsten endpoint konfigurationer och överväganden innan du konfigurerar slutpunkter. **Virtuella nätverk tjänstslutpunkten:** A [virtuellt nätverk tjänstslutpunkten](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät vars egenskapsvärden innehåller en eller flera formella Azure-tjänst-typnamn. VNet services-slutpunkter använda Tjänsttypnamnet **Microsoft.Sql**, som refererar till Azure-tjänsten med namnet SQL-databas. Den här tjänsten taggen gäller även för Azure SQL Database, Azure-databas för PostgreSQL och MySQL-tjänster. Det är viktigt att känna till när du använder den **Microsoft.Sql** tjänsten taggen till en tjänstslutpunkt för virtuella nätverk konfigureras för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure-databas för PostgreSQL-tjänstens slutpunkt trafik och Azure-databas för MySQL-servrar i undernätet. 
   > 

3. När du har aktiverat, klickar du på **OK** och du ser att VNet Tjänsteslutpunkter är aktiverade tillsammans med en VNet-regel.

   ![VNet Tjänsteslutpunkter aktiverad och VNet-regel skapas](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Nästa steg
- På liknande sätt kan du skapa skript för att [aktivera VNet tjänstens slutpunkter och skapa en VNET-regel för Azure-databas för PostgreSQL med Azure CLI](howto-manage-vnet-using-cli.md).
- Hjälp med att ansluta till en Azure-databas för PostgreSQL-servern finns [anslutningsbibliotek för Azure-databas för PostgreSQL](./concepts-connection-libraries.md)
