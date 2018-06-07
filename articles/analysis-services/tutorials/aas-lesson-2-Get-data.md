---
title: 'Azure Analysis Services självstudiekurs 2: Hämta data | Microsoft Docs'
description: Beskriver hur du hämtar och importerar data i Azure Analysis Services-självstudieprojektet.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d66b3f303ab4e19ee645be8768512a451454769b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596982"
---
# <a name="get-data"></a>Hämta data

Under den här lektionen använder du Hämta data i SSDT för att ansluta till AdventureWorks-exempeldatabasen, väljer data, förhandsgranskar och filtrerar, och importerar sedan till modellarbetsytan.  
  
Med Hämta data kan du importera data från en mängd olika datakällor: Azure SQL Database, Oracle, Sybase, OData-Feed, Teradata, filer med mera. Det går även att fråga data med ett Power Query M-formeluttryck.

> [!NOTE]
> Uppgifter och bilder i den här kursen visar hur ansluter till en AdventureWorksDW2014-databas på en lokal server. I vissa fall kan en AdventureWorks-databas på Azure skilja sig.
  
Uppskattad tidsåtgång för den här lektionen: **10 minuter**  
  
## <a name="prerequisites"></a>Förutsättningar  
Det här avsnittet ingår i självstudiekursen för tabellmodellering som bör slutföras i rätt ordning. Innan du utför uppgifterna i den här lektionen bör du ha slutfört föregående lektion: [Lektion 1: Skapa ett nytt tabellmodellprojekt](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md).  
  
## <a name="create-a-connection"></a>Skapa en anslutning  
  
#### <a name="to-create-a-connection-to-the-adventureworksdw2014-database"></a>Att skapa en anslutning till databasen AdventureWorksDW2014  
  
1.  Högerklicka på **Datakällor** > **Importera från datakälla** i tabellmodellutforskaren.  
  
    Det här startar Hämta data, som hjälper dig att ansluta till en datakälla. Om du inte ser tabellmodellutforskaren i **Solution Explorer** dubbelklickar du på **Model.bim** för att öppna modellen i designern. 
    
    ![aas-lesson2-getdata](../tutorials/media/aas-lesson2-getdata.png)
  
2.  I Hämta Data klickar du på **Databas** > **SQL Server-databas** > **Anslut**.  
  
3.  I dialogrutan **SQL Server-databas** i **Server** anger du namnet på den server där du installerade AdventureWorksDW2014-databasen och klickar sedan på **Anslut**.  

4.  När du uppmanas att ange autentiseringsuppgifter så måste du ange de autentiseringsuppgifter som Analysis Services använder för att ansluta till datakällan vid importering och bearbetning av data. I **personifieringsläget** väljer du **Personifiera konto**, anger autentiseringsuppgifter och klickar på **Anslut**. Vi rekommenderar att du använder ett konto vars lösenord inte upphör att gälla.

    ![aas-lesson2-account](../tutorials/media/aas-lesson2-account.png)
  
    > [!NOTE]  
    > Den säkraste metoden för att ansluta till en datakälla är att använda ett Windows-användarkonto och lösenord.
  
5.  I navigatören markerar du databasen **AdventureWorksDW2014** och klickar sedan på **OK**. Då skapas en anslutning till databasen. 
  
6.  I navigatören markerar du kryssrutorna för följande tabeller: **DimCustomer**, **DimDate**, **DimGeography**, **DimProduct**, **DimProductCategory**, **DimProductSubcategory** och **FactInternetSales**.  

    ![aas-lesson2-select-tables](../tutorials/media/aas-lesson2-select-tables.png)
  
När du klickar på OK öppnas frågeredigeraren. I nästa avsnitt väljer du endast de data som du vill importera.

  
## <a name="filter-the-table-data"></a>Filtrera tabelldata  
Tabeller i exempeldatabasen AdventureWorksDW2014 har data som inte är nödvändiga att ta med i modellen. Om det är möjligt filtrerar du bort onödiga data för att spara på minnesutrymmet som används av modellen. Du kan filtrera bort vissa kolumner från tabeller så att de inte importeras till arbetsytedatabasen eller modelldatabasen när den har distribuerats. 
  
