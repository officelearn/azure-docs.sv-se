---
title: Lagring med automatisk tillväxt – Azure CLI - Azure Database för PostgreSQL – Enkel server
description: I den här artikeln beskrivs hur du kan konfigurera lagring automatiskt med hjälp av Azure CLI i Azure Database för PostgreSQL - Single Server.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 8/7/2019
ms.openlocfilehash: b0dc2fbb168d9325439ee18a227f71a3b88ef9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74767967"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Automatiskt växa Azure Database för PostgreSQL-lagring - Single Server med Azure CLI
I den här artikeln beskrivs hur du kan konfigurera en Azure-databas för PostgreSQL-serverlagring så att den växer utan att påverka arbetsbelastningen.

Servern [som når lagringsgränsen](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit)är skrivskyddad. Om automatisk lagring växer aktiveras sedan för servrar med mindre än 100 GB etablerad lagring, ökas den etablerade lagringsstorleken med 5 GB så snart det kostnadsfria lagringsutrymmet är lägre än 1 GB eller 10 % av den etablerade lagringen. För servrar med mer än 100 GB av etablerad lagring ökas den etablerade lagringsstorleken med 5 % när det lediga lagringsutrymmet är mindre än 5 % av den etablerade lagringsstorleken. Maximala lagringsgränser som anges [här](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) gäller.

## <a name="prerequisites"></a>Krav
För att slutföra den här guiden behöver du:
- En [Azure-databas för PostgreSQL-server](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Den här programguiden kräver att du använder Azure CLI version 2.0 eller senare. Om du vill bekräfta versionen anger du `az --version`i kommandotolken i Azure CLI . Information om hur du installerar eller uppgraderar finns i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="enable-postgresql-server-storage-auto-grow"></a>Aktivera Automatisk lagring av PostgreSQL-serverlagring

Aktivera lagring av server som växer automatiskt på en befintlig server med följande kommando:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Aktivera lagring för server som växer automatiskt när du skapar en ny server med följande kommando:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [skapar aviseringar om mått](howto-alert-on-metric.md).