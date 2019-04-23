---
title: Skapa och hantera Azure Database for MariaDB VNet-tjänstslutpunkter och regler med hjälp av Azure-portalen | Microsoft Docs
description: Skapa och hantera Azure Database for MariaDB VNet-tjänstslutpunkter och regler med hjälp av Azure-portalen
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 6644b6ae3a9482a1bd3f840a814d3bb6361517fc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790067"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Skapa och hantera Azure Database for MariaDB VNet-tjänstslutpunkter och VNet-regler med hjälp av Azure portal

Virtuella nätverk (VNet) services-slutpunkter och regler kan du utöka det privata adressutrymmet för ett virtuellt nätverk till Azure Database for MariaDB-server. En översikt över Azure Database för MariaDB VNet-tjänstslutpunkter, inklusive begränsningar, finns i [Azure Database for MariaDB Server VNet-tjänstslutpunkter](concepts-data-access-security-vnet.md). VNet-tjänstslutpunkter är tillgängliga i alla regioner som stöds för Azure Database for MariaDB.

> [!NOTE]
> Stöd för VNet-tjänstslutpunkter är endast för generell användning och Minnesoptimerad servrar.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Skapa en VNet-regel och aktivera Tjänsteslutpunkter

1. På sidan MariaDB, under inställningar klickar du **anslutningssäkerhet** att öppna fönstret anslutningssäkerhet för Azure Database for MariaDB.

2. Se till att Tillåt åtkomst till Azure-tjänster kontroll har angetts till **OFF**.

> [!Important]
> Om du ställer in det på ON accepterar Azure MariaDB Database-servern kommunikation från alla undernät. Lämna inställt på på kontrollen kan vara långa åtkomst från en säkerhetssynpunkt. Microsoft Azure Virtual Network service endpoint-funktionen, tillsammans med virtuellt nätverk regeln funktionen i Azure Database for MariaDB, kan tillsammans minska ytan din säkerhet.

3. Klicka på **+ att lägga till befintligt virtuellt nätverk**. Om du inte har ett befintligt VNet kan du klicka **+ Skapa nytt virtuellt nätverk** att skapa en. Gå till [Snabbstart: Skapa ett virtuellt nätverk med Azure portal](../virtual-network/quick-create-portal.md)

   ![Azure portal – Klicka på anslutningssäkerhet](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Ange ett VNet-Regelnamn, Välj prenumeration, virtuellt nätverk och namn på undernät och klicka sedan på **aktivera**. Detta aktiverar automatiskt VNet-tjänstslutpunkter på undernät med den **Microsoft.SQL** tjänsttaggen.

   ![Azure portal – konfigurera virtuellt nätverk](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   Kontot måste ha tillräcklig behörighet för att skapa ett virtuellt nätverk och tjänstslutpunkten.

   Tjänstslutpunkter kan konfigureras i virtuella nätverk separat, av en användare med skrivbehörighet för det virtuella nätverket.
    
   Om du vill skydda Azure-tjänstresurser i ett virtuellt nätverk, måste användaren ha behörighet att ”Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/” för undernäten läggas till. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.
    
   Lär dig mer om [inbyggda roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) och att tilldela specifika behörigheter till [anpassade roller](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   Virtuella nätverk och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om VNet och Azure-tjänstresurser finns i olika prenumerationer bör resurserna finnas under samma Active Directory (AD)-klient.

   > [!IMPORTANT]
   > Vi rekommenderar starkt att den här artikeln om tjänsten endpoint konfigurationer och överväganden innan du konfigurerar tjänstslutpunkter. **Tjänstslutpunkt för virtuellt nätverk:** En [tjänstslutpunkt för virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät som egenskaper innehåller en eller flera formella Azure-tjänst-typnamn. Services-slutpunkter för virtuellt nätverk använder namnet på tjänsten **Microsoft.Sql**, som refererar till Azure-tjänsten med namnet SQL-databas. Den här tjänsttaggen gäller även för Azure SQL Database, Azure Database for MariaDB, PostgreSQL och MySQL-tjänster. Det är viktigt att tänka på när du använder den **Microsoft.Sql** servicetagg till en VNet-tjänstslutpunkt konfigurerar den för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure Database för PostgreSQL-tjänstens slutpunkt trafik Azure Database for MariaDB och Azure Database för MySQL-servrar i undernät.
   > 

5. När du har aktiverat, klickar du på **OK** så ser du att VNet-tjänstslutpunkter aktiveras tillsammans med en VNet-regel.

   ![VNet-tjänstslutpunkter och VNet-regel skapas](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Nästa steg
- Läs mer om [konfigurerar SSL på Azure Database for MariaDB](howto-configure-ssl.md)
- På samma sätt kan du skapa skript till [aktivera VNet tjänstens slutpunkter och skapar en VNET-regel för Azure Database for MariaDB med Azure CLI](howto-manage-vnet-cli.md).
