---
title: Hantera Azure SQL-databaser med hjälp av Azure Automation
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
ms.openlocfilehash: 0e29cd85c63bb2f6e083f9cda9abd48fa6d9975a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689446"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Hantera Azure SQL-databaser med hjälp av Azure Automation

I den här guiden får du lära dig Azure Automation tjänsten och hur den kan användas för att förenkla hanteringen av dina Azure SQL-databaser.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation?

[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst för att förenkla moln hantering genom process automatisering. Att använda Azure Automation, långvariga, manuella, fel känsliga och ofta återkommande uppgifter kan automatiseras för att öka tillförlitligheten, effektiviteten och tiden för din organisation.

Azure Automation tillhandahåller en motor för körning av arbets flöden med hög tillförlitlighet och hög tillgänglighet och som kan skalas för att uppfylla dina behov när organisationen växer. I Azure Automation kan processer startas manuellt, av system från tredje part eller med schemalagda intervall så att aktiviteterna sker exakt vid behov.

Sänk drifts kostnader och frigör IT/DevOps-personalen och fokusera på arbete som lägger till affärs värde genom att flytta dina moln hanterings uppgifter så att de körs automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Hur kan Azure Automation hjälpa till att hantera Azure SQL-databaser?

Azure SQL Database kan hanteras i Azure Automation med hjälp av de [Azure SQL Database PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) som är tillgängliga i [Azure PowerShell-verktygen](/powershell/azure/overview). Azure Automation har dessa Azure SQL Database PowerShell-cmdlets tillgängliga i rutan så att du kan utföra alla dina hanterings uppgifter för SQL DB i tjänsten. Du kan också para ihop dessa cmdlets i Azure Automation med cmdletar för andra Azure-tjänster, för att automatisera komplexa uppgifter i Azure-tjänster och på olika system.

Azure Automation kan också kommunicera med SQL-servrar direkt genom att utfärda SQL-kommandon med PowerShell.

[Azure Automation Runbook-galleriet](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) innehåller en rad produkt team och community-Runbooks för att komma igång med automatisering av hantering av Azure SQL-databaser, andra Azure-tjänster och tredje parts system. Galleri Runbooks innehåller:

- [Köra SQL-frågor mot en SQL Server-databas](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Lodrätt skala (upp eller ned) ett Azure SQL Database enligt ett schema](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Trunkera en SQL-tabell om dess databas närmar sig maximal storlek](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Indexera tabeller i en Azure SQL Database om de är mycket fragmenterade](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna om Azure Automation och hur det kan användas för att hantera Azure SQL-databaser, kan du följa dessa länkar om du vill veta mer om Azure Automation.

- [Översikt över Azure Automation](../automation/automation-intro.md)
- [Min första Runbook](../automation/automation-first-runbook-graphical.md)
- [Azure Automation: din SQL-Agent i molnet](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 