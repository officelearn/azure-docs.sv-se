---
title: Analysera data med Azure Machine Learning | Microsoft Docs
description: Använd Azure Machine Learning för att skapa en förutsägbar Machine Learning-modell som baseras på data lagrade i Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 09/14/2016
ms.author: kevin;barbkess;sonyama

---
# Analysera data med Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> 
> 

Denna självstudie använder Azure Machine Learning för att skapa en förutsägbar Machine Learning-modell som baseras på data lagrade i Azure SQL Data Warehouse. Mer specifikt skapar detta en riktad marknadsföringskampanj för Adventure Works, en cykelbutik, genom att förutsäga hur sannolikt det är att en kund kommer att köpa en cykel.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## Krav
För att gå igenom de här självstudierna, behöver du:

* Ett SQL Data Warehouse förinstallerat med AdventureWorksDW som exempeldatabas. Om du vill distribuera detta, se [Skapa ett SQL Data Warehouse][Skapa ett SQL Data Warehouse] och välj att läsa in exempeldata. Om du redan har ett Data Warehouse men inte har exempeldata, kan du [läsa in exempeldata manuellt][läsa in exempeldata manuellt].

## 1. Hämta data
Aktuella data finns i dbo.vTargetMail-vyn i AdventureWorksDW-databasen. Så här läser du in dessa data:

1. Logga in på [Azure Machine Learning-studio][Azure Machine Learning-studio] och klicka på mina experiment.
2. Klicka på **+NY** och välj **Tomt experiment**.
3. Ange ett namn för experimentet: Riktad marknadsföring.
4. Dra modulen **Läsare** från modulfönstret till arbetsytan.
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

## 2. Rensa data
För att rensa data kommer vi att släppa vissa kolumner som inte är relevanta för modellen. Gör så här:

1. Dra modulen **Projektkolumner** till arbetsytan.
2. Klicka på **Starta kolumnväljaren** i fönstret Egenskaper för att ange vilka kolumner du vill ta bort.
   ![Projektkolumner][4]
3. Exkludera två kolumner: CustomerAlternateKey och GeographyKey.
   ![Ta bort onödiga kolumner][5]

## 3. Skapa modellen
Vi delar data 80–20: 80 % för att träna en maskininlärningsmodell och 20 % för att testa modellen. Vi använder ”Tvåklassalgoritmer” för detta binära klassificeringsproblem.

1. Dra modulen **Dela** till arbetsytan.
2. Ange 0,8 för andel av rader i den första utdatamängden i fönstret Egenskaper.
   ![Dela data till uppsättningar för träning och testning][6]
3. Dra modulen **tvåklassförhöjt beslutsträd** till arbetsytan.
4. Dra modulen **Träningsmodell** till arbetsytan och ange indata. Klicka sedan på **Starta kolumnväljaren** i fönstret Egenskaper.
   * Första indata: ML-algoritmen.
   * Andra indata: Data att träna algoritmen på.
     ![Anslut träningsmodellmodulen][7]
5. Välj kolumnen **BikeBuyer** som kolumn att förutsäga.
   ![Välj kolumn att förutsäga][8]

## 4. Poängsätt modellen
Vi kommer nu att testa hur modellen presterar på testdata. Vi kommer att jämföra algoritmen vi valt med en annan algoritm för att se vilken som presterar bäst.

1. Dra modulen **Poängmodell** till arbetsytan.
    Första indata: tränad modell, Andra indata: testdata ![poängsätt modellen][9]
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

## Nästa steg
Mer information om hur du skapar förutsägbara maskininlärningsmodeller finns i [Introduktion till Machine Learning på Azure][Introduktion till Machine Learning på Azure].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure Machine Learning-studio]:https://studio.azureml.net/
[Introduktion till Machine Learning på Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[läsa in exempeldata manuellt]: sql-data-warehouse-load-sample-databases.md
[Skapa ett SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md



<!--HONumber=Sep16_HO3-->


