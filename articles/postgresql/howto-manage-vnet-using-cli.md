---
title: Skapa och hantera Azure-databas för PostgreSQL VNet slutpunkter och regler som använder Azure CLI | Microsoft Docs
description: Den här artikeln beskriver hur du skapar och hanterar Azure-databas för PostgreSQL VNet slutpunkter och regler som använder Azure CLI-kommandoraden.
services: postgresql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 7312000d1f22af3eb0091b46caac2c9607231513
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736634"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-using-azure-cli"></a>Skapa och hantera Azure-databas för PostgreSQL VNet slutpunkter med hjälp av Azure CLI
Virtuella nätverk (VNet) services-slutpunkter och regler kan du utöka det privata adressutrymmet i ett virtuellt nätverk till din Azure-databas för PostgreSQL-servern. Med lämplig Azure-kommandoradsgränssnittet (CLI)-kommandon du skapa, uppdatera, ta bort, lista och visa VNet slutpunkter och regler för att hantera servern. En översikt över Azure-databas för PostgreSQL VNet slutpunkter, inklusive begränsningar, finns i [Azure-databas för PostgreSQL Server VNet Tjänsteslutpunkter](concepts-data-access-and-security-vnet.md). Slutpunkter för virtuella nätverk är tillgänglig som förhandsversion i alla regioner som stöds för Azure-databas för PostgreSQL.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att gå igenom den här instruktioner:
- Installera [Azure CLI 2.0](/cli/azure/install-azure-cli) -kommandoradsverktyget eller Använd Azure Cloud-gränssnittet i webbläsaren.
- En [Azure-databas för PostgreSQL-servern och databasen](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> Stöd för VNet slutpunkter stöds endast för generella och Minnesoptimerade servrar.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Konfigurera slutpunkter för virtuella nätverk för Azure-databas för PostgreSQL
Den [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) kommandon som används för att konfigurera virtuella nätverk.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Om du kör CLI lokalt, måste du logga in på ditt konto med hjälp av kommandot [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in). Observera egenskapen **ID** från kommandoutdata för det motsvarande prenumerationsnamnet.
```azurecli-interactive
az login
```
Installera CLI-tillägget för Azure-databas för PostgreSQL VNet slutpunkter med hjälp av den `az extension add --name rdbms-vnet` kommando. 
```azurecli-interactive
az extension add --name rdbms-vnet
```

Kör den `az extension list` kommando för att kontrollera installationen av CLI-tillägget.
```azurecli-interactive
az extension list
```
Utdata från kommandot visar alla installerade tillägg. Azure-databasen för PostgreSQL CLI tillägg är:

 {”extensionType”: ”whl”, ”name”: ”rdbms-vnet”, ”version”: ”10.0.0”}

> [!NOTE]
> Så här avinstallerar du tillägget CLI kör den `az extension remove -n rdbms-vnet` kommando. 

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account#az_account_set). Ersätt egenskapen **ID** från **az login**-utdata för din prenumeration i platshållaren för prenumerations-ID.

- Kontot måste ha behörighet för att skapa ett virtuellt nätverk och tjänstslutpunkten.

Slutpunkter kan konfigureras i virtuella nätverk, oberoende av en användare med skrivbehörighet till det virtuella nätverket.

För att kunna skydda Azure-tjänstresurser i ett virtuellt nätverk måste behörigheten "Microsoft.Network/JoinServicetoaSubnet" för undernäten läggas till för användaren. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.

Lär dig mer om [inbyggda roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) och att tilldela specifika behörigheter till [anpassade roller](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Virtuella nätverk och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om virtuella nätverk och Azure serviceresurser finns i olika prenumerationer måste vara resurser under samma Active Directory (AD)-klient vid tidpunkten för den här förhandsgranskningen.

> [!IMPORTANT]
> Vi rekommenderar starkt att den här artikeln om tjänsten endpoint konfigurationer och överväganden innan du kör skriptet exemplet nedan, eller konfigurera slutpunkter. **Virtuella nätverk tjänstslutpunkten:** A [virtuellt nätverk tjänstslutpunkten](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät vars egenskapsvärden innehåller en eller flera formella Azure-tjänst-typnamn. VNet services-slutpunkter använda Tjänsttypnamnet **Microsoft.Sql**, som refererar till Azure-tjänsten med namnet SQL-databas. Den här tjänsten taggen gäller även för Azure SQL Database, Azure-databas för PostgreSQL och MySQL-tjänster. Det är viktigt att känna till när du använder den **Microsoft.Sql** tjänsten taggen till en tjänstslutpunkt för virtuella nätverk konfigureras för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure-databas för PostgreSQL-tjänstens slutpunkt trafik och Azure-databas för MySQL-servrar i undernätet. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Exempel på skript för att skapa en Azure-databas för PostgreSQL-databas, skapa ett VNet VNet tjänstslutpunkten och skydda servern till undernät med en VNet-regel
I det här exempelskriptet ändrar du markerade rader om du vill anpassa administratörens användarnamn och lösenord. Ersätt prenumerations-ID som används i den `az account set --subscription` kommandot med dina egna prenumerations-ID.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När exempelskriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]
