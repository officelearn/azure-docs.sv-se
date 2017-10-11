---
title: "Hantera Azure Data Lake Analytics med hjälp av Azure portal | Microsoft Docs"
description: "Lär dig hur du hanterar Data Lake Analytics räkenskaper, datakällor, användare och jobb."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: e49d1a0e0ccc6567d0a6841817667717ff5dba76
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="manage-azure-data-lake-analytics-by-using-the-azure-portal"></a>Hantera Azure Data Lake Analytics med hjälp av Azure portal
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Lär dig mer om att hantera Azure Data Lake Analytics-konton, konto datakällor, användare och jobb med hjälp av Azure-portalen. Om du vill se avsnitt om om hur du använder andra verktyg klickar du på en flik överst på sidan.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Hantera Data Lake Analytics-konton

### <a name="create-an-account"></a>Skapa ett konto

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **Nytt** > **Intelligence + analytics** > **Data Lake Analysis**.
3. Välj värden för följande objekt: 
   1. **Namnet**: namnet på Data Lake Analytics-konto.
   2. **Prenumerationen**: Azure-prenumeration som används för kontot.
   3. **Resursgruppen**: Azure-resursgrupp att skapa kontot. 
   4. **Plats**: Azure-datacenter för Data Lake Analytics-kontot. 
   5. **Data Lake Store**: standardlagringsplatsen som ska användas för Data Lake Analytics-kontot. Azure Data Lake Store-konto och Data Lake Analytics-kontot måste vara på samma plats.
4. Klicka på **Skapa**. 

### <a name="delete-a-data-lake-analytics-account"></a>Ta bort ett Data Lake Analytics-konto

Innan du tar bort ett Data Lake Analytics-konto kan du ta bort dess Data Lake Store-standardkontot.

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Ta bort**.
3. Skriv namnet på kontot.
4. Klicka på **Ta bort**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Hantera datakällor

Data Lake Analytics stöder följande datakällor:

* Data Lake Store
* Azure Storage

Du kan använda Data Explorer för att bläddra igenom datakällor och utföra grundläggande filhanteringsåtgärder. 

### <a name="add-a-data-source"></a>Lägga till en datakälla

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **datakällor**.
3. Klicka på **lägga till en datakälla**.
    
   * Om du vill lägga till ett Data Lake Store-konto, behöver du kontonamnet och åtkomst till kontot för att kunna läsa den.
   * Om du vill lägga till Azure Blob storage, behöver du lagringskontot och nyckeln för kontot. Du hittar dem genom att gå till lagringskontot på portalen.

## <a name="set-up-firewall-rules"></a>Konfigurera brandväggsregler

Du kan använda Data Lake Analytics att ytterligare låsa åtkomst till ditt Data Lake Analytics-konto på nätverksnivån. Du kan aktivera en brandvägg, ange en IP-adress eller definiera ett intervall med IP-adresser för dina betrodda klienter. När du har aktiverat dessa åtgärder kan endast klienter som har IP-adresser inom det angivna intervallet ansluta till arkivet.

Om andra Azure-tjänster, t.ex. Azure Data Factory eller virtuella datorer kan ansluta till Data Lake Analytics-kontot, kontrollerar du att **Tillåt Azure Services** är aktiverat **på**. 

### <a name="set-up-a-firewall-rule"></a>Konfigurera en brandväggsregel

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. På menyn till vänster **brandväggen**.

## <a name="add-a-new-user"></a>Lägg till en ny användare

Du kan använda den **guiden Lägg till användare** att enkelt etablera nya Data Lake-användare.

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Till vänster under **komma igång**, klickar du på **guiden Lägg till användare**.
3. Välj en användare och klicka sedan på **Välj**.
4. Välj en roll och klicka sedan på **Välj**. Skapa en ny utvecklare att använda Azure Data Lake, välja den **Data Lake Analytics Developer** roll.
5. Välj åtkomstkontrollistor (ACL) för U-SQL-databaser. När du är nöjd med dina val klickar du på **Välj**.
6. Välj ACL: er för filer. Inte ändra ACL: er för rotmappen för arkivet standard ”/” och för mappen/System. Klicka på **Välj**.
7. Granska alla valda ändringarna och klicka sedan på **kör**.
8. När guiden är klar klickar du på **klar**.

## <a name="manage-role-based-access-control"></a>Hantera rollbaserad åtkomstkontroll

Du kan använda rollbaserad åtkomstkontroll (RBAC) som andra Azure-tjänster för att styra hur användarna samverkar med tjänsten.

Standard RBAC-roller har följande möjligheter:
* **Ägare**: kan skicka jobb, övervaka jobb, avbryta jobb från alla användare och konfigurera kontot.
* **Deltagare**: kan skicka jobb, övervaka jobb, avbryta jobb från alla användare och konfigurera kontot.
* **Läsaren**: övervaka jobb.

