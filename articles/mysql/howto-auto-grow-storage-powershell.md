---
title: Utöka lagringen automatiskt – Azure PowerShell – Azure Database for MySQL
description: I den här artikeln beskrivs hur du kan aktivera automatisk storleks ökning med PowerShell i Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/28/2020
ms.openlocfilehash: c8a19fe338af14f97e0eb191d7b57e840c71e400
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612732"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-server-using-powershell"></a>Utöka lagringen automatiskt i Azure Database for MySQL server med PowerShell

I den här artikeln beskrivs hur du kan konfigurera en Azure Database for MySQL Server lagring så att den växer utan att arbets belastningen påverkas.

Med automatisk storleks ökning förhindrar du att servern [når lagrings gränsen](/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit) och blir skrivskyddad. För servrar med 100 GB eller mindre allokerat lagrings utrymme ökas storleken med 5 GB när det lediga utrymmet är under 10%. För servrar som har mer än 100 GB allokerat lagrings utrymme ökas storleken med 5% när det lediga utrymmet är lägre än 10 GB. De maximala lagrings gränserna gäller enligt vad som anges i avsnittet lagring på [Azure Database for MySQL pris nivåer](/azure/mysql/concepts-pricing-tiers#storage).

> [!IMPORTANT]
> Kom ihåg att lagringen bara kan skalas upp, inte nedåt.

## <a name="prerequisites"></a>Krav

För att slutföra den här instruktions guiden behöver du:

- [AZ PowerShell-modulen](/powershell/azure/install-az-ps) installeras lokalt eller [Azure Cloud Shell](https://shell.azure.com/) i webbläsaren
- En [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Även om modulen AZ. MySql PowerShell är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande `Install-Module -Name Az.MySql -AllowPrerelease`kommando:.
> När AZ. MySql PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-moduler och är tillgängliga internt från Azure Cloud Shell.

Om du väljer att använda PowerShell lokalt ansluter du till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

## <a name="enable-mysql-server-storage-auto-grow"></a>Aktivera automatisk storleks ökning för MySQL server Storage

Aktivera automatisk storleks lagring på servern på en befintlig server med följande kommando:

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Aktivera server utöka lagring automatiskt när du skapar en ny server med följande kommando:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och hantera Läs repliker i Azure Database for MySQL med PowerShell](howto-read-replicas-powershell.md).
