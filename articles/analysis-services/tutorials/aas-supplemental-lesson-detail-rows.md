---
title: 'Kompletterande lektion i Azure Analysis Services-självstudiekurs: Detaljrader | Microsoft Docs'
description: Beskriver hur du skapar uttryck för rader med detaljerad information i Azure Analysis Services-självstudiekursen.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6e15788992e66fdb8e742b290e5bcf5bce8d20fb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596108"
---
# <a name="supplemental-lesson---detail-rows"></a>Kompletterande lektion – Detaljrader

I den här kompletterande lektionen använder du DAX-redigeraren för att definiera ett anpassad uttryck för rader med detaljerad information. Ett uttryck för rader med detaljerad information är en egenskap för ett mått som ger slutanvändarna mer information om det aggregerade resultatet av ett mått. 
  
Uppskattad tidsåtgång för den här lektionen: **10 minuter**  
  
## <a name="prerequisites"></a>Förutsättningar  
Den här kompletterande lektionen ingår i en självstudiekurs om tabellmodeller. Innan du utför uppgifterna i den här kompletterande lektionen måste du ha slutfört alla föregående lektioner eller ha ett slutfört Adventure Works Internet Sales-exempelmodellprojekt.  
  
## <a name="whats-the-issue"></a>Vad är problemet?
Nu ska vi titta på information om måttet InternetTotalSales innan du lägger till ett uttryck för information om rader.

1.  Klicka på menyn **Modell** > **Analysera i Excel** i SSDT för att öppna Excel och skapa en tom pivottabell.
  
2.  I **Pivottabellfält** lägger du till måttet **InternetTotalSales** från tabellen FactInternetSales till **Values**, **CalendarYear** från tabellen DimDate till **Columns** och **EnglishCountryRegionName** till **Rows**. Pivottabellen kan nu en sammanlagda resultat från InternetTotalSales måttet av regioner och år. 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. Dubbelklicka på ett aggregerat värde för ett år och ett regionnamn i pivottabellen. Värdet för Australien och år 2014. Ett nytt blad öppnas som innehåller data, men inte användbara data.

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
Avsikten är en tabell som innehåller kolumner och rader med data som bidrar till aggregerade resultatet av måttet InternetTotalSales. För att göra det lägger du till ett uttryck för information om rader som en egenskap för måttet.

## <a name="add-a-detail-rows-expression"></a>Lägga till ett uttryck för rader med detaljerad information

#### <a name="to-create-a-detail-rows-expression"></a>Så här skapar du ett uttryck för rader med detaljerad information 
  
1. Klicka på måttet **InternetTotalSales** i FactInternetSales-tabellens rutnät för mått i Visual Studio. 

2. I **Egenskaper** > **Uttryck för rader med detaljerad information** klickar du på knappen för redigeraren för att öppna DAX-redigeraren.

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. Ange följande uttryck i DAX-redigeraren:

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    Det här uttrycket anger namn, kolumner och måttresultat från tabellen FactInternetSales och relaterade tabeller returneras om en användare dubbelklickar på ett aggregerat resultat i en pivottabell eller rapport.

4. Gå tillbaka till Excel, ta bort bladet som du skapade i steg 3 och dubbelklicka på ett aggregerat värde. Nu när en egenskap för uttryck för rader med detaljerad information har definierats för måttet öppnas ett nytt blad med mer användbara data.

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. Distribuera om din modell.

  
## <a name="see-also"></a>Se också  

[Funktionen SELECTCOLUMNS (DAX)](https://msdn.microsoft.com/library/mt761759.aspx)   
[Kompletterande lektionen - dynamiska säkerhet](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Kompletterande lektion – Ojämna hierarkier](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
 