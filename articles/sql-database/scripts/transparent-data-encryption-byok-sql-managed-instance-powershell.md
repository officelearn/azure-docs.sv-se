---
title: 'PowerShell: Aktivera BYOK TDE - Azure SQL Database Managed Instance '
description: Lär dig hur du konfigurerar en Azure SQL-hanterad instans för att börja använda BYOK Transparent Data Encryption (TDE) för kryptering i vila med PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73691405"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Hantera transparent datakryptering i en hanterad instans med din egen nyckel från Azure Key Vault

Det här PowerShell-skriptexempelet konfigurerar Transparent Data Encryption (TDE) med kundhanterad nyckel för Azure SQL Managed Instance med hjälp av en nyckel från Azure Key Vault. Detta kallas ofta för ett Ta med din egen nyckel-scenario för TDE. Mer information om TDE med kundhanterad nyckel finns i [TDE Ta med din egen nyckel till Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Krav

- En befintlig hanterad instans. Se [Använda PowerShell för att skapa en hanterad Azure SQL-databas-hanterad instans](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du använder både PowerShell lokalt eller med Azure Cloud Shell krävs AZ PowerShell 2.3.2 eller en senare version. Om du behöver uppgradera läser du [Installera Azure PowerShell-modul](/powershell/azure/install-az-ps)eller kör exempelskriptet nedan för att installera modulen för den aktuella användaren:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-scripts"></a>Exempelskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).
