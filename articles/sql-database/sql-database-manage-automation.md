---
title: Hantera databaser med Azure Automation
description: Lär dig mer om hur Azure Automation-tjänsten kan användas för att hantera Azure SQL-databaser i stor skala.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 9d826a75f05cf2031565f89e21d7f3667ecc8f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822814"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Hantera Azure SQL-databaser med Azure Automation

Den här guiden introducerar dig till Azure Automation-tjänsten och hur den kan användas för att förenkla hanteringen av dina Azure SQL-databaser.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?

[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst för att förenkla molnhantering genom processautomatisering. Med Hjälp av Azure Automation kan tidskrävande, manuella, felbenägna och ofta upprepade uppgifter automatiseras för att öka tillförlitligheten, effektiviteten och tiden till värde för din organisation.

Azure Automation tillhandahåller en arbetsflödeskörningsmotor med hög tillförlitlighet och hög tillgänglighet, och som skalas för att uppfylla dina behov när din organisation växer. I Azure Automation kan processer startas manuellt, av tredjepartssystem eller med schemalagda intervall så att aktiviteter sker exakt när det behövs.

Lägre driftkostnader och frigör IT/DevOps-personal för att fokusera på arbete som tillför affärsvärde genom att flytta dina molnhanteringsuppgifter som ska köras automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Hur kan Azure Automation hjälpa till att hantera Azure SQL-databaser?

Azure SQL Database kan hanteras i Azure Automation med hjälp av [Azure SQL Database PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) som är tillgängliga i [Azure PowerShell-verktygen](/powershell/azure/overview). Azure Automation har dessa Azure SQL Database PowerShell-cmdlets tillgängliga direkt, så att du kan utföra alla dina SQL DB-hanteringsuppgifter i tjänsten. Du kan också para ihop dessa cmdlets i Azure Automation med cmdlets för andra Azure-tjänster, för att automatisera komplexa uppgifter över Azure-tjänster och över tredjepartssystem.

Azure Automation har också möjlighet att kommunicera med SQL-servrar direkt genom att utfärda SQL-kommandon med PowerShell.

[Azure Automation-runbook-galleriet](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) innehåller en mängd olika produktteam och community-runbooks för att komma igång med att automatisera hanteringen av Azure SQL-databaser, andra Azure-tjänster och tredjepartssystem. Galleri runbooks inkluderar:

- [Köra SQL-frågor mot en SQL Server-databas](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Lodrätt skala (upp eller ned) en Azure SQL-databas enligt ett schema](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Trunkera en SQL-tabell om databasen närmar sig dess maximala storlek](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Indexera tabeller i en Azure SQL-databas om de är mycket fragmenterade](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna i Azure Automation och hur det kan användas för att hantera Azure SQL-databaser följer du dessa länkar för att lära dig mer om Azure Automation.

- [Översikt över Azure Automation](../automation/automation-intro.md)
- [Min första Runbook](../automation/automation-first-runbook-graphical.md)
- [Azure Automation: Din SQL-agent i molnet](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 