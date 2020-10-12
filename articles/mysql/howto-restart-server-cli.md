---
title: Starta om Server – Azure CLI – Azure Database for MySQL
description: I den här artikeln beskrivs hur du startar om en Azure Database for MySQL-server med hjälp av Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9707aaa911fc8ceb8c03b369bd6479646ca3418c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500733"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Starta om Azure Database for MySQL server med Azure CLI
I det här avsnittet beskrivs hur du kan starta om en Azure Database for MySQL-server. Du kan behöva starta om servern för underhålls orsaker, vilket orsakar ett kort avbrott eftersom servern utför åtgärden.

Servern kommer att startas om när tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalnings virtuella kärnor.

Tiden som krävs för att slutföra en omstart beror på MySQL-återställnings processen. För att minska omstarts tiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern innan du startar om.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for MySQL-server](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Den här instruktions guiden kräver att du använder Azure CLI version 2,0 eller senare. Bekräfta versionen genom att ange i kommando tolken för Azure CLI `az --version` . Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Starta om servern

Starta om servern med följande kommando:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du ställer in parametrar i Azure Database for MySQL](howto-configure-server-parameters-using-cli.md)
