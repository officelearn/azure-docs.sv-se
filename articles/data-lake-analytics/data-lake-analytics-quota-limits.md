---
title: Justera kvoter och begränsningar i Azure Data Lake Analytics
description: Lär dig hur du justerar och öka kvoter och begränsningar i Azure Data Lake Analytics (ADLA)-konton.
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 4629b52f3b2c9e351ddc2a68a40c5178a9a73950
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048263"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Justera kvoter och begränsningar i Azure Data Lake Analytics

Lär dig hur du justerar och öka kvoter och begränsningar i Azure Data Lake Analytics (ADLA)-konton. Att känna till gränserna kan hjälpa dig förstå din beteende för U-SQL-jobb. Alla kvotgränser är mjuk, så du kan öka maxgränserna genom att kontakta Azure-supporten.

## <a name="azure-subscriptions-limits"></a>Begränsningar för Azure-prenumerationer

**Maximalt antal ADLA-konton per prenumeration per region:** 5

Om du försöker skapa ett sjätte ADLA-konto, får du felmeddelandet ”du har nått det maximala antalet Data Lake Analytics-konton som tillåts (5) i region under prenumerationens namn”. 

Om du vill gå bortom den här gränsen kan du prova följande alternativ.
* Välj en annan region om det är lämpligt
* Kontakta Azure-supporten genom [att öppna ett supportärende](#increase-maximum-quota-limits) att öka kvoten.

## <a name="default-adla-account-limits"></a>Standardgränser ADLA-konto

**Maximalt antal analysenheter (AU) per konto:** 32

Det här är det maximala antalet AU som kan köras samtidigt i ditt konto. Om det totala antalet köra AU: er för alla jobb överskrider den här gränsen kan köas nyare jobb automatiskt. Exempel:

* Om du har endast ett jobb som körs med 32 analysenheter, när du skickar in en andra jobb den väntar i jobbkön tills det första jobbet har slutförts.
* Om du redan har fyra jobb som körs och var och en använder 8 analysenheter, när du skickar in ett femte jobb som måste 8 analysenheter den väntar i jobbkön tills det finns 8 analysenheter som är tillgängliga.

**Maximalt antal analysenheter (AU) per jobb:** 32

Det här är standard högsta antalet AU som kan du tilldela varje enskilda jobb i ditt konto. Jobb som tilldelas mer än den här gränsen avvisas, såvida inte påverkas av avsändaren av en princip för beräkning (jobbet skickas gräns) som ger dem mer AU per jobb. Den övre gränsen för det här värdet är AU-gräns för kontot.

**Maximalt antal samtidiga U-SQL-jobb per konto:** 20

Det här är det maximala antalet jobb som kan köras samtidigt i ditt konto. Ovanför det här värdet senare jobb ställs i kö automatiskt.

## <a name="adjust-adla-account-limits"></a>Justera ADLA gränser

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj ett befintligt ADLA-konto.
3. Klicka på **Egenskaper**.
4. Justera värden för **maximala AU: er**, **maximalt antal jobb som körs**, och **jobbet begränsningar för jobbinlämning** efter dina behov.

## <a name="increase-maximum-quota-limits"></a>Öka maximal kvotgränser

Du hittar mer information om Azure-gränser i den [Azure tjänstspecifika begränsar dokumentation](../azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Öppna en supportbegäran på Azure-portalen.

    ![Azure Data Lake Analytics-portalsidan](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics-portalsidan](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Välj typ av ärende **kvot**.
3. Välj din **prenumeration** (Kontrollera att det inte är en ”” utvärderingsprenumeration).
4. Välj typ av kvot **Datasjöanalys**.

    ![Azure Data Lake Analytics-portalsidan](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. På sidan problemet förklarar den begärda ökning gränsen med **information** över varför du behöver den här extra kapacitet.

    ![Azure Data Lake Analytics-portalsidan](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Kontrollera din kontaktinformation och skapa supportförfrågan.

Microsoft har granskat din begäran och försöker att tillgodose behoven för din verksamhet så snart som möjligt.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Övervaka och felsök Azure Data Lake Analytics-jobb med hjälp av Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
