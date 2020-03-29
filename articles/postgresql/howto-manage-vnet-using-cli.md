---
title: Använd regler för virtuella nätverk - Azure CLI - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du skapar och hanterar slutpunkter och regler för VNet-tjänst för Azure Database för PostgreSQL med hjälp av Azure CLI-kommandoraden.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 5e80b48e43e72bc3eda16a5dcc64f980065c923f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974113"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Skapa och hantera slutpunkter för VNet-tjänst för Azure Database för PostgreSQL - Single Server med Azure CLI
Slutpunkter och regler för virtuella nätverkstjänster (Virtual Network) utökar det privata adressutrymmet för ett virtuellt nätverk till din Azure-databas för PostgreSQL-server. Med hjälp av bekväma CLI-kommandon (Azure Command Line Interface) kan du skapa, uppdatera, ta bort, lista och visa slutpunkter och regler för VNet-tjänst för att hantera servern. En översikt över Slutpunkter för Azure Database for PostgreSQL VNet-tjänst, inklusive begränsningar, finns i [Azure Database for PostgreSQL Server VNet-tjänstslutpunkter](concepts-data-access-and-security-vnet.md). Slutpunkter för VNet-tjänst är tillgängliga i alla regioner som stöds för Azure Database för PostgreSQL.

## <a name="prerequisites"></a>Krav
För att gå igenom den här guiden behöver du:
- Installera [Azure CLI](/cli/azure/install-azure-cli) eller använd Azure Cloud Shell i webbläsaren.
- En [Azure-databas för PostgreSQL-server och databas](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> Stöd för slutpunkter för VNet-tjänst är endast för servrar med allmänt syfte och minnesoptimerade.
> Om trafiken flödar via en gemensam VNet-gateway med tjänstslutpunkter och ska flöda till peer-enheten, skapar du en ACL/VNet-regel som tillåter virtuella Azure-datorer i gateway-nätverket att komma åt Azure-databasen för PostgreSQL-servern.


## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Konfigurera slutpunkter för Vnet-tjänst för Azure Database för PostgreSQL
De [az-nätverksvnet-kommandona](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) används för att konfigurera virtuella nätverk.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). 

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
> Vi rekommenderar starkt att du läser den här artikeln om tjänstslutpunktskonfigurationer och överväganden innan du kör exempelskriptet nedan eller konfigurerar tjänstslutpunkter. **Slutpunkt för tjänsten Virtuellt nätverk:** Slutpunkten [för tjänsten Virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät vars egenskapsvärden innehåller ett eller flera formella Azure-tjänsttypsnamn. Slutpunkter för VNet-tjänster använder tjänsttypsnamnet **Microsoft.Sql**, som refererar till Azure-tjänsten SOM heter SQL Database. Det här tjänstmärket gäller även för Azure SQL Database, Azure Database for PostgreSQL- och MySQL-tjänster. Det är viktigt att notera när du använder **Microsoft.Sql-tjänsttaggen** på en VNet-tjänstslutpunkt som konfigurerar tjänstslutpunktstrafik för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure Database för PostgreSQL och Azure Database för MySQL-servrar i undernätet. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Exempelskript för att skapa en Azure-databas för PostgreSQL-databas, skapa en VNet, VNet-tjänstslutpunkt och skydda servern till undernätet med en VNet-regel
I det här exempelskriptet ändrar du markerade rader om du vill anpassa administratörens användarnamn och lösenord. Ersätt prenumerations-ID `az account set --subscription` som används i kommandot med din egen prenumerationsidentifierare.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md