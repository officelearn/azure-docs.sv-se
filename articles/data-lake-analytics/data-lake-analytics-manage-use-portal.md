---
title: Hantera Azure Data Lake Analytics med hjälp av Azure-portalen
description: I den här artikeln beskrivs hur du använder Azure-portalen för att hantera DataSjöanalyskonton, datakällor, användare & jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8a6b7cb3fd45e17b84519efcaa826b569083e156
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265706"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Hantera Azure Data Lake Analytics med hjälp av Azure-portalen
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

I den här artikeln beskrivs hur du hanterar Azure Data Lake Analytics-konton, datakällor, användare och jobb med hjälp av Azure-portalen.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Hantera Data Lake Analytics-konton

### <a name="create-an-account"></a>Skapa ett konto

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Skapa en resursinformation** > +**analysdatasjöanalys****Intelligence + analytics** > .
3. Välj värden för följande objekt: 
   1. **Namn**: Namnet på DataSjöanalyskontot.
   2. **Prenumeration**: Azure-prenumerationen som används för kontot.
   3. **Resursgrupp**: Azure-resursgruppen där kontot ska skapas. 
   4. **Plats**: Azure-datacentret för DataSjöanalyskontot. 
   5. **Datasjölagring:** Standardarkivet som ska användas för DataSjöanalyskontot. Azure Data Lake Store-kontot och DataSjöanalyskontot måste vara på samma plats.
4. Klicka på **Skapa**. 

### <a name="delete-a-data-lake-analytics-account"></a>Ta bort ett DataSjöanalyskonto

Innan du tar bort ett DataSjöanalyskonto tar du bort standardkontot för DataSjölagring.

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Ta bort**.
3. Skriv kontonamnet.
4. Klicka på **Ta bort**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Hantera datakällor

DataSjöanalys stöder följande datakällor:

* Data Lake Store
* Azure Storage

Du kan använda Data Explorer för att bläddra bland datakällor och utföra grundläggande filhanteringsåtgärder. 

### <a name="add-a-data-source"></a>Lägga till en datakälla

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Datakällor**.
3. Klicka på **Lägg till datakälla**.
    
   * Om du vill lägga till ett DataSjölagringskonto behöver du kontonamnet och åtkomsten till kontot för att kunna fråga det.
   * Om du vill lägga till Azure Blob-lagring behöver du lagringskontot och kontonyckeln. Om du vill hitta dem går du till lagringskontot i portalen.

## <a name="set-up-firewall-rules"></a>Ställa in brandväggsregler

Du kan använda Data Lake Analytics för att ytterligare låsa åtkomsten till ditt Data Lake Analytics-konto på nätverksnivå. Du kan aktivera en brandvägg, ange en IP-adress eller definiera ett IP-adressintervall för dina betrodda klienter. När du har aktiverat dessa mått kan endast klienter som har IP-adresserna inom det definierade intervallet ansluta till arkivet.

Om andra Azure-tjänster, till exempel Azure Data Factory eller virtuella datorer, ansluter till DataSjöanalyskontot kontrollerar du att **Tillåt Azure Services** är aktiverat . **On** 

### <a name="set-up-a-firewall-rule"></a>Konfigurera en brandväggsregel

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Brandvägg**på menyn till vänster .

## <a name="add-a-new-user"></a>Lägga till en ny användare

Du kan använda **guiden Lägg till användare** för att enkelt etablera nya DataSjöanvändare.

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Lägg till användarguide under**Komma **igång**till vänster.
3. Markera en användare och klicka sedan på **Markera**.
4. Välj en roll och klicka sedan på **Välj**. Om du vill konfigurera en ny utvecklare för att använda Azure Data Lake väljer du rollen **Utvecklare av DataSjöanalys.**
5. Välj åtkomstkontrollistorna (ACL: er) för U-SQL-databaserna. När du är nöjd med dina val klickar du på **Välj**.
6. Välj ACL:er för filer. För standardarkivet ska du inte ändra åtkomstkontrollistorna för rotmappen "/" och för mappen /system. Klicka på **Markera**.
7. Granska alla markerade ändringar och klicka sedan på **Kör**.
8. När guiden är klar klickar du på **Klar**.

## <a name="manage-role-based-access-control"></a>Hantera rollbaserad åtkomstkontroll

