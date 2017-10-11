---
title: "Installera Visual Studio och SSDT för SQL Data Warehouse | Microsoft Docs"
description: "Installera Visual Studio och SQL Server Development Tools (SSDT) för Azure SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: antvgski
manager: jhubbard
editor: 
ms.assetid: 0ed9b406-9b42-4fe6-b963-fe0a5b48aac1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: connect
ms.date: 03/30/2017
ms.author: anvang;barbkess
ms.openlocfilehash: f7023b78c241a7bc8014276cd0bfa455165b42cc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Installera Visual Studio och SSDT för SQL Data Warehouse
För att utveckla program för SQL Data Warehouse, rekommenderar vi använder den senaste versionen av Visual Studio med den senaste versionen av SQL Server Data Tools (SSDT).  Visual Studio 2013, uppdatering 5 med SSDT, stöds också för bakåtkompatibilitet.  

Visual Studio med SSDT låter dig använda SQL Server Object Explorer för att visuellt utforska tabeller, vyer, lagrade procedurer och många fler objekt i ditt SQL Data Warehouse och köra frågor.

> [!NOTE]
> SQL Data Warehouse stöder ännu inte Visual Studio Database Projects.  Den funktionen kommer läggas till i framtida versioner.
> 
> 

## <a name="step-1-install-visual-studio"></a>Steg 1: Installera Visual Studio
Följa dessa länkar om du vill hämta och installera Visual Studio. Om du redan har Visual Studio 2013 eller senare, du kan gå vidare till steg 2, installera SSDT.

1. [Hämta Visual Studio][].
2. Följ den [installera Visual Studio] [ Installing Visual Studio] på MSDN och välj standardkonfigurationerna.

## <a name="step-2-install-ssdt"></a>Steg 2: Installera SSDT
För att installera SSDT för Visual Studio, kollar du efter en SSDT uppdatering i Visual Studio genom att följa de här stegen.

1. I Visual Studio klickar du på **verktyg** / **tillägg och uppdateringar...** / **Uppdateringar**
2. Välj **Produktuppdateringar** och hitta sedan **Microsoft SQL Server-uppdatering för databas-tooling**

Om ingen uppdatering hittas, bör du ha den senaste versionen installerad.  Bekräfta SSDT har installerats genom att klicka på **Hjälp** / **Om Microsoft Visual Studio** och leta efter SQL Server Data Tools i listan.  Den senaste versionen av SSDT är 14.0.60525.0.  Om alternativet att installera inte är tillgänglig från Visual Studio alternativt så kan du besöka den [SSDT hämta] [ SSDT Download] sidan om du vill hämta och installera SSDT manuellt.

## <a name="next-steps"></a>Nästa steg
Nu när du har den senaste versionen av SSDT, är du redo att [ansluta] [ connect] till SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Hämta Visual Studio]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
