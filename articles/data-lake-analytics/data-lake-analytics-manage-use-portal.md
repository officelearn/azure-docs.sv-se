---
title: Hantera Azure Data Lake Analytics med hjälp av Azure-portalen
description: Den här artikeln beskriver hur du använder Azure-portalen för att hantera Data Lake Analytics-konton, datakällor, användare och jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 1d49403ec04b2ec35291869385c316cb5ab3b0da
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43044917"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Hantera Azure Data Lake Analytics med hjälp av Azure portal
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Den här artikeln beskriver hur du hanterar Azure Data Lake Analytics-konton, datakällor, användare och jobb med hjälp av Azure portal.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Hantera Data Lake Analytics-konton

### <a name="create-an-account"></a>Skapa ett konto

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **skapa en resurs** > **information + analys** > **Datasjöanalys**.
3. Välj värden för följande objekt: 
   1. **Namn på**: namnet på Data Lake Analytics-kontot.
   2. **Prenumeration**: Azure-prenumeration som används för kontot.
   3. **Resursgrupp**: Azure-resursgrupp där du vill skapa kontot. 
   4. **Plats**: Azure-datacenter för Data Lake Analytics-kontot. 
   5. **Data Lake Store**: standardlagringsplatsen som ska användas för Data Lake Analytics-kontot. Azure Data Lake Store-konto och Data Lake Analytics-konto måste vara på samma plats.
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

Du kan använda Datautforskaren för att söka efter datakällor och utföra grundläggande hanteringsåtgärder. 

### <a name="add-a-data-source"></a>Lägg till en datakälla

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **datakällor**.
3. Klicka på **Lägg till datakälla**.
    
   * Om du vill lägga till ett Data Lake Store-konto, behöver du kontonamn och åtkomst till kontot för att kunna köra frågor till dessa.
   * Om du vill lägga till Azure Blob storage, behöver du storage-konto och kontonyckeln. Du hittar dem genom att gå till storage-konto i portalen.

## <a name="set-up-firewall-rules"></a>Konfigurera brandväggsregler

Du kan använda Data Lake Analytics att ytterligare låsa åtkomsten till ditt Data Lake Analytics-konto på nätverksnivå. Du kan aktivera en brandvägg, ange en IP-adress eller definiera ett IP-adressintervall för dina betrodda klienter. När du har aktiverat dessa åtgärder kan endast klienter som har IP-adresser inom det angivna intervallet ansluta till arkivet.

Om andra Azure-tjänster som Azure Data Factory eller virtuella datorer kan ansluta till Data Lake Analytics-konto, se till att **Tillåt Azure Services** stängs **på**. 

### <a name="set-up-a-firewall-rule"></a>Konfigurera en brandväggsregel

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. På menyn till vänster, klickar du på **brandväggen**.

## <a name="add-a-new-user"></a>Lägg till en ny användare

Du kan använda den **guiden Lägg till användare** att etablera nya Data Lake-användare.

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Till vänster under **komma igång**, klickar du på **guiden Lägg till användare**.
3. Välj en användare och klicka sedan på **Välj**.
4. Välj en roll och klicka sedan på **Välj**. Om du vill konfigurera nya utvecklare att använda Azure Data Lake, Välj den **Data Lake Analytics-utvecklare** roll.
5. Välj åtkomstkontrollistor (ACL) för U-SQL-databaser. När du är nöjd med dina val klickar du på **Välj**.
6. Välj ACL: er för filer. För standard-store inte ändrar ACL: er för rotmappen ”/” och för mappen/System. Klicka på **Välj**.
7. Granska dina valda ändringar och klicka sedan på **kör**.
8. När guiden har slutförts klickar du på **klar**.

## <a name="manage-role-based-access-control"></a>Hantera rollbaserad åtkomstkontroll

Liksom andra Azure-tjänster, kan du använda rollbaserad åtkomstkontroll (RBAC) för att styra hur användarna samverkar med tjänsten.

