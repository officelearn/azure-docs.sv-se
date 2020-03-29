---
title: Starta om server - Azure CLI - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du kan starta om en Azure-databas för PostgreSQL - Single Server med Hjälp av Azure CLI
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 13b26b545f9e95ce2457e4f8d9cf32da59cd91e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770160"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Starta om Azure Database för PostgreSQL – Single Server med Azure CLI
I det här avsnittet beskrivs hur du kan starta om en Azure-databas för PostgreSQL-server. Du kan behöva starta om servern av underhållsskäl, vilket medför ett kort avbrott när servern utför åtgärden.

Omstarten av servern blockeras om tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalning av virtuella kärnor.
 
Hur länge som krävs för att slutföra en omstart beror på PostgreSQL-återställningsprocessen. Om du vill minska omstartstiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern före omstarten.

## <a name="prerequisites"></a>Krav
För att slutföra den här guiden behöver du:
- En [Azure-databas för PostgreSQL-server](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Den här programguiden kräver att du använder Azure CLI version 2.0 eller senare. Om du vill bekräfta versionen anger du `az --version`i kommandotolken i Azure CLI . Information om hur du installerar eller uppgraderar finns i [Installera Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Starta om servern

Starta om servern med följande kommando:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du [anger parametrar i Azure Database för PostgreSQL](howto-configure-server-parameters-using-cli.md)