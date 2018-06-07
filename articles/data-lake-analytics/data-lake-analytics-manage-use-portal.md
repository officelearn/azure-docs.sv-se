---
title: Hantera Azure Data Lake Analytics med hjälp av Azure portal
description: Den här artikeln beskriver hur du använder Azure-portalen för att hantera Data Lake Analytics-konton, datakällor, användare och jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 1ccd4dd6b8d4ee15b7d9f14e7436ccd87392121e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623714"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Hantera Azure Data Lake Analytics med hjälp av Azure portal
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Den här artikeln beskriver hur du hanterar Azure Data Lake Analytics-konton, datakällor, användare och jobb med hjälp av Azure portal.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Hantera Data Lake Analytics-konton

### <a name="create-an-account"></a>Skapa ett konto

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **skapar du en resurs** > **Intelligence + analys** > **Datasjöanalys**.
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

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Hantera Azure Data Lake Analytics med hjälp av principer](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-policies)