Standard RBAC-roller har följande funktioner:
* **Ägare**: kan skicka jobb, övervaka jobb, avbryta jobb från alla användare och konfigurera kontot.
* **Deltagare**: kan skicka jobb, övervaka jobb, avbryta jobb från alla användare och konfigurera kontot.
* **Läsare**: övervaka jobb.

Aktivera U-SQL-utvecklare kan använda Data Lake Analytics-tjänsten med hjälp av rollen Data Lake Analytics-utvecklare. Du kan använda rollen Data Lake Analytics-utvecklare för att:
* Skicka jobb.
* Övervakare för jobbets status och förlopp för jobb som skickats av någon användare.
* Se U-SQL-skript från jobb som skickats av någon användare.
* Avbryt endast dina egna jobb.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Lägg till användare eller säkerhetsgrupper i ett Data Lake Analytics-konto

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **åtkomstkontroll (IAM)** > **Lägg till**.
3. Välj en roll.
4. Lägga till en användare.
5. Klicka på **OK**.

>[!NOTE]
>Om en användare eller en grupp måste du skicka jobb, behöver de också behörighet för store-kontot. Mer information finns i [skyddar data som lagras i Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Hantera jobb

### <a name="submit-a-job"></a>Skicka ett jobb

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.

2. Klicka på **nytt jobb**. Konfigurera för varje jobb:

    1. **Jobbnamnet**: namnet på jobbet.
    2. **Prioritet**: låga tal har högre prioritet. Om två jobb i kö, körs som har lägre prioritetsvärde första.
    3. **Parallellitet**: maximalt antal beräkningsprocesser att reservera för det här jobbet.

3. Klicka på **Skicka jobb**.

### <a name="monitor-jobs"></a>Övervaka jobb

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **visa alla jobb**. En lista över alla aktiva och nyligen klar jobb i kontot visas.
3. Du kan också klicka på **Filter** för att hitta jobb efter **tidsintervall**, **jobbnamn**, och **författare** värden. 

### <a name="monitoring-pipeline-jobs"></a>Övervaka pipeline-jobb
Jobb som ingår i en pipeline fungerar tillsammans, vanligtvis sekventiellt för att utföra ett specifikt scenario. Du kan till exempel ha en pipeline som rensar, extraherar, omvandlar, aggregerar användning för customer insights. Pipeline-jobb identifieras med hjälp av egenskapen ”Pipeline” när jobbet har skickats. Jobb som schemalagts med ADF V2 har den här egenskapen som fylls i automatiskt. 

Visa en lista över U-SQL-jobb som ingår i pipelines: 

1. Gå till ditt Data Lake Analytics-konton i Azure-portalen.
2. Klicka på **jobbet Insights**. Fliken ”alla jobb” blir som standard, som visar en lista över körs, i kö och avslutade jobb.
3. Klicka på den **Pipeline-jobb** fliken. En lista över pipeline-jobb visas tillsammans med statistik för varje pipeline.

### <a name="monitoring-recurring-jobs"></a>Övervaka återkommande jobb
Ett återkommande jobb är en som har samma affärslogik men använder olika indata varje gång den körs. Vi rekommenderar bör återkommande jobb alltid lyckas och har relativt stabil körningstid; övervakning av dessa beteenden kan se till att jobbet är felfri. Återkommande jobb identifieras med hjälp av egenskapen ”återkommande”. Jobb som schemalagts med ADF V2 har den här egenskapen som fylls i automatiskt.

Visa en lista över U-SQL-jobb som är återkommande: 

1. Gå till ditt Data Lake Analytics-konton i Azure-portalen.
2. Klicka på **jobbet Insights**. Fliken ”alla jobb” blir som standard, som visar en lista över körs, i kö och avslutade jobb.
3. Klicka på den **återkommande jobb** fliken. En lista över återkommande jobb visas tillsammans med statistik för varje återkommande jobb.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Hantera Azure Data Lake Analytics med hjälp av principer](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-policies)
