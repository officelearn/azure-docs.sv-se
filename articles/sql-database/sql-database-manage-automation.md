---
title: Hantera Azure SQL-databaser med Azure Automation | Microsoft Docs
description: Läs mer om hur Azure Automation-tjänsten kan användas för att hantera Azure SQL-databaser i stor skala.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: e488e742fc49102f7c58d132a66bca2347ad575c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702103"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Hantera Azure SQL-databaser med Azure Automation

Den här guiden innehåller en introduktion till Azure Automation-tjänsten och hur den kan användas för att förenkla hanteringen av din Azure SQL-databaser.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?

[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst för att förenkla molnhantering med Processautomatisering. Med Azure Automation, kan tidskrävande, manuell, felbenägna och regelbundet återkommande uppgifter automatiseras för att öka tillförlitligheten, effektivitet och tid till värde för din organisation.

Azure Automation tillhandahåller en arbetsflödeskörningsmotor med hög tillgänglighet och hög tillgänglighet och som kan skalas efter dina behov när organisationen växer. I Azure Automation kan processer vara startats manuellt, genom att program från tredje part eller med schemalagda intervall så att uppgifter inträffa exakt när det behövs.

Minska driftsomkostnader och frigör IT / DevOps-personal att fokusera på arbete som lägger till företag värde genom att flytta din molnhanteringsuppgifter att köras automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Hur kan Azure Automation till att hantera Azure SQL-databaser?

Azure SQL Database kan hanteras i Azure Automation med hjälp av den [Azure SQL Database PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) som är tillgängliga i den [Azure PowerShell-verktyg](/powershell/azure/overview). Azure Automation har dessa Azure SQL Database PowerShell-cmdletar som är tillgängliga direkt, så att du kan utföra alla dina SQL DB-hanteringsuppgifter i tjänsten. Du kan också koppla dessa cmdletar i Azure Automation med cmdlets för andra Azure-tjänster, att automatisera avancerade uppgifter olika Azure-tjänster och i tredjepartssystem.

Azure Automation har också möjlighet att kommunicera med SQL-servrar direkt, genom att utfärda SQL-kommandon med hjälp av PowerShell.

Den [Azure Automation runbook-galleriet](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) innehåller en rad olika produkt-teamet och community runbooks för att komma igång med automatiserade hantering av Azure SQL-databaser, andra Azure-tjänster och program från tredje part. Runbooks från galleriet är:

- [Kör SQL-frågor mot en SQL Server-databas](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Lodrätt skalning (upp eller ned) en Azure SQL Database enligt ett schema](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Trunkera en SQL-tabell om databasen närmar sig maximal storlek](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Indexera tabeller i en Azure SQL-databas om de är mycket fragmenterat](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna i Azure Automation och hur den kan användas för att hantera Azure SQL-databaser kan du följa dessa länkar om du vill veta mer om Azure Automation.

- [Översikt över Azure Automation](../automation/automation-intro.md)
- [Min första Runbook](../automation/automation-first-runbook-graphical.md)
- [Azure Automation: Din SQL-Agent i molnet](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 