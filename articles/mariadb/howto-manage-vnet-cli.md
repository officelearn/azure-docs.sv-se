---
title: Skapa och hantera Azure Database for MariaDB VNet-tjänstslutpunkter och regler med hjälp av Azure CLI | Microsoft Docs
description: Den här artikeln beskriver hur du skapar och hanterar Azure Database for MariaDB VNet-tjänstslutpunkter och regler med hjälp av Azure CLI-kommandoraden.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 11/20/2018
ms.openlocfilehash: bfa55c0a91473a4adc6b9fb02ac9697208ef39f9
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540069"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Skapa och hantera Azure-databas för MariaDB VNet-tjänstslutpunkter med Azure CLI

> [!IMPORTANT]
> Funktionen med tjänstslutpunkt för virtuellt nätverk är i offentlig förhandsversion.

Virtuella nätverk (VNet) services-slutpunkter och regler kan du utöka det privata adressutrymmet för ett virtuellt nätverk till Azure Database for MariaDB-server. Med praktiska Azure kommandoradsgränssnitt (CLI) kommandon kan du skapa, uppdatera, ta bort, lista och visa tjänstslutpunkter för virtuellt nätverk och regler för att hantera servern. En översikt över Azure Database för MariaDB VNet-tjänstslutpunkter, inklusive begränsningar, finns i [Azure Database for MariaDB Server VNet-tjänstslutpunkter](concepts-data-access-security-vnet.md). VNet-tjänstslutpunkter är tillgängliga i alla regioner som stöds för Azure Database for MariaDB.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här guiden, måste du:
- Installera [Azure CLI](/cli/azure/install-azure-cli) eller använda Azure Cloud Shell i webbläsaren.
- En [Azure Database for MariaDB-servern och databasen](quickstart-create-mariadb-server-database-using-azure-cli.md).

> [!NOTE]
> Stöd för VNet-tjänstslutpunkter är endast för generell användning och Minnesoptimerad servrar.

## <a name="configure-vnet-service-endpoints"></a>Konfigurera tjänstslutpunkter för virtuellt nätverk
Den [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) kommandon används för att konfigurera virtuella nätverk.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

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
> Vi rekommenderar starkt att den här artikeln om tjänsten endpoint konfigurationer och överväganden innan du konfigurerar tjänstslutpunkter. **Tjänstslutpunkt för virtuellt nätverk:** En [tjänstslutpunkt för virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät som egenskaper innehåller en eller flera formella Azure-tjänst-typnamn. Services-slutpunkter för virtuellt nätverk använder namnet på tjänsten **Microsoft.Sql**, som refererar till Azure-tjänsten med namnet SQL-databas. Den här tjänsttaggen gäller även för Azure SQL Database, Azure Database for MariaDB, PostgreSQL och MySQL-tjänster. Det är viktigt att tänka på när du använder den **Microsoft.Sql** servicetagg till en VNet-tjänstslutpunkt konfigurerar den för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure Database för PostgreSQL-tjänstens slutpunkt trafik Azure Database for MariaDB och Azure Database för MySQL-servrar i undernät.

### <a name="sample-script"></a>Exempelskript

Det här exempelskriptet används för att skapa en Azure Database for MariaDB-server, skapa ett virtuellt nätverk, VNet-tjänstslutpunkt och skydda server till undernät med en VNet-regel. I det här exempelskriptet ändrar du administratörens användarnamn och lösenord. Ersätt prenumerations-ID som används i den `az account set --subscription` med ditt eget prenumerations-ID.

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>Rensa distribution
När exempelskriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->
