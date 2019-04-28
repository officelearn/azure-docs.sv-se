---
title: Analysera data med Azure Machine Learning | Microsoft Docs
description: Använd Azure Machine Learning för att skapa en förutsägbar Machine Learning-modell som baseras på data lagrade i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 03/22/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 7f9500adc6871c4c9f81c32bf456bc36cf91db4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440035"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analysera data med Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Denna självstudie använder Azure Machine Learning för att skapa en förutsägbar Machine Learning-modell som baseras på data lagrade i Azure SQL Data Warehouse. Mer specifikt skapar detta en riktad marknadsföringskampanj för Adventure Works, en cykelbutik, genom att förutsäga hur sannolikt det är att en kund kommer att köpa en cykel.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Nödvändiga komponenter
För att gå igenom de här självstudierna, behöver du:

* Ett SQL Data Warehouse förinstallerat med AdventureWorksDW som exempeldatabas. Om du vill distribuera detta läser du [Skapa ett SQL Data Warehouse][Create a SQL Data Warehouse] och väljer att läsa in exempeldata. Om du redan har ett Data Warehouse men inte har exempeldata kan du [läsa in exempeldata manuellt][load sample data manually].

## <a name="1-get-the-data"></a>1. Hämta data
Aktuella data finns i dbo.vTargetMail-vyn i AdventureWorksDW-databasen. Så här läser du in dessa data:

1. Logga in i [Azure Machine Learning Studio][Azure Machine Learning studio] och klicka på My experiments (Mina experiment).
2. Klicka på **+ ny** längst ned till vänster på skärmen och välj **tomt Experiment**.
3. Ange ett namn för experimentet: Riktad marknadsföring.
4. Dra den **dataimport** modulen under **Data indata och utdata** från modulfönstret till arbetsytan.
5. Ange information om din SQL Data Warehouse-databas i fönstret Egenskaper.
6. Ange **fråga** för databasen för att läsa data av intresse.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Kör försöket genom att klicka på **Kör** under experimentets arbetsyta.
![Kör experimentet][1]

När experimentet har körts, klicka på utdataporten längst ned i läsarmodulen och välj **Visualisera** för att se data som importerats.
![Visa data som importerats][3]

## <a name="2-clean-the-data"></a>2. Rensa data
För att rensa data kommer vi att släppa vissa kolumner som inte är relevanta för modellen. Gör så här:

1. Dra den **Välj kolumner i datauppsättning** modulen under **Dataomvandling < manipulering av** till arbetsytan. Anslut den här modulen till den **importdata** modulen.
2. Klicka på **Starta kolumnväljaren** i fönstret Egenskaper för att ange vilka kolumner du vill ta bort.
   ![Projektkolumner][4]
3. Exkludera två kolumner: CustomerAlternateKey och GeographyKey.
   ![Ta bort onödiga kolumner][5]

## <a name="3-build-the-model"></a>3. Skapa modellen
Vi delar data 80 – 20: 80% för att träna en maskininlärningsmodell och 20% för att testa modellen. Vi använder ”Tvåklassalgoritmer” för detta binära klassificeringsproblem.

1. Dra modulen **Dela** till arbetsytan.
2. Ange 0,8 för andel av rader i den första utdatauppsättningen i egenskapsfönstret.
   ![Dela data till uppsättningar för träning och testning][6]
3. Dra modulen **tvåklassförhöjt beslutsträd** till arbetsytan.
4. Dra den **träna modell** modulen till arbetsytan och ange indata via en anslutning till den **Tvåklassförhöjt beslutsträd** (ML-algoritmen) och **dela** (data att träna den moduler för algoritmen på). 
     ![Anslut träningsmodellmodulen][7]
5. Klicka sedan på **Starta kolumnväljaren** i fönstret Egenskaper. Välj kolumnen **BikeBuyer** som kolumn att förutsäga.
   ![Välj kolumn att förutsäga][8]

## <a name="4-score-the-model"></a>4. Poängsätt modellen
Vi kommer nu att testa hur modellen presterar på testdata. Vi kommer att jämföra algoritmen vi valt med en annan algoritm för att se vilken som presterar bäst.

1. Dra **Poängmodell** modulen till arbetsytan och anslut den till **Träningsmodell** och **dela Data** moduler.
   ![Poängsätt modellen][9]
2. Dra **Tvåklass, Bayes Point-dator** till arbetsytan för experimentet. Vi kommer att jämföra hur den här algoritmen presterar i jämförelse med det tvåklassförhöjda beslutsträdet.
3. Kopiera och klistra in modulerna Träningsmodell och Poängmodell i arbetsytan.
4. Dra modulen **Utvärdera modell** till arbetsytan för att jämföra de två algoritmerna.
5. **Kör** experimentet.
   ![Kör experimentet][10]
6. Klicka på utdataporten längst ned i modulen Utvärdera modell och klicka på Visualisera.
   ![Visualisera utvärderingsresultaten][11]

Mätvärdena är ROC-kurvan, precisionsåterkallningsdiagram och lyftkurvan. Genom att titta på de här måtten kan vi se att den första modellen presterade bättre än den andra. För att titta på hur den första modellen förutsade, klicka på utdataporten i Poängmodell och klicka på Visualisera.
![Visualisera poängresultat][12]

Du ser två kolumner som läggs till i testdata.

* Poängsatt sannolikhet: sannolikheten att en kund är en cykelköpare.
* Poängsatta etiketter: klassificering utförd av modellen – cykelköpare (1) eller inte (0). Det här sannolikhetströskelvärdet för etikettering anges till 50 % och kan justeras.

Genom att jämföra kolumnen BikeBuyer (faktiska) med Poängsatta etiketter (förutsagda), kan du se hur väl modellen har utförts. Du kan använda den här modellen som nästa steg för att göra förutsägelser för nya kunder och publicera den här modellen som en webbtjänst eller skriva resultaten tillbaka till SQL Data Warehouse.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du skapar förutsägbara maskininlärningsmodeller finns i [Introduktion till Machine Learning i Azure][Introduction to Machine Learning on Azure].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2-visualize-new.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
