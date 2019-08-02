---
title: Skapa och hantera Azure Database for MySQL VNet-tjänstens slut punkter och regler med hjälp av Azure CLI | Microsoft Docs
description: Den här artikeln beskriver hur du skapar och hanterar Azure Database for MySQL VNet-tjänstens slut punkter och regler med hjälp av kommando raden i Azure CLI.
author: bolzmj
ms.author: mbolz
manager: jhubbard
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: d08b99c0a668286d9e9b94f2229915be8ff106f0
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610403"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Skapa och hantera Azure Database for MySQL VNet-tjänstens slut punkter med Azure CLI
VNet-tjänstslutpunkter och regler utökar det privata adressutrymmet för ett virtuellt nätverk till din Azure Database for MySQL-server. Med hjälp av ett bekvämt kommando rads gränssnitt (CLI) för kommando tolken kan du skapa, uppdatera, ta bort, lista och Visa VNet-tjänstens slut punkter och regler för att hantera servern. En översikt över Azure Database for MySQL VNet-tjänstens slut punkter, inklusive begränsningar, finns i [Azure Database for MySQL serverns VNet-](concepts-data-access-and-security-vnet.md)slutpunkter. VNet-tjänstens slut punkter är tillgängliga i alla regioner som stöds för Azure Database for MySQL.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här instruktions guiden behöver du:
- Installera [Azure CLI](/cli/azure/install-azure-cli) eller Använd Azure Cloud Shell i webbläsaren.
- En [Azure Database för MySQL-servern och databasen](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!NOTE]
> Stöd för VNet-tjänstslutpunkter är endast för generell användning och Minnesoptimerad servrar.
> Om det gäller VNet-peering, om trafik flödar genom en gemensam VNet-Gateway med tjänst slut punkter och ska flöda till motparten, skapar du en ACL/VNet-regel för att tillåta Azure Virtual Machines i gatewayens VNet att komma åt Azure Database for MySQL-servern.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Konfigurera VNet-tjänstens slut punkter för Azure Database for MySQL
[AZ Network VNet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) -kommandon används för att konfigurera virtuella nätverk.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

Om du kör CLI lokalt, måste du logga in på ditt konto med hjälp av kommandot [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Observera egenskapen **ID** från kommandoutdata för det motsvarande prenumerationsnamnet.
```azurecli-interactive
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Ersätt egenskapen **ID** från **az login**-utdata för din prenumeration i platshållaren för prenumerations-ID.

- Kontot måste ha de behörigheter som krävs för att skapa ett virtuellt nätverk och en tjänst slut punkt.

Tjänst slut punkter kan konfigureras på virtuella nätverk oberoende av en användare med Skriv behörighet till det virtuella nätverket.

För att skydda Azure-tjänstens resurser till ett VNet måste användaren ha behörighet till "Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/" för de undernät som läggs till. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.

Lär dig mer om [inbyggda roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) och att tilldela specifika behörigheter till [anpassade roller](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Virtuella nätverk och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om VNet-och Azure-tjänstens resurser finns i olika prenumerationer bör resurserna vara under samma Active Directory-klient (AD). Se till att båda prenumerationerna har **Microsoft. SQL** -Resurshanterarens registrerad. Mer information hittar du i [Resource Manager-Registration][resource-manager-portal]

> [!IMPORTANT]
> Vi rekommenderar starkt att läsa den här artikeln om konfiguration och överväganden för tjänst slut punkter innan du kör exempel skriptet nedan eller konfigurerar tjänst slut punkter. **Virtual Network tjänst slut punkt:** En [Virtual Network tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät vars egenskaps värden innehåller ett eller flera formella namn för Azure-tjänst typ. I VNet Services-slutpunkter används tjänst typs namnet **Microsoft. SQL**, som refererar till Azure-tjänsten med namnet SQL Database. Den här tjänst tag gen gäller även för Azure SQL Database-, Azure Database for PostgreSQL-och MySQL-tjänster. Det är viktigt att du noterar när du använder service tag-taggen **Microsoft. SQL** för en VNet-tjänst slut punkt som konfigurerar tjänst slut punkts trafik för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure Database for PostgreSQL och Azure Database för MySQL-servrar i under nätet. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Exempel skript om du vill skapa en Azure Database for MySQL databas skapar du ett VNet, VNet-slutpunkt och skyddar servern till under nätet med en VNet-regel
I det här exempelskriptet ändrar du markerade rader om du vill anpassa administratörens användarnamn och lösenord. Ersätt SubscriptionID som används i `az account set --subscription` kommandot med ditt eget prenumerations-ID.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När exempelskriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/resource-manager-supported-services.md

