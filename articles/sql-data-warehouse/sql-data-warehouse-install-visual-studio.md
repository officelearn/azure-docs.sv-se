---
title: Installera Visual Studio och SSDT för SQL Data Warehouse | Microsoft Docs
description: Installera Visual Studio och SQL Server Development Tools (SSDT) för Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/05/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: b2e34f1f72b1b0aa76d4a3031102d052118dae5f
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304118"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Installera Visual Studio och SSDT för SQL Data Warehouse
Använd Visual Studio 2019 för att utveckla program för SQL Data Warehouse. Visual Studio 2019 SSDT stöds för närvarande inte för SQL Data Warehouse. 

Visual Studio med SSDT kan du använda SQL Server Object Explorer för att utforska tabeller, vyer, lagrade procedurer och många fler objekt visuellt i SQL Data Warehouse. Du kan också köra frågor.

> [!NOTE]
> SQL Data Warehouse stöder ännu inte Visual Studio Database Projects. För att få periodiska uppdateringar på den här funktionen, rösta på [UserVoice].
> 
> 

## <a name="step-1-install-visual-studio"></a>Steg 1: Installera Visual Studio
Följ länkarna för att ladda ned och installera Visual Studio. Om du redan har Visual Studio 2013 eller senare kan du gå vidare till steg 2, installera SSDT.

1. [Ladda ned Visual Studio][].
2. Följ den [installera Visual Studio] [ Installing Visual Studio] på MSDN och välj standardkonfigurationerna.

## <a name="step-2-install-ssdt"></a>Steg 2: Installera SSDT
Om du vill installera SSDT för Visual Studio, kontrollera först efter en SSDT uppdatering från Visual Studio genom att följa dessa steg.

1. I Visual Studio klickar du på **verktyg** / **tillägg och uppdateringar...** / **Uppdateringar**
2. Välj **Produktuppdateringar** och hitta sedan **Microsoft SQL Server-uppdatering för databas-tooling**

Du bör ha den senaste versionen installerad om en uppdatering inte hittas. Bekräfta SSDT har installerats genom att klicka på **Hjälp** / **Om Microsoft Visual Studio** och leta efter SQL Server Data Tools i listan. Om alternativet att installera inte är tillgängligt från Visual Studio, kan du besöka den [SSDT hämta] [ SSDT Download] sidan för att hämta och installera SSDT manuellt.

## <a name="next-steps"></a>Nästa steg
Nu när du har den senaste versionen av SSDT är du redo att [ansluta] [ connect] till SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Ladda ned Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
