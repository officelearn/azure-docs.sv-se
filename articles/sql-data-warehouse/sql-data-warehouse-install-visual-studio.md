<properties
   pageTitle="Installera Visual Studio och SSDT för SQL Data Warehouse | Microsoft Azure"
   description="Installera Visual Studio och SQL Server Development Tools (SSDT) för Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="sonyama;barbkess"/>

# Installera Visual Studio 2015 och SSDT för SQL Data Warehouse

För utveckling av program för SQL Data Warehouse, rekommenderar vi att du använder Visual Studio 2015 med den senaste versionen av SQL Server Data Tools (SSDT).  Visual Studio 2013 med SSDT stöds också för bakåtkompatibilitet.  

Visual Studio med SSDT låter dig använda SQL Server Object Explorer för att visuellt utforska tabeller, vyer, lagrade procedurer och många fler objekt i ditt SQL Data Warehouse och köra frågor.

> [AZURE.NOTE] SQL Data Warehouse stöder ännu inte Visual Studio Database Projects.  Den funktionen kommer läggas till i framtida versioner.

## Steg 1: Installera Visual Studio 2015

Följ länkarna för att hämta och installera Visual Studio 2015. Om du redan har Visual Studio 2013 eller 2015 installerat, kan du gå vidare till steg 2, installera SSDT.

1. [Hämta Visual Studio 2015][].
2. Följ guiden [Installera Visual Studio][] på MSDN och välj standardkonfigurationerna.

## Steg 2: Installera SSDT

För att installera SSDT för Visual Studio, kollar du efter en SSDT uppdatering i Visual Studio genom att följa de här stegen.

1. I Visual Studio klickar du på **Verktyg** / **Tillägg och uppdateringar...** / **Uppdateringar**
2. Välj **Produktuppdateringar** och hitta sedan **Microsoft SQL Server-uppdatering för databas-tooling**

Om ingen uppdatering hittas, bör du ha den senaste versionen installerad.  Bekräfta SSDT har installerats genom att klicka på **Hjälp** / **Om Microsoft Visual Studio** och leta efter SQL Server Data Tools i listan.  Den senaste versionen av SSDT är 14.0.60525.0.  Om alternativet att installera inte är tillgängligt från Visual Studio så kan du besöka sida [SSDT hämta][], för att manuellt hämta och installera SSDT.

## Nästa steg

Nu när du har den senaste versionen av SSDT, är du redo att [ansluta][] till ditt SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[ansluta]: ./sql-data-warehouse-get-started-connect.md

<!--Other-->
[Hämta Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Installera Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT hämta]: https://msdn.microsoft.com/library/mt204009.aspx



<!--HONumber=Jun16_HO2-->


