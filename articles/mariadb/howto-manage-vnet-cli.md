---
title: Hantera VNet-slutpunkter - Azure CLI - Azure Database för MariaDB
description: I den här artikeln beskrivs hur du skapar och hanterar Azure Database för MariaDB VNet-tjänstslutpunkter och regler med hjälp av Azure CLI-kommandoraden.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 46bfab6935d08ac28ced7f392892ade6f68a0492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530860"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Skapa och hantera Azure Database för MariaDB VNet-tjänstslutpunkter med Azure CLI

VNet-tjänstslutpunkter och regler utökar det privata adressutrymmet för ett virtuellt nätverk till din Azure Database for MariaDB-server. Med hjälp av bekväma CLI-kommandon (Azure Command Line Interface) kan du skapa, uppdatera, ta bort, lista och visa slutpunkter och regler för VNet-tjänst för att hantera servern. En översikt över slutpunkter för Azure Database för MariaDB VNet-tjänst, inklusive begränsningar, finns i [Azure Database for MariaDB Server VNet-tjänstslutpunkter](concepts-data-access-security-vnet.md). Slutpunkter för VNet-tjänst är tillgängliga i alla regioner som stöds för Azure Database för MariaDB.

## <a name="prerequisites"></a>Krav
För att gå igenom den här guiden behöver du:
- Installera [Azure CLI](/cli/azure/install-azure-cli) eller använd Azure Cloud Shell i webbläsaren.
- En [Azure-databas för MariaDB-server och databas](quickstart-create-mariadb-server-database-using-azure-cli.md).

> [!NOTE]
> Stöd för slutpunkter för VNet-tjänst är endast för servrar med allmänt syfte och minnesoptimerade.

## <a name="configure-vnet-service-endpoints"></a>Konfigurera slutpunkter för VNet-tjänsten
De [az-nätverksvnet-kommandona](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) används för att konfigurera virtuella nätverk.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

Om du kör CLI lokalt, måste du logga in på ditt konto med hjälp av kommandot [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Observera egenskapen **ID** från kommandoutdata för det motsvarande prenumerationsnamnet.
```azurecli-interactive
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Ersätt egenskapen **ID** från **az login**-utdata för din prenumeration i platshållaren för prenumerations-ID.

- Kontot måste ha nödvändiga behörigheter för att skapa ett virtuellt nätverk och tjänstslutpunkten.

Tjänstslutpunkter kan konfigureras i virtuella nätverk oberoende av en användare med skrivåtkomst till det virtuella nätverket.

För att skydda Azure-tjänstresurser till ett virtuellt nätverk måste användaren ha behörighet att "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" för de undernät som läggs till. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.

Lär dig mer om [inbyggda roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) och att tilldela specifika behörigheter till [anpassade roller](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

VNet och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om VNet- och Azure-tjänstresurserna finns i olika prenumerationer bör resurserna finnas under samma Active Directory (AD) -klientorganisation. Kontrollera att båda prenumerationerna har **Microsoft.Sql-resursprovidern** registrerad. Mer information finns i [resurs-manager-registrering][resource-manager-portal]

> [!IMPORTANT]
> Vi rekommenderar starkt att du läser den här artikeln om tjänstslutpunktskonfigurationer och överväganden innan du konfigurerar tjänstslutpunkter. **Slutpunkt för tjänsten Virtuellt nätverk:** Slutpunkten [för tjänsten Virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät vars egenskapsvärden innehåller ett eller flera formella Azure-tjänsttypsnamn. Slutpunkter för VNet-tjänster använder tjänsttypsnamnet **Microsoft.Sql**, som refererar till Azure-tjänsten SOM heter SQL Database. Det här tjänstmärket gäller även för Azure SQL Database, Azure Database for MariaDB, PostgreSQL och MySQL-tjänster. Det är viktigt att notera när du använder **Microsoft.Sql-tjänsttaggen** på en VNet-tjänstslutpunkt som konfigurerar tjänstslutpunktstrafik för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure Database for PostgreSQL, Azure Database för MariaDB och Azure Database for MySQL-servrar i undernätet.

### <a name="sample-script"></a>Exempelskript

Det här exempelskriptet används för att skapa en Azure-databas för MariaDB-server, skapa en VNet-, VNet-tjänstslutpunkt och skydda servern till undernätet med en VNet-regel. Ändra administratörens användarnamn och lösenord i det här exempelskriptet. Ersätt prenumerations-ID `az account set --subscription` som används i kommandot med din egen prenumerationsidentifierare.

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
När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md