---
title: Analysera data med Azure Machine Learning
description: Använd Azure Machine Learning för att skapa en modell för automatisk maskininlärning baserat på data som lagras i Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 07570fd456d7f5a75a6b5a3ee635605a5d40072a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350575"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analysera data med Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS (SSMS)](sql-data-warehouse-query-ssms.md)
> 
> 

Den här självstudien använder Azure Machine Learning för att skapa en modell för automatisk maskininlärning baserat på data som lagras i Azure Synapse. Mer specifikt skapar detta en riktad marknadsföringskampanj för Adventure Works, en cykelbutik, genom att förutsäga hur sannolikt det är att en kund kommer att köpa en cykel.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Krav
För att gå igenom de här självstudierna, behöver du:

* En SQL-pool förinstallerad med AdventureWorksDW-exempeldata. Information om hur du etablerar detta finns i [Skapa en SQL-pool](create-data-warehouse-portal.md) och välja att läsa in exempeldata. Om du redan har ett Data Warehouse men inte har exempeldata kan du [läsa in exempeldata manuellt](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-get-the-data"></a>1. Få data
Aktuella data finns i dbo.vTargetMail-vyn i AdventureWorksDW-databasen. Så här läser du in dessa data:

1. Logga in i [Azure Machine Learning Studio](https://studio.azureml.net/) och klicka på My experiments (Mina experiment).
2. Klicka på **+NYTT** längst ned till vänster på skärmen och välj **Tomt experiment**.
3. Ange ett namn för experimentet: Riktad marknadsföring.
4. Dra **datamodulen Importera** under **Datainmatning och utdata** från modulerna till arbetsytan.
5. Ange information om SQL-poolen i fönstret Egenskaper.
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

![Kör experimentet](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

När experimentet har körts, klicka på utdataporten längst ned i läsarmodulen och välj **Visualisera** för att se data som importerats.

![Visa data som importerats](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. Rensa data
För att rensa data kommer vi att släppa vissa kolumner som inte är relevanta för modellen. Gör så här:

1. Dra modulen **Välj kolumner i datauppsättning** under **Dataomvandling < manipulering** till arbetsytan. Anslut den här modulen till modulen **Importera data.**
2. Klicka på **Starta kolumnväljaren** i fönstret Egenskaper för att ange vilka kolumner du vill ta bort.

   ![Projektkolumner](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. Exkludera två kolumner: CustomerAlternateKey och GeographyKey.

   ![Ta bort onödiga kolumner](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. Bygg modellen
Vi delar data 80–20: 80 % för att träna en maskininlärningsmodell och 20 % för att testa modellen. Vi kommer att använda sig av "Två-klass" algoritmer för denna binära klassificering problem.

1. Dra modulen **Dela** till arbetsytan.
2. Ange 0,8 för Bråk av rader i den första utdatauppsättningen i egenskapsfönstret.

   ![Dela data till uppsättningar för träning och testning](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. Dra modulen **tvåklassförhöjt beslutsträd** till arbetsytan.
4. Dra **modulen Tågmodell** till arbetsytan och ange indata genom att ansluta den till **ml-modulen (Two-Class Boosted Decision Tree)** och **Split** (data för att träna algoritmen på). 

     ![Anslut träningsmodellmodulen](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. Klicka sedan på **Starta kolumnväljaren** i fönstret Egenskaper. Välj kolumnen **BikeBuyer** som kolumn att förutsäga.

   ![Välj kolumn att förutsäga](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. Betyg på modellen
Vi kommer nu att testa hur modellen presterar på testdata. Vi kommer att jämföra algoritmen vi valt med en annan algoritm för att se vilken som presterar bäst.

1. Dra **poängmodellmodulen** till arbetsytan och anslut den till **moduler för tågmodell** och delade **data.**

   ![Poängsätt modellen](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. Dra **Tvåklass, Bayes Point-dator** till arbetsytan för experimentet. Vi kommer att jämföra hur den här algoritmen presterar i jämförelse med det tvåklassförhöjda beslutsträdet.
3. Kopiera och klistra in modulerna Träningsmodell och Poängmodell i arbetsytan.
4. Dra modulen **Utvärdera modell** till arbetsytan för att jämföra de två algoritmerna.
5. **Kör** experimentet.

   ![Kör experimentet](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. Klicka på utdataporten längst ned i modulen Utvärdera modell och klicka på Visualisera.

   ![Visualisera utvärderingsresultaten](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

De mått som tillhandahålls är ROC-kurvan, precisionsåterkallningsdiagrammet och lyftkurvan. Genom att titta på de här måtten kan vi se att den första modellen presterade bättre än den andra. Om du vill titta på vad den första modellen förutspådde klickar du på utdataporten i poängmodellen och klickar på Visualisera.

![Visualisera poängresultat](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

Du ser två kolumner som läggs till i testdata.

* Poängsatt sannolikhet: sannolikheten att en kund är en cykelköpare.
* Poängsatta etiketter: klassificering utförd av modellen – cykelköpare (1) eller inte (0). Det här sannolikhetströskelvärdet för etikettering anges till 50 % och kan justeras.

Genom att jämföra kolumnen BikeBuyer (faktiska) med Poängsatta etiketter (förutsagda), kan du se hur väl modellen har utförts. Därefter kan du använda den här modellen för att göra förutsägelser för nya kunder och publicera den här modellen som en webbtjänst eller skriva tillbaka resultat till Azure Synapse.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du skapar förutsägbara maskininlärningsmodeller finns i [Introduktion till Machine Learning i Azure](https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/).