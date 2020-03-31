---
title: Lagring med automatisk tillväxt – Azure CLI - Azure Database för MariaDB
description: I den här artikeln beskrivs hur du kan aktivera lagring för automatisk tillväxt med Hjälp av Azure CLI i Azure Database för MariaDB.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4be84c750f6a3ca7a0d48aa2b98d75272c1cbadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529092"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Automatiskt växa Azure Database för MariaDB-lagring med Hjälp av Azure CLI
I den här artikeln beskrivs hur du kan konfigurera en Azure-databas för MariaDB-serverlagring så att den växer utan att påverka arbetsbelastningen.

Servern [som når lagringsgränsen](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit)är skrivskyddad. Om automatisk lagring växer aktiveras sedan för servrar med mindre än 100 GB etablerad lagring, ökas den etablerade lagringsstorleken med 5 GB så snart det kostnadsfria lagringsutrymmet är lägre än 1 GB eller 10 % av den etablerade lagringen. För servrar med mer än 100 GB av etablerad lagring ökas den etablerade lagringsstorleken med 5 % när det lediga lagringsutrymmet är mindre än 5 % av den etablerade lagringsstorleken. Maximala lagringsgränser som anges [här](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) gäller.

## <a name="prerequisites"></a>Krav
För att slutföra den här guiden behöver du:
- En [Azure-databas för MariaDB-server](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Den här programguiden kräver att du använder Azure CLI version 2.0 eller senare. Om du vill bekräfta versionen anger du `az --version`i kommandotolken i Azure CLI . Information om hur du installerar eller uppgraderar finns i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="enable-mariadb-server-storage-auto-grow"></a>Aktivera MariaDB-serverlagring automatiskt växa

Aktivera lagring av server som växer automatiskt på en befintlig server med följande kommando:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Aktivera lagring för server som växer automatiskt när du skapar en ny server med följande kommando:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [skapar aviseringar om mått](howto-alert-metric.md).