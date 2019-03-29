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
ms.date: 03/27/2019
ms.openlocfilehash: 6181183b1455d5ca38ab9bbd37102cb3bc091b3c
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622101"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Hantera Transparent datakryptering i en hanterad instans med din egen nyckel från Azure Key Vault

Det här PowerShell-Skriptexemplet konfigurerar Transparent datakryptering (TDE) i scenario med Bring Your Own Key för Azure SQL Managed Instance, med hjälp av en nyckel från Azure Key Vault. Läs mer om transparent Datakryptering med stöd för ta med din egen nyckel (BYOK) i [TDE Bring Your Own Key till Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell 1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sample-scripts"></a>Exempelskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../sql-database-powershell-samples.md).
