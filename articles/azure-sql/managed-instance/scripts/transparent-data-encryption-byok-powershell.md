---
title: 'PowerShell: Aktivera TDE (BYOK)'
titleSuffix: Azure SQL Managed Instance
description: Lär dig hur du konfigurerar en Azure SQL-hanterad instans att börja använda BYOK () transparent datakryptering (TDE) för kryptering i vila med hjälp av PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: cff98c0acfe06a9dbf9e3d7c7dae1b2411823d28
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051796"
---
# <a name="transparent-data-encryption-tde-in-a-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transparent datakryptering (TDE) i en SQL-hanterad instans med hjälp av din egen nyckel från Azure Key Vault
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Detta exempel på PowerShell-skript konfigurerar transparent datakryptering (TDE) med kundhanterad nyckel för Azure SQL-hanterad instans med hjälp av en nyckel från Azure Key Vault. Detta kallas ofta för ett Bring Your Own Key scenario för TDE. Mer information om TDE med kundhanterad nyckel finns i [TDE Bring Your Own Key till Azure SQL](../../database/transparent-data-encryption-byok-overview.md).

## <a name="prerequisites"></a>Förutsättningar

- En befintlig SQL-hanterad instans. Se [använda PowerShell för att skapa en hanterad Azure SQL-instans](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Att använda både PowerShell lokalt eller med Azure Cloud Shell kräver AZ PowerShell 2.3.2 eller en senare version. Om du behöver uppgradera kan du läsa [installera Azure PowerShell modul](/powershell/azure/install-az-ps)eller köra exempel skriptet nedan för att installera modulen för den aktuella användaren:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-scripts"></a>Exempelskript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skript exempel för SQL-hanterad instans finns i [PowerShell-skripten för Azure SQL-hanterad instans](../../database/powershell-script-content-guide.md).
