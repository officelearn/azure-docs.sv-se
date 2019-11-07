---
title: Installera Visual Studio 2019
description: Installera Visual Studio och SQL Server Development Tools (SSDT) för Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: seo-lt-2019
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/17/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: caf4a3efbfafcdb10a8834c213b2469e30296501
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692670"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Komma igång med Visual Studio 2019 för SQL Data Warehouse
Visual Studio **2019** SQL Server Data Tools (SSDT) är ett enda verktyg som gör att du kan göra följande:

- Anslut, fråga och utveckla program för SQL Data Warehouse 
- Använd en objekt Utforskare för att visuellt utforska alla objekt i din data modell, inklusive tabeller, vyer, lagrade procedurer och så vidare.
- Generera T-SQL Data Definition Language-skript (DDL) för dina objekt
- Utveckla ditt informations lager med hjälp av en tillstånds-baserad metod med SSDT databas projekt
- Integrera ditt databas projekt med käll kontroll system som git med Azure databaser
- Konfigurera kontinuerliga integrerings-och distributions pipeliner med Automation-servrar som Azure DevOps

> [!NOTE]
> För närvarande är Visual Studio SSDT databas projekt i för hands version. Om du vill ta emot regelbundna uppdateringar av den här funktionen kan du rösta på [UserVoice].

## <a name="install-visual-studio-2019-preview"></a>Installera Visual Studio 2019 Preview
Se [Hämta Visual Studio 2019][] för att ladda ned och installera visual Studio **16,3 och senare**. Under installationen väljer du arbets belastningen lagring och bearbetning av data. Installation av fristående SSDT krävs inte längre i Visual Studio 2019.

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>Rapportera problem med SSDT Visual Studio 2019 (för hands version)
Om du vill rapportera problem när du använder SSDT med SQL Data Warehouse, e-posta följande e-postdistributions lista: <sqldwssdtpreview@service.microsoft.com>

## <a name="next-steps"></a>Nästa steg
Nu när du har den senaste versionen av SSDT är du redo att [ansluta][connect] till din SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Hämta Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
