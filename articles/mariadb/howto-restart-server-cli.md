---
title: Starta om Azure Database for MariaDB-server med Azure CLI
description: Den här artikeln beskrivs hur du kan starta om en Azure Database for MariaDB-server med Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/28/2019
ms.openlocfilehash: a6e0509d941d9bfdfe6db7a8b93ee49c5bece1a6
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58623085"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Starta om Azure Database for MariaDB-server med Azure CLI
Det här avsnittet beskrivs hur du kan starta om en Azure Database for MariaDB-server. Du kan behöva starta om servern för underhåll orsaker, vilket medför ett kort avbrott som servern utför åtgärden.

Starta om servern kommer att blockeras om tjänsten är upptagen. Tjänsten kan till exempel behandlar tidigare åtgärden, till exempel skala virtuella kärnor.

Den tid som krävs för att slutföra en omstart är beroende av MariaDB återställningsprocessen. Om du vill minska tiden för omstart, rekommenderar vi du minska mängden aktiviteten på server före omstarten.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att slutföra den här guiden:
- En [Azure Database for MariaDB-server](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Den här guiden kräver att du använder Azure CLI version 2.0 eller senare. För att bekräfta versionen på Azure CLI-kommandotolk och ange `az --version`. Om du vill installera eller uppgradera, se [installera Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Starta om servern

Starta om servern med följande kommando:

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du ställer in parametrarna i Azure Database for MariaDB](howto-configure-server-parameters-cli.md)