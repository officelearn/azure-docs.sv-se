---
title: 'PowerShell: Aktivera BYOK TDE-Azure SQL Database Hanterad instans | Microsoft Docs'
description: Lär dig hur du konfigurerar en Azure SQL-hanterad instans att börja använda BYOK transparent datakryptering (TDE) för kryptering vid vila med hjälp av PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 04/19/2019
ms.openlocfilehash: d8f0f4a8e603a9040d166b00682077cff23abd8d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569701"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>Hantera transparent datakryptering i en hanterad instans med hjälp av din egen nyckel från Azure Key Vault (förhands granskning)

Detta exempel på PowerShell-skript konfigurerar transparent datakryptering (TDE) i Bring Your Own Key (för hands version) för Azure SQL-hanterad instans med hjälp av en nyckel från Azure Key Vault. Mer information om stöd för TDE med Bring Your Own Key (BYOK) finns i [TDE Bring Your Own Key till Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Förutsättningar

- En befintlig hanterad instans. Se [använda PowerShell för att skapa en Azure SQL Database Hanterad instans](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Att använda både PowerShell lokalt eller med Azure Cloud Shell kräver AZ PowerShell 1.1.1 – för hands versionen eller en senare för hands version. Om du behöver uppgradera kan du läsa [installera Azure PowerShell modul](/powershell/azure/install-az-ps)eller köra exempel skriptet nedan för att installera modulen.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-scripts"></a>Exempelskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).
