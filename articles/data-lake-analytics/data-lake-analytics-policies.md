---
title: Hantera Azure Data Lake Analytics-principer
description: Lär dig hur du använder principer för att styra användningen av ett Data Lake Analytics-konto.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 2c074b1a75c5bfef07ffb90e719bd3247a474e63
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623952"
---
# <a name="manage-azure-data-lake-analytics-using-policies"></a>Hantera Azure Data Lake Analytics med hjälp av principer

Med Kontoprinciper kan du kontrollera hur ett Azure Data Lake Analytics-konto finns resurser används. Dessa principer kan du kontrollera kostnaden för att använda Azure Data Lake Analytics. Med dessa principer kan du hindra oväntad kostnad toppar genom att begränsa hur många Australien kontot samtidigt kan använda.

## <a name="account-level-policies"></a>Kontonivå principer

Dessa principer som gäller för alla jobb i ett Data Lake Analytics-konto.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maximalt antal Australien i ett Data Lake Analytics-konto
En princip styr det totala antalet Analytics-enheter (Australien) kan använda ditt Data Lake Analytics-konto. Värdet är som standard är 250. Om det här värdet är inställt på 250 exempelvis Australien, kan du ha ett jobb som körs med 250 Australien tilldelade eller 10 jobb som körs med 25 Australien varje. Ytterligare jobb som skickats köas förrän jobb som körs är klar. När jobb som körs är slutförda frigörs Australien för att köra köade jobb.

Ändra antalet Australien för ditt Data Lake Analytics-konto:

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **maximala Australien**, flytta skjutreglaget för att välja ett värde eller ange värdet i textrutan. 
4. Klicka på **Spara**.

> [!NOTE]
> Om du behöver mer än standardvärdet (250) Australien, i portalen klickar du på **hjälp + Support** att skicka en supportförfrågan. Antalet Australien som är tillgängliga i ditt Data Lake Analytics-konto kan du öka.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maximalt antal jobb som kan köras samtidigt
En princip styr hur många jobb kan köras samtidigt. Det här värdet är som standard till 20. Om Data Lake Analytics har Australien som är tillgängliga, är nya jobb schemalagda att köras omedelbart tills det totala antalet jobb som körs värdet för den här principen. När du når det maximala antalet jobb som kan köras samtidigt efterföljande jobb ställs i kö i prioritetsordning tills en eller flera av de jobb som körs är klar (beroende på Australien tillgänglighet).

Ändra antalet jobb som kan köras samtidigt:

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **maximala antalet av kör jobb**, flytta skjutreglaget för att välja ett värde eller ange värdet i textrutan. 
4. Klicka på **Spara**.

> [!NOTE]
> Om du behöver köra fler än antalet jobb, standard (20) i portalen klickar du på **hjälp + Support** att skicka en supportförfrågan. Antalet jobb som kan köras samtidigt i ditt Data Lake Analytics-konto kan du öka.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Hur lång tid att behålla jobbet metadata och resurser 
När användarna kör U-SQL-jobb, behåller alla relaterade filer i Data Lake Analytics-tjänsten. Relaterade filer innehåller U-SQL-skript, DLL-filer som refereras i U-SQL-skript, kompilerade resurser och statistik. Filerna som finns i mappen /system/ i Azure Data Lake-standardlagringskontot. Den här principen styr hur länge dessa resurser lagras innan de tas bort automatiskt (standardvärdet är 30 dagar). Du kan använda dessa filer för felsökning och prestandajustering av jobb som du ska köra i framtiden.

Ändra hur lång tid för att behålla jobbet metadata och resurser:

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **dagar för att behålla Jobbfrågor**, flytta skjutreglaget för att välja ett värde eller ange värdet i textrutan.  
4. Klicka på **Spara**.

## <a name="job-level-policies"></a>Nivå principer

Du kan styra de maximala Australien och maximal prioritet som enskilda användare (eller medlemmar i specifika säkerhetsgrupper) kan ställa in för jobb som de skickar med principer för jobbet på objektnivå. Den här principen kan du styra kostnader användare. Du kan också kontrollera att schemalagda jobb kan ha på hög prioritet produktionsjobb som körs i samma Data Lake Analytics-konto.

Data Lake Analytics har två principer som du kan ange på jobbnivån:

* **Australien gränsen per jobb**: användare kan bara skicka jobb som behöver det här antalet Australien. Som standard är den här gränsen detsamma som Australien maxgränsen för kontot.
* **Prioritet**: användare kan bara skicka jobb som har en prioritet lägre än eller lika med det här värdet. En hög siffra anger en lägre prioritet. Den här gränsen är som standard till 1, vilket är den högsta möjliga prioriteten.

Det finns en standardprincip som anges för varje konto. Standardprincipen gäller för alla användare av kontot. Du kan ange ytterligare principer för specifika användare och grupper. 

> [!NOTE]
> Kontonivå och nivå principer tillämpas samtidigt.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Lägg till en princip för en specifik användare eller grupp

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **jobbet skicka gränser**, klicka på den **Lägg till princip** knappen. Sedan, Välj eller ange följande inställningar:
    1. **Beräkna principnamn**: Ange ett principnamn, för att påminna dig om syftet med principen.
    2. **Välj användare eller grupp**: Välj den användare eller grupp som den här principen gäller för.
    3. **Ställ in gräns för jobbet Australien**: Ställ in gräns för automatiska uppdateringar som gäller för den markerade användaren eller gruppen.
    4. **Ställ in gräns för prioritet**: Ställ in gräns för prioritet som gäller för den markerade användaren eller gruppen.

4. Klicka på **OK**.

5. Den nya principen visas i den **standard** princip tabell under **jobbet skicka gränser**. 

### <a name="delete-or-edit-an-existing-policy"></a>Ta bort eller redigera en befintlig princip

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **jobbet skicka gränser**, hitta principen du vill redigera.
4.  Se den **ta bort** och **redigera** , i kolumnen längst till höger i tabellen, klicka på `...`.

## <a name="additional-resources-for-job-policies"></a>Ytterligare resurser för jobbet principer
* [Blogginlägget för principen: översikt](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Principer för kontonivå blogginlägget](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Principer jobb på blogginlägget](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure portal](data-lake-analytics-get-started-portal.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

