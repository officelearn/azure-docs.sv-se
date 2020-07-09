---
title: Konfigurera Server parametrar – Azure PowerShell-Azure Database for MariaDB
description: I den här artikeln beskrivs hur du konfigurerar tjänst parametrarna i Azure Database for MariaDB med PowerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 5/26/2020
ms.openlocfilehash: 4ee0e5bd0d9d1fa941af245ecc6182ba1608eef0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86104970"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-powershell"></a>Konfigurera Server parametrar i Azure Database for MariaDB med PowerShell

Du kan visa, Visa och uppdatera konfigurations parametrar för en Azure Database for MariaDB-server med hjälp av PowerShell. En del av motor konfigurationerna exponeras på server nivå och kan ändras.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

- [AZ PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps) installeras lokalt eller [Azure Cloud Shell](https://shell.azure.com/) i webbläsaren
- En [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Även om PowerShell-modulen AZ. MariaDb är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> När AZ. MariaDb PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

Om du väljer att använda PowerShell lokalt ansluter du till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Visa lista med Server konfigurations parametrar för Azure Database for MariaDB Server

Om du vill visa alla ändrings bara parametrar på en server och deras värden kör du `Get-AzMariaDbConfiguration` cmdleten.

I följande exempel visas serverns konfigurations parametrar för servern **mydemoserver** i resurs gruppen **myresourcegroup**.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

För definitionen av var och en av de angivna parametrarna, se avsnittet referens för MariaDB på [Server systemets variabler](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Visa information om Server konfigurations parameter

Om du vill visa information om en viss konfigurations parameter för en server kör du `Get-AzMariaDbConfiguration` cmdleten och anger parametern **namn** .

Det här exemplet visar information om konfigurations parametern för **långsam \_ fråge \_ logg** Server för Server- **mydemoserver** under resurs grupp **myresourcegroup**.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Ändra ett parameter värde för Server konfiguration

Du kan också ändra värdet för en viss server konfigurations parameter, som uppdaterar det underliggande konfiguration svärdet för MariaDB-Server motorn. Använd cmdleten för att uppdatera konfigurationen `Update-AzMariaDbConfiguration` .

Så här uppdaterar du den **långsamma \_ fråge \_ loggs** serverns konfigurations parameter för Server **mydemoserver** under resurs grupp **myresourcegroup**.

```azurepowershell-interactive
Update-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utöka lagringen automatiskt i Azure Database for MariaDB server med PowerShell](howto-auto-grow-storage-powershell.md).
