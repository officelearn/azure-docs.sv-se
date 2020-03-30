---
title: Hantera VNet-slutpunkter - Azure-portalen - Azure Database för MariaDB
description: Skapa och hantera Azure Database för MariaDB VNet-tjänstslutpunkter och regler med Hjälp av Azure-portalen
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 61a8337536f55ceda9bef5b7eaa67a37644d2aca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530605"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Skapa och hantera Azure Database för MariaDB VNet-tjänstslutpunkter och VNet-regler med hjälp av Azure-portalen

VNet-tjänstslutpunkter och regler utökar det privata adressutrymmet för ett virtuellt nätverk till din Azure Database for MariaDB-server. En översikt över slutpunkter för Azure Database för MariaDB VNet-tjänst, inklusive begränsningar, finns i [Azure Database for MariaDB Server VNet-tjänstslutpunkter](concepts-data-access-security-vnet.md). Slutpunkter för VNet-tjänst är tillgängliga i alla regioner som stöds för Azure Database för MariaDB.

> [!NOTE]
> Stöd för slutpunkter för VNet-tjänst är endast för servrar med allmänt syfte och minnesoptimerade.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Skapa en VNet-regel och aktivera tjänstslutpunkter

1. Klicka på **Anslutningssäkerhet** under rubriken Inställningar på sidan MariaDB-server för att öppna fönstret Anslutningssäkerhet för Azure Database för MariaDB under rubriken Inställningar.

2. Kontrollera att kontrollen Tillåt åtkomst till Azure-tjänster är inställd **på OFF**.

> [!Important]
> Om du ställer in den på PÅ accepterar din Azure MariaDB-databasserver kommunikation från alla undernät. Att lämna kontrollen inställd på PÅ kan vara överdriven åtkomst från säkerhetssynpunkt. Slutpunktsfunktionen för Tjänsten Microsoft Azure Virtual Network, i samordning med den virtuella nätverksregelfunktionen i Azure Database för MariaDB, kan tillsammans minska din säkerhetsyta.

3. Klicka sedan på **+ Lägga till befintliga virtuella nätverk**. Om du inte har ett befintligt virtuellt nätverk kan du klicka på **+ Skapa nytt virtuellt nätverk** för att skapa ett. Se [Snabbstart: Skapa ett virtuellt nätverk med Azure-portalen](../virtual-network/quick-create-portal.md)

   ![Azure portal - klicka på Anslutningssäkerhet](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Ange ett VNet-regelnamn, välj prenumeration, Virtuellt nätverk och Undernätsnamn och klicka sedan på **Aktivera**. Detta aktiverar automatiskt slutpunkter för VNet-tjänsten i undernätet med hjälp av **Microsoft.SQL-tjänsttaggen.**

   ![Azure portal - konfigurera VNet](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   Kontot måste ha nödvändiga behörigheter för att skapa ett virtuellt nätverk och tjänstslutpunkten.

   Tjänstslutpunkter kan konfigureras i virtuella nätverk oberoende av en användare med skrivåtkomst till det virtuella nätverket.
    
   För att skydda Azure-tjänstresurser till ett virtuellt nätverk måste användaren ha behörighet att "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" för de undernät som läggs till. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.
    
   Lär dig mer om [inbyggda roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) och att tilldela specifika behörigheter till [anpassade roller](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   VNet och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om VNet- och Azure-tjänstresurserna finns i olika prenumerationer bör resurserna finnas under samma Active Directory (AD) -klientorganisation. Kontrollera att båda prenumerationerna har **Microsoft.Sql-resursprovidern** registrerad. Mer information finns i [resurs-manager-registrering][resource-manager-portal]

   > [!IMPORTANT]
   > Vi rekommenderar starkt att du läser den här artikeln om tjänstslutpunktskonfigurationer och överväganden innan du konfigurerar tjänstslutpunkter. **Slutpunkt för tjänsten Virtuellt nätverk:** Slutpunkten [för tjänsten Virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät vars egenskapsvärden innehåller ett eller flera formella Azure-tjänsttypsnamn. Slutpunkter för VNet-tjänster använder tjänsttypsnamnet **Microsoft.Sql**, som refererar till Azure-tjänsten SOM heter SQL Database. Det här tjänstmärket gäller även för Azure SQL Database, Azure Database for MariaDB, PostgreSQL och MySQL-tjänster. Det är viktigt att notera när du använder **Microsoft.Sql-tjänsttaggen** på en VNet-tjänstslutpunkt som konfigurerar tjänstslutpunktstrafik för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure Database for PostgreSQL, Azure Database för MariaDB och Azure Database for MySQL-servrar i undernätet.
   > 

5. När du har aktiverat, klicka på **OK** och du kommer att se att VNet-tjänsten slutpunkter är aktiverade tillsammans med en VNet-regel.

   ![VNet-tjänstslutpunkter aktiverade och VNet-regeln har skapats](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Nästa steg
- Läs mer om [att konfigurera SSL i Azure Database för MariaDB](howto-configure-ssl.md)
- På samma sätt kan du skript för att [aktivera VNet-tjänstslutpunkter och skapa en VNET-regel för Azure Database för MariaDB med Azure CLI](howto-manage-vnet-cli.md).

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md