#### <a name="to-filter-the-table-data-before-importing"></a>Filtrera tabelldata innan du importerar  
  
1.  Markera tabellen **DimCustomer** i frågeredigeraren. En vy av tabellen DimCustomer i datakällan (exempeldatabasen AdventureWorksDW2014) visas. 
  
2.  Flermarkera (Ctrl + klicka) **SpanishEducation**, **FrenchEducation**, **SpanishOccupation**, **FrenchOccupation** och högerklicka och klicka sedan på **Ta bort kolumner**. 

    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-columns.png)
  
    Eftersom värdena för de här kolumnerna inte är relevanta för analysen av Internetförsäljning så behöver du inte importera dessa kolumner. Genom att ta bort onödiga kolumner blir din modell mindre och effektivare.  

    > [!TIP]
    > Om du gör ett misstag kan säkerhetskopiera genom att ta bort ett steg i **ANVÄNDA STEG**.   
    
    ![aas-lesson2-remove-columns](../tutorials/media/aas-lesson2-remove-step.png)

  
4.  Filtrera de återstående tabellerna genom att ta bort följande kolumner i varje tabell:  
    
    **DimDate**
    
      |Kolumn|  
      |--------|  
      |**DateKey**|  
      |**SpanishDayNameOfWeek**|  
      |**FrenchDayNameOfWeek**|  
      |**SpanishMonthName**|  
      |**FrenchMonthName**|  
  
    **DimGeography**
  
      |Kolumn|  
      |-------------|  
      |**SpanishCountryRegionName**|  
      |**FrenchCountryRegionName**|  
      |**IpAddressLocator**|  
  
    **DimProduct**
  
      |Kolumn|  
      |-----------|  
      |**SpanishProductName**|  
      |**FrenchProductName**|  
      |**FrenchDescription**|  
      |**ChineseDescription**|  
      |**ArabicDescription**|  
      |**HebrewDescription**|  
      |**ThaiDescription**|  
      |**GermanDescription**|  
      |**JapaneseDescription**|  
      |**TurkishDescription**|  
  
    **DimProductCategory**
  
      |Kolumn|  
      |--------------------|  
      |**SpanishProductCategoryName**|  
      |**FrenchProductCategoryName**|  
  
    **DimProductSubcategory**
  
      |Kolumn|  
      |-----------------------|  
      |**SpanishProductSubcategoryName**|  
      |**FrenchProductSubcategoryName**|  
  
    **FactInternetSales**
  
      Inga kolumner har tagits bort.
  
## <a name="Import"></a>Importera de markerade tabellerna och kolumndata  
Nu när du har förhandsgranskat och filtrerat bort onödiga data kan du importera resten av de data som du behöver. Med guiden importeras tabelldata och eventuella relationer mellan tabellerna. Nya tabeller och kolumner skapas i modellen och data som du filtrerat ut importeras inte.  
  
#### <a name="to-import-the-selected-tables-and-column-data"></a>Så här importerar du de markerade tabellerna och kolumndata  
  
1.  Granska dina val. Om allt ser bra ut klickar du på **Importera**. I dialogrutan Databearbetning visas status för data som importeras från din datakälla till arbetsytedatabasen.
  
    ![aas-lesson2-success](../tutorials/media/aas-lesson2-success.png) 
  
2.  Klicka på **Stäng**.  

  
## <a name="save-your-model-project"></a>Spara modellprojektet  
Det är viktigt att spara modellprojektet ofta.  
  
#### <a name="to-save-the-model-project"></a>Så här sparar du modellprojektet  
  
-   Klicka på **Arkiv** > **Spara alla**.  
  
## <a name="whats-next"></a>Nästa steg
[Lektion 3: Markera som datumtabell](../tutorials/aas-lesson-3-mark-as-date-table.md).

  
  