Precis som andra Azure-tjänster kan du använda ROLLBASERAD åtkomstkontroll (RBAC) för att styra hur användare interagerar med tjänsten.

Standard-RBAC-rollerna har följande funktioner:
* **Ägare**: Kan skicka jobb, övervaka jobb, avbryta jobb från alla användare och konfigurera kontot.
* **Deltagare:** Kan skicka jobb, övervaka jobb, avbryta jobb från alla användare och konfigurera kontot.
* **Läsare:** Kan övervaka jobb.

Använd rollen Utvecklare av DataSjöanalys för att göra det möjligt för U-SQL-utvecklare att använda tjänsten Data Lake Analytics. Du kan använda rollen Utvecklare av DataSjöanalys för att:
* Skicka jobb.
* Övervaka jobbstatus och förloppet för jobb som skickas av alla användare.
* Se U-SQL-skript från jobb som skickas av alla användare.
* Avbryt bara dina egna jobb.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Lägga till användare eller säkerhetsgrupper i ett DataSjöanalyskonto

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Åtkomstkontroll (IAM)** > **Lägg till rolltilldelning**.
3. Välj en roll.
4. Lägg till en användare.
5. Klicka på **OK**.

>[!NOTE]
>Om en användare eller en säkerhetsgrupp behöver skicka jobb behöver de också behörighet för butikskontot. Mer information finns [i Säkra data som lagras i DataSjö store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Hantera jobb

### <a name="submit-a-job"></a>Skicka ett jobb

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.

2. Klicka på **Nytt jobb**. Konfigurera för varje jobb:

    1. **Jobbnamn**: Namnet på jobbet.
    2. **Prioritet**: Lägre tal har högre prioritet. Om två jobb köas körs det med lägre prioritetsvärde först.
    3. **Parallellism**: Det maximala antalet beräkningsprocesser som ska reserveras för det här jobbet.

3. Klicka på **Skicka jobb**.

### <a name="monitor-jobs"></a>Övervaka jobb

1. Gå till ditt Data Lake Analytics-konto i Azure-portalen.
2. Klicka på **Visa alla jobb**. En lista över alla aktiva och nyligen avslutade jobb i kontot visas.
3. Du kan också klicka på **Filter** för att hjälpa dig att hitta jobben efter **tidsintervall,** **jobbnamn**och **författarvärden.** 

### <a name="monitoring-pipeline-jobs"></a>Övervakning av pipeline-jobb
Jobb som ingår i en pipeline arbetar tillsammans, vanligtvis sekventiellt, för att utföra ett visst scenario. Du kan till exempel ha en pipeline som rensar, extraherar, transformerar, aggregatanvändning för kundinsikter. Pipeline-jobb identifieras med egenskapen "Pipeline" när jobbet skickades. Jobb som schemalagts med ADF V2 har automatiskt den här egenskapen ifylld. 

Så här visar du en lista över U-SQL-jobb som ingår i pipelines: 

1. Gå till dina Data Lake Analytics-konton i Azure-portalen.
2. Klicka på **Jobbstatistik**. Fliken "Alla jobb" är standard, som visar en lista över jobb som körs, köas och avslutas.
3. Klicka på fliken **Pipeline-jobb.** En lista över pipeline-jobb visas tillsammans med aggregerad statistik för varje pipeline.

### <a name="monitoring-recurring-jobs"></a>Övervaka återkommande jobb
Ett återkommande jobb är ett som har samma affärslogik men använder olika indata varje gång det körs. Helst bör återkommande jobb alltid lyckas och ha relativt stabil körningstid. övervaka dessa beteenden kommer att bidra till att säkerställa att jobbet är hälsosamt. Återkommande jobb identifieras med egenskapen "Återkommande". Jobb som schemalagts med ADF V2 har automatiskt den här egenskapen ifylld.

Så här visar du en lista över U-SQL-jobb som är återkommande: 

1. Gå till dina Data Lake Analytics-konton i Azure-portalen.
2. Klicka på **Jobbstatistik**. Fliken "Alla jobb" är standard, som visar en lista över jobb som körs, köas och avslutas.
3. Klicka på fliken **Återkommande jobb.** En lista med återkommande jobb visas tillsammans med aggregerad statistik för varje återkommande jobb.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Hantera Azure Data Lake Analytics med hjälp av principer](data-lake-analytics-account-policies.md)
