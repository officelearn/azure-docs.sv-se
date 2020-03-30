---
title: Hantera VNet-slutpunkter - Azure-portalen - Azure Database för MySQL
description: Skapa och hantera Azure Database för MySQL VNet-tjänstslutpunkter och regler med hjälp av Azure-portalen
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: fec85f7d87bbfe0039744b9ea63239083ff1c8a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063542"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Skapa och hantera Azure Database for MySQL VNet-tjänstslutpunkter och VNet-regler med hjälp av Azure-portalen
VNet-tjänstslutpunkter och regler utökar det privata adressutrymmet för ett virtuellt nätverk till din Azure Database for MySQL-server. En översikt över slutpunkter för Azure Database for MySQL VNet-tjänst, inklusive begränsningar, finns i [Azure Database for MySQL Server VNet-tjänstslutpunkter](concepts-data-access-and-security-vnet.md). Slutpunkter för VNet-tjänst är tillgängliga i alla regioner som stöds för Azure Database for MySQL.

> [!NOTE]
> Stöd för slutpunkter för VNet-tjänst är endast för servrar med allmänt syfte och minnesoptimerade.
> Om trafiken flödar via en gemensam VNet-gateway med tjänstslutpunkter och ska flöda till peer-enheten, skapar du en ACL/VNet-regel som tillåter virtuella Azure-datorer i gateway-nätverket att komma åt Azure-databasen för MySQL-servern.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Skapa en VNet-regel och aktivera tjänstslutpunkter i Azure-portalen

1. Klicka på **Anslutningssäkerhet** under rubriken Inställningar på sidan MySQL-server för att öppna fönstret Anslutningssäkerhet för Azure Database för MySQL under rubriken Inställningar. 

2. Kontrollera att kontrollen Tillåt åtkomst till Azure-tjänster är inställd **på OFF**.

> [!Important]
> Om du lämnar kontrollen inställd på ON accepterar din Azure MySQL Database-server kommunikation från alla undernät. Att lämna kontrollen inställd på PÅ kan vara överdriven åtkomst från säkerhetssynpunkt. Slutpunktsfunktionen för Tjänsten Microsoft Azure Virtual Network, i samordning med den virtuella nätverksregelfunktionen i Azure Database for MySQL, kan tillsammans minska din säkerhetsyta.

3. Klicka sedan på **+ Lägga till befintliga virtuella nätverk**. Om du inte har ett befintligt virtuellt nätverk kan du klicka på **+ Skapa nytt virtuellt nätverk** för att skapa ett. Se [Snabbstart: Skapa ett virtuellt nätverk med Azure-portalen](../virtual-network/quick-create-portal.md)

   ![Azure portal - klicka på Anslutningssäkerhet](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. Ange ett VNet-regelnamn, välj prenumeration, Virtuellt nätverk och Undernätsnamn och klicka sedan på **Aktivera**. Detta aktiverar automatiskt slutpunkter för VNet-tjänsten i undernätet med hjälp av **Microsoft.SQL-tjänsttaggen.**

   ![Azure portal - konfigurera VNet](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   Kontot måste ha nödvändiga behörigheter för att skapa ett virtuellt nätverk och tjänstslutpunkten.

   Tjänstslutpunkter kan konfigureras i virtuella nätverk oberoende av en användare med skrivåtkomst till det virtuella nätverket.
    
   För att skydda Azure-tjänstresurser till ett virtuellt nätverk måste användaren ha behörighet att "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" för de undernät som läggs till. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.
    
   Lär dig mer om [inbyggda roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) och att tilldela specifika behörigheter till [anpassade roller](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   VNet och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om VNet- och Azure-tjänstresurserna finns i olika prenumerationer bör resurserna finnas under samma Active Directory (AD) -klientorganisation. Kontrollera att båda prenumerationerna har **Microsoft.Sql-resursprovidern** registrerad. Mer information finns i [resurs-manager-registrering][resource-manager-portal]

   > [!IMPORTANT]
   > Vi rekommenderar starkt att du läser den här artikeln om tjänstslutpunktskonfigurationer och överväganden innan du konfigurerar tjänstslutpunkter. **Slutpunkt för tjänsten Virtuellt nätverk:** Slutpunkten [för tjänsten Virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät vars egenskapsvärden innehåller ett eller flera formella Azure-tjänsttypsnamn. Slutpunkter för VNet-tjänster använder tjänsttypsnamnet **Microsoft.Sql**, som refererar till Azure-tjänsten SOM heter SQL Database. Det här tjänstmärket gäller även för Azure SQL Database, Azure Database for PostgreSQL- och MySQL-tjänster. Det är viktigt att notera när du använder **Microsoft.Sql-tjänsttaggen** på en VNet-tjänstslutpunkt som konfigurerar tjänstslutpunktstrafik för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure Database för PostgreSQL och Azure Database för MySQL-servrar i undernätet. 
   > 

5. När du har aktiverat, klicka på **OK** och du kommer att se att VNet-tjänsten slutpunkter är aktiverade tillsammans med en VNet-regel.

   ![VNet-tjänstslutpunkter aktiverade och VNet-regeln har skapats](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Nästa steg
- På samma sätt kan du skript för att [aktivera VNet-tjänstslutpunkter och skapa en VNET-regel för Azure Database för MySQL med Azure CLI](howto-manage-vnet-using-cli.md).
- Mer information om hur du ansluter till en Azure Database for MySQL-server finns i [Anslutningsbibliotek för Azure Database for MySQL](./concepts-connection-libraries.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md