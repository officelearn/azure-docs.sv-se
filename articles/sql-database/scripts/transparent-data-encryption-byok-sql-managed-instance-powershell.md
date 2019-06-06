---
title: 'PowerShell: Aktivera transparent Datakryptering BYOK – Azure SQL Database Managed Instance | Microsoft Docs'
description: Lär dig hur du konfigurerar en Azure SQL Managed-instans för att börja använda BYOK Transparent datakryptering (TDE) för kryptering i vila med hjälp av PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 04/19/2019
ms.openlocfilehash: c2c4bd7bffd923430d0817cb6ea975f4c1596623
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729171"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>Hantera Transparent datakryptering i en hanterad instans med din egen nyckel från Azure Key Vault (förhandsversion)

Det här PowerShell-Skriptexemplet konfigurerar Transparent datakryptering (TDE) i scenario med Bring Your Own Key (förhandsversion) för Azure SQL Managed Instance, med hjälp av en nyckel från Azure Key Vault. Läs mer om transparent Datakryptering med stöd för ta med din egen nyckel (BYOK) i [TDE Bring Your Own Key till Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

- En befintlig hanterad instans. Se [Använd PowerShell för att skapa en Azure SQL Database-hanterad instans](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Med hjälp av både PowerShell lokalt eller med hjälp av Azure Cloud Shell kräver AZ PowerShell 1.1.1-preview eller en senare förhandsversion. Om du behöver uppgradera kan du läsa [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps), eller köra den nedan exempelskriptet för att installera modulen.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-scripts"></a>Exempelskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).
