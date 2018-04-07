---
title: Hantera Azure SQL-databaser med hjälp av Azure Automation | Microsoft Docs
description: Läs mer om hur Azure Automation-tjänsten kan användas för att hantera Azure SQL-databaser i större skala.
services: sql-database, automation
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: f0f071c2ad5e79168d89361c89a005c247599655
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Hantera Azure SQL-databaser med hjälp av Azure Automation
Den här guiden innehåller en introduktion till Azure Automation-tjänsten och hur det kan användas för att förenkla hanteringen av dina Azure SQL-databaser.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst som förenklar molnhantering via automatisering. Med Azure Automation kan tidskrävande, manuell, felbenägna och ofta återkommande uppgifter automatiseras för att öka tillförlitligheten, effektivitet och tid för värde för din organisation.

Azure Automation ger en Körningsmotor för arbetsflöden för hög tillförlitliga och hög tillgänglighet som kan skalas för att uppfylla dina behov när organisationen växer. I Azure Automation kan processer vara inletts manuellt av 3 part eller med schemalagda intervall så att aktiviteter inträffa exakt vid behov.

Lägre operativ tillsyn och frigöra IT / DevOps-personal att fokusera på arbete som lägger till företag värde genom att flytta dina uppgifter för hantering av molnet att köras automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Hur Azure Automation kan hantera Azure SQL-databaser?
Azure SQL Database kan hanteras i Azure Automation med hjälp av den [Azure SQL Database PowerShell-cmdlets](https://docs.microsoft.com/powershell/servicemanagement/azure.sqldatabase/v1.6.1/azure.sqldatabase/) som är tillgängliga i den [Azure PowerShell verktygen](/powershell/azure/overview). Azure Automation har dessa Azure SQL Database PowerShell-cmdlets som är tillgängliga efter installationen, så att du kan utföra alla aktiviteter i tjänsten SQL DB management. Du kan också koppla dessa cmdlets i Azure Automation med cmdlets för andra Azure-tjänster, att automatisera avancerade uppgifter över Azure-tjänster och 3 part.

Azure Automation har också möjlighet att kommunicera med SQL-servrar direkt, genom att utfärda SQL-kommandon med hjälp av PowerShell.

Den [Azure Automation runbook-galleriet](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) innehåller en mängd produkten team och community runbooks för att komma igång med att automatisera hanteringen av Azure SQL-databaser, andra Azure-tjänster och 3 part. Galleriet runbooks är:

* [Kör SQL-frågor mot en SQL Server-databas](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
* [Lodrätt skala (upp eller ned) en Azure SQL Database enligt ett schema](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
* [Trunkera en SQLtabell om databasen närmar sig maximal storlek](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
* [Indexera tabeller i en Azure SQL Database om de är mycket fragmenterat](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Azure Automation och hur det kan användas för att hantera Azure SQL-databaser, följa dessa länkar för att lära dig mer om Azure Automation.

* [Azure Automation-översikt](../automation/automation-intro.md)
* [Min första Runbook](../automation/automation-first-runbook-graphical.md)
* [Azure Automation-inlärningsguide](https://azure.microsoft.com/documentation/learning-paths/automation/)
* [Azure Automation: Din SQL Agent i molnet](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 

