---
title: Kvotgränserna för Azure Data Lake Analytics
description: Lär dig hur du justerar och öka kvotgränserna i Azure Data Lake Analytics (ADLA)-konton.
services: data-lake-analytics
keywords: Azure Data Lake Analytics
documentationcenter: ''
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.topic: article
ms.workload: big-data
ms.date: 03/15/2018
ms.author: omidm
ms.openlocfilehash: 4334a438f09d7c18912262e9c70bfffbcdeb1d9e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Kvotgränserna för Azure Data Lake Analytics

Lär dig hur du justerar och öka kvotgränserna i Azure Data Lake Analytics (ADLA)-konton. Känna till dessa begränsningar kan hjälpa dig förstå din U-SQL-jobbet beteende. Alla kvotgränserna är mjuka, så du kan öka de maximala gränserna genom att kontakta Azure-supporten.

## <a name="azure-subscriptions-limits"></a>Azure-prenumerationer gränser

**Maximalt antal ADLA konton per prenumeration per region:** 5

Om du försöker skapa ett sjätte ADLA konto får du felmeddelandet ”du har nått det maximala antalet Data Lake Analytics-konton tillåts (5) i regionen under prenumerationsnamn”. 

Om du vill sträcker sig utöver den här gränsen kan du testa dessa alternativ:
* Välj en annan region om lämplig
* Kontakta Azure-supporten av [öppna ett supportärende](#increase-maximum-quota-limits) att begära en ökad kvot.

## <a name="default-adla-account-limits"></a>Standard ADLA gränser

**Maximalt antal Analytics (Australien) per konto:** 32

Detta är det maximala antalet Australien som kan köras samtidigt i ditt konto. Om det totala antalet kör Australien över alla jobb som överskrider denna gräns, nya jobb ställs i kö automatiskt. Exempel:

* Om du har endast ett jobb körs med 32 Australien när du skickar en andra jobb den kommer att vänta i jobbkön tills det första jobbet har slutförts.
* Om du redan har fyra körs och var och en med 8 Australien när du skickar ett femte jobb som behöver 8 Australien den väntar i jobbkön tills det finns 8 Australien som är tillgängliga.

**Maximalt antal Analytics (Australien) per jobb:** 32

Detta är standard maxantalet Australien som varje enskild jobb kan tilldelas i ditt konto. Jobb som har tilldelats fler än begränsningen avvisas, såvida inte påverkas av avsändaren av en princip för beräkning (jobbet skicka gräns) som ger dem mer Australien per jobb. Den övre gränsen för det här värdet är Australien gränsen för kontot.

**Maximalt antal samtidiga U-SQL-jobb per konto:** 20

Detta är det maximala antalet jobb som kan köras samtidigt i ditt konto. Ovanför det här värdet senare jobb ställs i kö automatiskt.

## <a name="adjust-adla-account-limits"></a>Justera ADLA gränser

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj ett befintligt ADLA-konto.
3. Klicka på **Egenskaper**.
4. Justera värden för **maximala Australien**, **maximalt antal jobb som körs**, och **jobbet skicka gränser** som passar dina behov.

## <a name="increase-maximum-quota-limits"></a>Öka maximal kvotgränser

Du hittar mer information om Azure gränser i den [Azure tjänstspecifika begränsar dokumentationen](../azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Öppna ett supportärende i Azure-portalen.

    ![Portalsida för Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Portalsida för Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Välj typ av problem **kvot**.
3. Välj din **prenumeration** (Kontrollera att den inte är en ”” utvärderingsprenumeration).
4. Välj typ av kvot **Datasjöanalys**.

    ![Portalsida för Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. På sidan problemet förklarar begärda öka gränsen med **information** av varför du behöver den här extra kapacitet.

    ![Portalsida för Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Kontrollera din kontaktinformation och skapa supportbegäran.

Microsoft har granskat din begäran och ett försök att passa dina behov så snart som möjligt.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Övervaka och felsök Azure Data Lake Analytics-jobb med hjälp av Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
