---
title: Skapa och hantera Azure Database för PostgreSQL-VNet-tjänstslutpunkter och regler med hjälp av Azure CLI
description: Den här artikeln beskriver hur du skapar och hanterar Azure Database for PostgreSQL-VNet-tjänstslutpunkter och regler med hjälp av Azure CLI-kommandoraden.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: 7dc5a49e8b27d1a4e4126bef1cce638373e056fb
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008740"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-using-azure-cli"></a>Skapa och hantera Azure Database för PostgreSQL-VNet-tjänstslutpunkter med Azure CLI
Virtuella nätverk (VNet) services-slutpunkter och regler kan du utöka det privata adressutrymmet för ett virtuellt nätverk till Azure Database för PostgreSQL-server. Med praktiska Azure kommandoradsgränssnitt (CLI) kommandon kan du skapa, uppdatera, ta bort, lista och visa tjänstslutpunkter för virtuellt nätverk och regler för att hantera servern. En översikt över Azure Database för PostgreSQL-VNet-tjänstslutpunkter, inklusive begränsningar, finns i [Azure Database for PostgreSQL Server VNet-tjänstslutpunkter](concepts-data-access-and-security-vnet.md). VNet-tjänstslutpunkter är tillgängliga i alla regioner som stöds för Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Nödvändiga komponenter
För att gå igenom den här guiden, måste du:
- Installera [Azure CLI](/cli/azure/install-azure-cli) eller använda Azure Cloud Shell i webbläsaren.
- En [Azure Database for PostgreSQL-server och databas](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> Stöd för VNet-tjänstslutpunkter är endast för generell användning och Minnesoptimerad servrar.
> VNet peering om trafik flödar genom en gemensam Gateway för virtuellt nätverk med tjänstslutpunkter och ska flöda till peer-datorn kan du skapa en ACL/VNet-regel som tillåter Azure Virtual Machines i Gateway virtuella nätverk till Azure Database for PostgreSQL-server.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Konfigurera Vnet-tjänstslutpunkter för Azure Database för PostgreSQL
Den [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) kommandon används för att konfigurera virtuella nätverk.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). 

Om du kör CLI lokalt, måste du logga in på ditt konto med hjälp av kommandot [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Observera egenskapen **ID** från kommandoutdata för det motsvarande prenumerationsnamnet.
```azurecli-interactive
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Ersätt egenskapen **ID** från **az login**-utdata för din prenumeration i platshållaren för prenumerations-ID.

- Kontot måste ha tillräcklig behörighet för att skapa ett virtuellt nätverk och tjänstslutpunkten.

Tjänstslutpunkter kan konfigureras i virtuella nätverk separat, av en användare med skrivbehörighet för det virtuella nätverket.

Om du vill skydda Azure-tjänstresurser i ett virtuellt nätverk, måste användaren ha behörighet att ”Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/” för undernäten läggas till. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.

Lär dig mer om [inbyggda roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) och att tilldela specifika behörigheter till [anpassade roller](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Virtuella nätverk och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om VNet och Azure-tjänstresurser finns i olika prenumerationer bör resurserna finnas under samma Active Directory (AD)-klient.

> [!IMPORTANT]
> Vi rekommenderar starkt att den här artikeln om tjänsten endpoint konfigurationer och överväganden innan du kör exempelskriptet nedan, eller konfigurera tjänstslutpunkter. **Tjänstslutpunkt för virtuellt nätverk:** En [tjänstslutpunkt för virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät som egenskaper innehåller en eller flera formella Azure-tjänst-typnamn. Services-slutpunkter för virtuellt nätverk använder namnet på tjänsten **Microsoft.Sql**, som refererar till Azure-tjänsten med namnet SQL-databas. Den här tjänsttaggen gäller även för Azure SQL Database, Azure Database för PostgreSQL och MySQL-tjänster. Det är viktigt att tänka på när du använder den **Microsoft.Sql** servicetagg till en VNet-tjänstslutpunkt konfigurerar den för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure Database for PostgreSQL-tjänstens slutpunkt trafik och Azure Database for MySQL-servrar i undernät. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Exempel på skript för att skapa en Azure Database for PostgreSQL-databas, skapa ett virtuellt nätverk, VNet-tjänstslutpunkt och skydda server till undernät med en VNet-regel
I det här exempelskriptet ändrar du markerade rader om du vill anpassa administratörens användarnamn och lösenord. Ersätt prenumerations-ID som används i den `az account set --subscription` med ditt eget prenumerations-ID.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När exempelskriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]