Använd Data Lake Analytics Developer-rollen för att aktivera U-SQL-utvecklare kan använda Data Lake Analytics-tjänsten. Du kan använda rolltjänsten Data Lake Analytics utvecklare att:
* Skicka jobb.
* Övervaka jobbstatus och förlopp för jobb som skickats av någon användare.
* Se U-SQL-skript från jobb som skickats av någon användare.
* Avbryt endast egna jobb.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Lägga till användare eller säkerhetsgrupper till ett Data Lake Analytics-konto

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **åtkomstkontroll (IAM)** > **Lägg till**.
3. Välj en roll.
4. Lägga till en användare.
5. Klicka på **OK**.

>[!NOTE]
>Om en användare eller en säkerhetsgrupp måste du skicka jobb, måste de också behörighet till store-konto. Mer information finns i [skyddar data som lagras i Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Hantera jobb

### <a name="submit-a-job"></a>Skicka ett jobb

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.

2. Klicka på **nytt jobb**. Konfigurera följande för varje jobb:

    1. **Jobbnamnet**: namnet på jobbet.
    2. **Prioritet**: lägre nummer har högre prioritet. Om två jobb i kö, körs med lägre prioritetsvärde första.
    3. **Parallellitet**: det maximala antalet beräknings-processer att reservera för det här jobbet.

3. Klicka på **Skicka jobb**.

### <a name="monitor-jobs"></a>Övervaka jobb

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **visa alla jobb**. En lista över alla aktiva och nyligen klar jobb i kontot visas.
3. Du kan också klicka på **Filter** för att hitta jobb efter **tidsintervall**, **jobbnamn**, och **författare** värden. 

### <a name="monitoring-pipeline-jobs"></a>Övervaka pipeline-jobb
Jobb som är en del av en pipeline fungerar tillsammans, vanligtvis sekventiellt för att uppnå ett specifikt scenario. Du kan till exempel ha en pipeline som rensar, extraherar, omvandlar, aggregerar användning för kunden insikter. Pipeline-jobb identifieras med hjälp av egenskapen ”Pipeline” när jobbet har skickats. Jobb som schemalagts med ADF V2 har automatiskt den här egenskapen fylls i. 

Visa en lista över U-SQL-jobb som är del av pipelines: 

1. Gå till din Data Lake Analytics-konton i Azure-portalen.
2. Klicka på **jobbet insikter**. På fliken ”alla jobb” blir som standard, visar en lista över körs, i kö och jobb avslutades.
3. Klicka på den **Pipeline jobb** fliken. En lista över pipeline jobb visas tillsammans med statistik för varje pipelinen.

### <a name="monitoring-recurring-jobs"></a>Övervaka återkommande jobb
Ett återkommande jobb är en som har samma affärslogik men använder olika indata varje gång det körs. Vi rekommenderar bör återkommande jobb alltid lyckas och har stabilt körningstid; övervakning av dessa beteenden säkerställer jobbet är felfri. Återkommande jobb identifieras med hjälp av egenskapen ”återkommande”. Jobb som schemalagts med ADF V2 har automatiskt den här egenskapen fylls i.

Visa en lista över U-SQL-jobb som är återkommande: 

1. Gå till din Data Lake Analytics-konton i Azure-portalen.
2. Klicka på **jobbet insikter**. På fliken ”alla jobb” blir som standard, visar en lista över körs, i kö och jobb avslutades.
3. Klicka på den **återkommande jobb** fliken. En lista över återkommande jobb visas tillsammans med statistik för alla återkommande jobb.

## <a name="manage-policies"></a>Hantera principer

### <a name="account-level-policies"></a>Kontonivå principer

Dessa principer som gäller för alla jobb i ett Data Lake Analytics-konto.

#### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maximalt antal Australien i ett Data Lake Analytics-konto
En princip styr det totala antalet Analytics-enheter (Australien) kan använda ditt Data Lake Analytics-konto. Värdet är som standard är 250. Om det här värdet är inställt på 250 exempelvis Australien, kan du ha ett jobb som körs med 250 Australien tilldelade eller 10 jobb som körs med 25 Australien varje. Ytterligare jobb som skickats köas förrän jobb som körs är klar. När jobb som körs är slutförda frigörs Australien för att köra köade jobb.

Ändra antalet Australien för ditt Data Lake Analytics-konto:

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **maximala Australien**, flytta skjutreglaget för att välja ett värde eller ange värdet i textrutan. 
4. Klicka på **Spara**.

> [!NOTE]
> Om du behöver mer än standardvärdet (250) Australien, i portalen klickar du på **hjälp + Support** att skicka en supportförfrågan. Antalet Australien som är tillgängliga i ditt Data Lake Analytics-konto kan du öka.
>

#### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maximalt antal jobb som kan köras samtidigt
En princip styr hur många jobb kan köras samtidigt. Det här värdet är som standard till 20. Om Data Lake Analytics har Australien som är tillgängliga, är nya jobb schemalagda att köras omedelbart tills det totala antalet jobb som körs värdet för den här principen. När du når det maximala antalet jobb som kan köras samtidigt efterföljande jobb ställs i kö i prioritetsordning tills en eller flera av de jobb som körs är klar (beroende på Australien tillgänglighet).

Ändra antalet jobb som kan köras samtidigt:

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **maximala antalet av kör jobb**, flytta skjutreglaget för att välja ett värde eller ange värdet i textrutan. 
4. Klicka på **Spara**.

> [!NOTE]
> Om du behöver köra fler än antalet jobb, standard (20) i portalen klickar du på **hjälp + Support** att skicka en supportförfrågan. Antalet jobb som kan köras samtidigt i ditt Data Lake Analytics-konto kan du öka.
>

#### <a name="how-long-to-keep-job-metadata-and-resources"></a>Hur lång tid att behålla jobbet metadata och resurser 
När användarna kör U-SQL-jobb, behåller alla relaterade filer i Data Lake Analytics-tjänsten. Relaterade filer innehåller U-SQL-skript, DLL-filer som refereras i U-SQL-skript, kompilerade resurser och statistik. Filerna som finns i mappen /system/ i Azure Data Lake-standardlagringskontot. Den här principen styr hur länge dessa resurser lagras innan de tas bort automatiskt (standardvärdet är 30 dagar). Du kan använda dessa filer för felsökning och prestandajustering av jobb som du ska köra i framtiden.

Ändra hur lång tid för att behålla jobbet metadata och resurser:

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **dagar för att behålla Jobbfrågor**, flytta skjutreglaget för att välja ett värde eller ange värdet i textrutan.  
4. Klicka på **Spara**.

### <a name="job-level-policies"></a>Nivå principer
Du kan styra de maximala Australien och maximal prioritet som enskilda användare (eller medlemmar i specifika säkerhetsgrupper) kan ställa in för jobb som de skickar med principer för jobbet på objektnivå. På så sätt kan du styra kostnader användare. Du kan också kontrollera att schemalagda jobb kan ha på hög prioritet produktionsjobb som körs i samma Data Lake Analytics-konto.

Data Lake Analytics har två principer som du kan ange på jobbnivån:

* **Australien gränsen per jobb**: användare kan bara skicka jobb som behöver det här antalet Australien. Som standard är den här gränsen detsamma som Australien maxgränsen för kontot.
* **Prioritet**: användare kan bara skicka jobb som har en prioritet lägre än eller lika med det här värdet. Observera att ett högre värde innebär en lägre prioritet. Detta är som standard till 1, vilket är den högsta möjliga prioriteten.

Det finns en standardprincip som anges för varje konto. Standardprincipen gäller för alla användare av kontot. Du kan ange ytterligare principer för specifika användare och grupper. 

> [!NOTE]
> Kontonivå och nivå principer tillämpas samtidigt.
>

#### <a name="add-a-policy-for-a-specific-user-or-group"></a>Lägg till en princip för en specifik användare eller grupp

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **jobbet skicka gränser**, klicka på den **Lägg till princip** knappen. Sedan, Välj eller ange följande inställningar:
    1. **Beräkna principnamn**: Ange ett principnamn, för att påminna dig om syftet med principen.
    2. **Välj användare eller grupp**: Välj den användare eller grupp som den här principen gäller för.
    3. **Ställ in gräns för jobbet Australien**: Ställ in gräns för automatiska uppdateringar som gäller för den markerade användaren eller gruppen.
    4. **Ställ in gräns för prioritet**: Ställ in gräns för prioritet som gäller för den markerade användaren eller gruppen.

4. Klicka på **OK**.

5. Den nya principen visas i den **standard** princip tabell under **jobbet skicka gränser**. 

#### <a name="delete-or-edit-an-existing-policy"></a>Ta bort eller redigera en befintlig princip

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Egenskaper**.
3. Under **jobbet skicka gränser**, hitta principen du vill redigera.
4.  Se den **ta bort** och **redigera** , i kolumnen längst till höger i tabellen, klicka på **...** .

### <a name="additional-resources-for-job-policies"></a>Ytterligare resurser för jobbet principer
* [Blogginlägget för principen: översikt](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Principer för kontonivå blogginlägget](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Principer jobb på blogginlägget](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure portal](data-lake-analytics-get-started-portal.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

