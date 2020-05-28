---
title: Konfigurera minimal TLS-version – hanterad instans
description: Lär dig hur du konfigurerar minimal TLS-version för hanterad instans
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: bf09994e8ebae09771ce110f9284aa3e99fc1816
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122481"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Konfigurera minimal TLS-version i Azure SQL-hanterad instans
Inställningen minimalt [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) gör det möjligt för kunder att kontrol lera vilken version av TLS som används av deras Azure SQL-hanterade instanser.

För närvarande har vi stöd för TLS 1,0, 1,1 och 1,2. Genom att ange en minimal TLS-version kan du se till att senare TLS-versioner stöds. T. ex. kan du välja en TLS-version som är större än 1,1. innebär endast anslutningar med TLS 1,1 och 1,2 godkänns och TLS 1,0 avvisas. När du har testat för att bekräfta att programmen har stöd för det rekommenderar vi att du ställer in minimal TLS-version på 1,2 eftersom den innehåller korrigeringar för sårbarheter som finns i tidigare versioner och är den högsta versionen av TLS som stöds i Azure SQL Managed instance.

För kunder med program som förlitar sig på äldre versioner av TLS rekommenderar vi att du ställer in den lägsta TLS-versionen enligt kraven för dina program. För kunder som förlitar sig på att program ska ansluta via en okrypterad anslutning rekommenderar vi inte att du anger någon minimal TLS-version. 

Mer information finns i [TLS-överväganden för SQL Database anslutning](../database/connect-query-content-reference-guide.md#tls-considerations-for-sql-database-connectivity).

När du har angett den lägsta TLS-versionen kommer inloggnings försök från klienter som använder en TLS-version som är lägre än den lägsta TLS-versionen av servern att Miss lyckas med följande fel:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Ange minimal TLS-version via PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Följande skript kräver Azure PowerShell- [modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar hur `Get` och `Set` egenskapen **minimal TLS-version** på instans nivå:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Ange minimal TLS-version via Azure CLI

> [!IMPORTANT]
> Alla skript i det här avsnittet kräver [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI i ett bash-gränssnitt

Följande CLI-skript visar hur du ändrar inställningen för **minimal TLS-version** i ett bash-gränssnitt:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```
