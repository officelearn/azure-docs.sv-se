---
title: Hantera Azure Data Lake Analytics-principer
description: Lär dig hur du använder principer för att styra användningen av ett Data Lake Analytics-konto.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: f84cb59e7d4fd7d8301d22348ca066a7f9d9e94e
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048826"
---
# <a name="manage-azure-data-lake-analytics-using-policies"></a>Hantera Azure Data Lake Analytics med hjälp av principer

Med hjälp av principer för konton, kan du styra hur resurser ett Azure Data Lake Analytics-konto är används. Dessa principer kan du kontrollera kostnaden för att använda Azure Data Lake Analytics. Med dessa principer kan du förhindra oväntade toppar genom att begränsa hur många analysenheter samtidigt använda kontot.

## <a name="account-level-policies"></a>Kontonivå principer

Dessa principer gäller för alla jobb i ett Data Lake Analytics-konto.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maximalt antal AU: er i ett Data Lake Analytics-konto
En princip styr det totala antalet analysenheter (AU) kan använda Data Lake Analytics-kontot. Värdet är som standard är 250. Exempel: om det här värdet är inställt på 250 analysenheter, kan du ha ett jobb körs med 250 analysenheter som tilldelats eller 10 jobb som körs med 25 analysenheter varje. Ytterligare jobb som skickas köas tills jobb som körs är klar. När jobb som körs är slutförda frigörs AU: er för att köra köade jobb.

Ändra antalet analysenheter för ditt Data Lake Analytics-konto:

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **maximala AU: er**, flytta skjutreglaget för att välja ett värde eller ange värdet i textrutan. 
4. Klicka på **Spara**.

> [!NOTE]
> Om du behöver mer än standardvärdet (250) analysenheter, i portalen klickar du på **hjälp + Support** att skicka en supportförfrågan. Antalet AU som är tillgängliga i ditt Data Lake Analytics-konto kan ökas.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maximalt antal jobb som kan köras samtidigt
En princip styr hur många jobb kan köras samtidigt. Det här värdet anges som standard till 20. Om Data Lake Analytics har analysenheter som är tillgängliga, är nya jobb schemalagda att köras direkt till det totala antalet jobb som körs uppnår värdet för den här principen. När du når det maximala antalet jobb som kan köras samtidigt kan köas efterföljande jobb i prioritetsordning tills en eller flera jobb som körs har slutförts (beroende på tillgänglighet i Australien).

Ändra antalet jobb som kan köras samtidigt:

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **maximalt antal av kör jobb**, flytta skjutreglaget för att välja ett värde eller ange värdet i textrutan. 
4. Klicka på **Spara**.

> [!NOTE]
> Om du behöver köra mer än (20) standardantalet jobb i portalen klickar du på **hjälp + Support** att skicka en supportförfrågan. Antalet jobb som kan köras samtidigt i ditt Data Lake Analytics-konto kan ökas.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Hur lång tid att hålla jobbmetadata och resurser 
När användarna kör U-SQL-jobb, behåller alla relaterade filer i Data Lake Analytics-tjänsten. Relaterade filer inkluderar U-SQL-skript, DLL-filer som refereras i U-SQL-skript, kompilerade resurser och statistik. Filerna som finns i mappen/System / i Azure Data Lake Storage-kontot. Den här principen styr hur länge resurserna ska sparas innan de tas automatiskt bort (standardvärdet är 30 dagar). Du kan använda dessa filer för felsökning och för prestandajustering för jobb som du ska köra igen i framtiden.

Ändra hur lång tid för att behålla jobbmetadata och resurser:

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **dagar att behålla Jobbfrågor**, flytta skjutreglaget för att välja ett värde eller ange värdet i textrutan.  
4. Klicka på **Spara**.

## <a name="job-level-policies"></a>Jobbnivå principer

Du kan styra de maximala AU: er och den högsta prioritet som enskilda användare (eller medlemmar i specifika säkerhetsgrupper) kan ställa in på jobb som de skickar med jobbnivå principer. Den här principen kan du styra kostnader användare. Du kan även kontroll att schemalagda jobb kan ha på hög prioritet produktionsjobb som körs i samma Data Lake Analytics-konto.

Data Lake Analytics har två principer som du kan ange på jobbnivån:

* **AU-gräns per jobb**: användare kan bara skicka jobb som kan ha upp till det här antalet AU. Som standard är den här gränsen samma som den maximala AU-gränsen för kontot.
* **Prioritet**: användare kan bara skicka jobb som har en prioritet lägre än eller lika med det här värdet. En hög siffra anger lägre prioritet. Den här gränsen är som standard till 1, vilket är den högsta möjliga prioriteten.

Det finns en standardprincip som på varje konto. Standardprincipen gäller för alla användare på kontot. Du kan ange ytterligare principer för specifika användare och grupper. 

> [!NOTE]
> Kontonivå och jobbnivå principer gäller samtidigt.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Lägg till en princip för en viss användare eller grupp

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **begränsningar för Jobbinlämning**, klickar du på den **Lägg till princip** knappen. Sedan, Välj eller ange följande inställningar:
    1. **Compute principnamn**: Ange ett principnamn, som påminner dig om syftet med principen.
    2. **Välj användare eller grupp**: Välj den användare eller grupper som principen gäller.
    3. **Ställ in gräns för Australien i jobbet**: Ange AU-gräns som gäller för den valda användare eller grupp.
    4. **Ställ in gräns för prioritet**: Ställ in gräns för den priority som gäller för den valda användare eller grupp.

4. Klicka på **OK**.

5. Den nya principen visas i den **standard** princip tabellen under **begränsningar för Jobbinlämning**. 

### <a name="delete-or-edit-an-existing-policy"></a>Ta bort eller redigera en befintlig princip

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **begränsningar för Jobbinlämning**, hitta den princip som du vill redigera.
4.  Se den **ta bort** och **redigera** i kolumnen längst till höger i tabellen, klicka på `...`.

## <a name="additional-resources-for-job-policies"></a>Ytterligare resurser för principer för jobbet
* [Blogginlägg för princip-översikt](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Principer för kontonivå blogginlägget](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Principer jobb på blogginlägget](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure portal](data-lake-analytics-get-started-portal.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

