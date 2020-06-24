---
title: Hantera Läs repliker – Azure PowerShell-Azure Database for PostgreSQL
description: Lär dig hur du konfigurerar och hanterar Läs repliker i Azure Database for PostgreSQL med PowerShell.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 082a4e863760d4b327e00de2e6721e306ebb9d78
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84740094"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>Skapa och hantera Läs repliker i Azure Database for PostgreSQL med PowerShell

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i Azure Database for PostgreSQL-tjänsten med hjälp av PowerShell. Mer information om Läs repliker finns i [översikten](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Du kan skapa och hantera Läs repliker med PowerShell.

## <a name="prerequisites"></a>Krav

För att slutföra den här instruktions guiden behöver du:

- [AZ PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps) installeras lokalt eller [Azure Cloud Shell](https://shell.azure.com/) i webbläsaren
- En [Azure Database for postgresql-server](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Även om PowerShell-modulen AZ. PostgreSql är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> När AZ. PostgreSql PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

Om du väljer att använda PowerShell lokalt ansluter du till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Funktionen Läs replik är bara tillgänglig för Azure Database for PostgreSQL servrar i Generell användning eller Minnesoptimerade pris nivåer. Se till att huvud servern är i någon av dessa pris nivåer.

### <a name="create-a-read-replica"></a>Skapa en Läs replik

Du kan skapa en Läs replik server med följande kommando:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzPostgreSqlServerReplica`Kommandot kräver följande parametrar:

| Inställningen | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Resurs gruppen där replik servern skapas.  |
| Name | mydemoreplicaserver | Namnet på den nya replik servern som skapas. |

Om du vill skapa en skrivskyddad replik av en kors region använder du parametern **location** . I följande exempel skapas en replik i regionen **USA, västra** .

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Om du vill veta mer om vilka regioner du kan skapa en replik i går du till [artikeln Läs replik begrepp](concepts-read-replicas.md).

Som standard skapas Läs repliker med samma server konfiguration som i huvud gruppen om inte **SKU** -parametern anges.

> [!NOTE]
> Vi rekommenderar att replik serverns konfiguration måste vara lika med eller större än huvud värden, för att repliken ska kunna fortsätta med huvud servern.

### <a name="list-replicas-for-a-master-server"></a>Lista repliker för en huvud server

Om du vill visa alla repliker för en specifik huvud server kör du följande kommando:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMariaDReplica`Kommandot kräver följande parametrar:

| Inställningen | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Resurs gruppen där replik servern ska skapas.  |
| ServerName | mydemoserver | Namn eller ID för huvud servern. |

### <a name="delete-a-replica-server"></a>Ta bort en replik Server

Du kan ta bort en Läs replik Server genom att köra `Remove-AzPostgreSqlServer` cmdleten.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>Ta bort en huvud server

> [!IMPORTANT]
> Om du tar bort en huvudserver stoppas replikeringen till alla replikservrar och själva huvudservern tas bort. Replikservrar blir fristående servrar som nu stöder både läsningar och skrivningar.

Om du vill ta bort en huvud server kan du köra `Remove-AzPostgreSqlServer` cmdleten.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Starta om Azure Database for PostgreSQL server med PowerShell](howto-restart-server-powershell.md)
