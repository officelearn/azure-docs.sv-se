---
title: Hantera databaser med Azure Automation
description: Lär dig mer om hur Azure Automation-tjänsten kan användas för att hantera Azure SQL Database i stor skala.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 4b5378b5df36c158c3f401f214730b4f493f5def
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84043613"
---
# <a name="managing-azure-sql-database-using-azure-automation"></a>Hantera Azure SQL Database med Azure Automation
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb.md)]

I den här guiden får du lära dig Azure Automation tjänsten och hur den kan användas för att förenkla hanteringen av databaser i Azure SQL Database.

## <a name="what-is-azure-automation"></a>Vad är Azure Automation

[Azure Automation](https://azure.microsoft.com/services/automation/) är en Azure-tjänst för att förenkla moln hantering genom process automatisering. Att använda Azure Automation, långvariga, manuella, fel känsliga och ofta återkommande uppgifter kan automatiseras för att öka tillförlitligheten, effektiviteten och tiden för din organisation. Information om att komma igång finns [Azure Automation Intro](../../automation/automation-intro.md)

Azure Automation tillhandahåller en motor för körning av arbets flöden med hög tillförlitlighet och hög tillgänglighet och som kan skalas för att uppfylla dina behov när organisationen växer. I Azure Automation kan processer startas manuellt, av system från tredje part eller med schemalagda intervall så att aktiviteterna sker exakt vid behov.

Sänk drifts kostnader och frigör IT/DevOps-personalen och fokusera på arbete som lägger till affärs värde genom att flytta dina moln hanterings uppgifter så att de körs automatiskt av Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-database"></a>Hur kan Azure Automation hjälpa till att hantera Azure SQL Database

Azure SQL Database kan hanteras i Azure Automation med hjälp av [PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) som är tillgängliga i [Azure PowerShell-verktyg](/powershell/azure/overview). Azure Automation har dessa Azure SQL Database PowerShell-cmdlets tillgängliga i rutan så att du kan utföra alla dina SQL Database hanterings uppgifter i tjänsten. Du kan också para ihop dessa cmdlets i Azure Automation med cmdletar för andra Azure-tjänster, för att automatisera komplexa uppgifter i Azure-tjänster och på olika system.

Azure Automation kan också kommunicera med SQL-servrar direkt genom att utfärda SQL-kommandon med PowerShell.

Galleri för Runbook och moduler för [Azure Automation](../../automation/automation-runbook-gallery.md) flera olika Runbooks från Microsoft och communityn som du kan importera till Azure Automation. Om du vill använda en, laddar du ned en Runbook från galleriet eller så kan du importera runbooks direkt från galleriet eller från ditt Automation-konto i Azure Portal.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna om Azure Automation och hur det kan användas för att hantera Azure SQL Database, kan du följa dessa länkar om du vill veta mer om Azure Automation.

- [Översikt över Azure Automation](../../automation/automation-intro.md)
- [Min första Runbook](../../automation/learn/automation-tutorial-runbook-graphical.md)
