---
title: Konfigurera Server parametrar – Azure PowerShell-Azure Database for MySQL
description: I den här artikeln beskrivs hur du konfigurerar tjänst parametrarna i Azure Database for MySQL med PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: conceptual
ms.date: 4/29/2020
ms.openlocfilehash: 6c91f1494e56e9176f8bbf79387144aea5942787
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84725204"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-powershell"></a>Konfigurera Server parametrar i Azure Database for MySQL med PowerShell

Du kan visa, Visa och uppdatera konfigurations parametrar för en Azure Database for MySQL-server med hjälp av PowerShell. En del av motor konfigurationerna exponeras på server nivå och kan ändras.

## <a name="prerequisites"></a>Krav

För att slutföra den här instruktions guiden behöver du:

- [AZ PowerShell-modulen](/powershell/azure/install-az-ps) installeras lokalt eller [Azure Cloud Shell](https://shell.azure.com/) i webbläsaren
- En [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Även om modulen AZ. MySql PowerShell är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.MySql -AllowPrerelease` .
> När AZ. MySql PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-moduler och är tillgängliga internt från Azure Cloud Shell.

Om du väljer att använda PowerShell lokalt ansluter du till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Visa lista med Server konfigurations parametrar för Azure Database for MySQL Server

Om du vill visa alla ändrings bara parametrar på en server och deras värden kör du `Get-AzMySqlConfiguration` cmdleten.

I följande exempel visas serverns konfigurations parametrar för servern **mydemoserver** i resurs gruppen **myresourcegroup**.

```azurepowershell-interactive
Get-AzMySqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

För definitionen av var och en av de angivna parametrarna, se referens avsnittet MySQL på [Server system-variabler](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Visa information om Server konfigurations parameter

Om du vill visa information om en viss konfigurations parameter för en server kör du `Get-AzMySqlConfiguration` cmdleten och anger parametern **namn** .

Det här exemplet visar information om konfigurations parametern för **långsam \_ fråge \_ logg** Server för Server- **mydemoserver** under resurs grupp **myresourcegroup**.

```azurepowershell-interactive
Get-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Ändra ett parameter värde för Server konfiguration

Du kan också ändra värdet för en viss server konfigurations parameter, som uppdaterar det underliggande konfiguration svärdet för MySQL Server-motorn. Använd cmdleten för att uppdatera konfigurationen `Update-AzMySqlConfiguration` .

Så här uppdaterar du den **långsamma \_ fråge \_ loggs** serverns konfigurations parameter för Server **mydemoserver** under resurs grupp **myresourcegroup**.

```azurepowershell-interactive
Update-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utöka lagringen automatiskt i Azure Database for MySQL server med PowerShell](howto-auto-grow-storage-powershell.md).
