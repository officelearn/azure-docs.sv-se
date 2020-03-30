---
title: Hantera kontoprinciper för Azure Data Lake Analytics
description: Lär dig hur du använder kontoprinciper för att styra användningen av ett DataSjöanalyskonto, till exempel maximal AUs och maximal jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 4689714073047e383a53a04bd0069a8a27afdf9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72966442"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Hantera Azure Data Lake Analytics med hjälp av kontoprinciper

Kontoprinciper hjälper dig att styra hur resurser ett Azure Data Lake Analytics-konto används. Med dessa principer kan du styra kostnaden för att använda Azure Data Lake Analytics. Med dessa principer kan du till exempel förhindra oväntade kostnadstoppar genom att begränsa hur många ru:er som kontot kan använda samtidigt.

## <a name="account-level-policies"></a>Principer på kontonivå

Dessa principer gäller för alla jobb i ett DataSjöanalyskonto.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maximalt antal AUs i ett DataSjöanalyskonto
En princip styr det totala antalet Analytics-enheter som ditt Data Lake Analytics-konto kan använda. Som standard är värdet satt till 250. Om värdet till exempel är inställt på 250 AUs kan du ha ett jobb som körs med 250 AUs tilldelade till det, eller 10 jobb som körs med 25 AUs vardera. Ytterligare jobb som skickas köas tills jobben körs är klara. När jobb körs frigörs AUs för de köade jobben som ska köras.

Så här ändrar du antalet AUs för ditt DataSjöanalyskonto:

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Gränser och principer**.
3. Under **Maximalt AUs**flyttar du skjutreglaget för att markera ett värde eller anger värdet i textrutan. 
4. Klicka på **Spara**.

> [!NOTE]
> Om du behöver mer än standard-Aus (250) klickar du på **Hjälp+Support** i portalen för att skicka en supportbegäran. Antalet AUs som är tillgängliga i ditt DataSjöanalyskonto kan ökas.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maximalt antal jobb som kan köras samtidigt
Den här principen begränsar hur många jobb som kan köras samtidigt. Som standard är det här värdet 20. Om datasjöanalysen har tillgängliga AUs schemaläggs nya jobb att köras omedelbart tills det totala antalet jobb som körs når värdet för den här principen. När du når det maximala antalet jobb som kan köras samtidigt, köas efterföljande jobb i prioritetsordning tills ett eller flera jobb som körs har slutförts (beroende på tillgängliga ru: er).

Så här ändrar du antalet jobb som kan köras samtidigt:

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Gränser och principer**.
3. Under **Maximalt antal jobb som körs**flyttar du skjutreglaget för att markera ett värde eller anger värdet i textrutan. 
4. Klicka på **Spara**.

> [!NOTE]
> Om du behöver köra fler än standardantalet (20) jobb klickar du på **Hjälp+Support** i portalen för att skicka en supportbegäran. Antalet jobb som kan köras samtidigt i ditt Data Lake Analytics-konto kan ökas.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Hur lång tid det är att behålla metadata och resurser för jobb 
När användarna kör U-SQL-jobb behåller Data Lake Analytics-tjänsten alla relaterade filer. Dessa filer inkluderar U-SQL-skriptet, DLL-filerna som refereras i U-SQL-skriptet, kompilerade resurser och statistik. Filerna finns i mappen /system/i standardkontot för Azure Data Lake Storage. Den här principen styr hur länge dessa resurser lagras innan de tas bort automatiskt (standardvärdet är 30 dagar). Du kan använda dessa filer för felsökning och för prestandajustering av jobb som du ska köra igen i framtiden.

Så här ändrar du hur länge du behåller metadata och resurser för jobbet:

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Gränser och principer**.
3. Under **Dagar att behålla jobbfrågor**flyttar du skjutreglaget för att markera ett värde eller anger värdet i textrutan.  
4. Klicka på **Spara**.

## <a name="job-level-policies"></a>Principer på jobbnivå

Med principer på jobbnivå kan du styra de maximala AUs och den högsta prioritet som enskilda användare (eller medlemmar i specifika säkerhetsgrupper) kan ange för jobb som de skickar. Med den här principen kan du styra användarnas kostnader. Du kan också styra vilken effekt schemalagda jobb kan ha på produktionsjobb med hög prioritet som körs i samma DataSjöanalyskonto.

DataSjöanalys har två principer som du kan ange på jobbnivå:

* **AU-gräns per jobb**: Användare kan bara skicka jobb som har upp till detta antal AUs. Som standard är den här gränsen samma som den maximala AU-gränsen för kontot.
* **Prioritet**: Användare kan bara skicka jobb som har en prioritet som är lägre än eller lika med det här värdet. Ett högre tal anger en lägre prioritet. Som standard är den här gränsen inställd på 1, vilket är högsta möjliga prioritet.

Det finns en standardprincipuppsättning för varje konto. Standardprincipen gäller för alla användare av kontot. Du kan skapa ytterligare principer för specifika användare och grupper. 

> [!NOTE]
> Principer på kontonivå och principer på jobbnivå gäller samtidigt.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Lägga till en princip för en viss användare eller grupp

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Gränser och principer**.
3. Klicka på knappen **Lägg till princip** under Begränsningar för **jobböverföring.** Välj eller ange sedan följande inställningar:
    1. **Beräkningsprincipnamn:** Ange ett principnamn för att påminna dig om syftet med principen.
    2. **Välj Användare eller Grupp**: Välj den användare eller grupp som den här principen gäller för.
    3. **Ange jobb-AU-gränsen**: Ange au-gränsen som gäller för den valda användaren eller gruppen.
    4. **Ange prioritetsgränsen:** Ange den prioritetsgräns som gäller för den valda användaren eller gruppen.

4. Klicka på **OK**.

5. Den nya principen visas i tabellen **Standardprincip,** under **Gränser för jobbinlämning**. 

### <a name="delete-or-edit-an-existing-policy"></a>Ta bort eller redigera en befintlig princip

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Gränser och principer**.
3. Leta reda på den princip som du vill redigera under Gränser för **jobbinlämning.**
4.  Klicka på i kolumnen Längst till höger i tabellen `...`om du vill visa alternativen **Ta bort** och **redigera.**

## <a name="additional-resources-for-job-policies"></a>Ytterligare resurser för jobbprinciper
* [Blogginlägg för policyöversikt](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Blogginlägg om principer på kontonivå](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Blogginlägg om policyer på jobbnivå](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Komma igång med Data Lake Analytics med hjälp av Azure-portalen](data-lake-analytics-get-started-portal.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)