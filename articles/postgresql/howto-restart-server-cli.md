---
title: Starta om Server – Azure CLI – Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du kan starta om en Azure Database for PostgreSQL-enskild server med Azure CLI
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: e6b3d6b4d08c699b9747bb5b0fe11ce471dc3fe9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106721"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Starta om Azure Database for PostgreSQL – en server med hjälp av Azure CLI
I det här avsnittet beskrivs hur du kan starta om en Azure Database for PostgreSQL-Server. Du kan behöva starta om servern för underhålls orsaker, vilket orsakar ett kort avbrott eftersom servern utför åtgärden.

Servern kommer att startas om när tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalnings virtuella kärnor.
 
Tiden som krävs för att slutföra en omstart beror på återställnings processen för PostgreSQL. För att minska omstarts tiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern innan du startar om.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for postgresql-server](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Den här instruktions guiden kräver att du använder Azure CLI version 2,0 eller senare. Bekräfta versionen genom att ange i kommando tolken för Azure CLI `az --version` . Information om hur du installerar eller uppgraderar finns i [Installera Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Starta om servern

Starta om servern med följande kommando:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du ställer in parametrar i Azure Database for PostgreSQL](howto-configure-server-parameters-using-cli.md)