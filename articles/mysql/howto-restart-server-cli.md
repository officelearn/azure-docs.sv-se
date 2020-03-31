---
title: Starta om servern - Azure CLI - Azure Database för MySQL
description: I den här artikeln beskrivs hur du kan starta om en Azure-databas för MySQL-server med Hjälp av Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c1fd688fbfd892e3d0dfc3ebb1712dd931e0ed39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063502"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Starta om Azure Database för MySQL-server med Azure CLI
I det här avsnittet beskrivs hur du kan starta om en Azure-databas för MySQL-server. Du kan behöva starta om servern av underhållsskäl, vilket medför ett kort avbrott när servern utför åtgärden.

Omstarten av servern blockeras om tjänsten är upptagen. Tjänsten kan till exempel bearbeta en tidigare begärd åtgärd, till exempel skalning av virtuella kärnor.

Hur mycket tid det krävs för att slutföra en omstart beror på MySQL-återställningsprocessen. Om du vill minska omstartstiden rekommenderar vi att du minimerar mängden aktivitet som inträffar på servern före omstarten.

## <a name="prerequisites"></a>Krav
För att slutföra den här guiden behöver du:
- En [Azure-databas för MySQL-server](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Den här programguiden kräver att du använder Azure CLI version 2.0 eller senare. Om du vill bekräfta versionen anger du `az --version`i kommandotolken i Azure CLI . Information om hur du installerar eller uppgraderar finns i [Installera Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Starta om servern

Starta om servern med följande kommando:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du anger parametrar i Azure Database för MySQL](howto-configure-server-parameters-using-cli.md)