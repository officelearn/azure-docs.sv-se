---
title: Justera kvoter och gränser i Azure Data Lake Analytics
description: Lär dig hur du justerar och ökar kvoter och begränsningar i Adla-konton (Azure Data Lake Analytics).
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c1d7c8cc4b50682a74ac88b2113f0d44ebc55199
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644723"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Justera kvoter och gränser i Azure Data Lake Analytics

Lär dig hur du justerar och ökar kvoten och gränserna i Adla-konton (Azure Data Lake Analytics). Att känna till dessa gränser hjälper dig att förstå ditt U-SQL-jobbbeteende. Alla kvotgränser är mjuka, så du kan öka maxgränserna genom att kontakta Azure-support.

## <a name="azure-subscriptions-limits"></a>Begränsningar för Azure-prenumerationer

**Maximalt antal ADLA-konton per prenumeration per region:** 5

Om du försöker skapa ett sjätte ADLA-konto får du ett felmeddelande "Du har nått det maximala antalet DataSjöanalyskonton som tillåts (5) i regionen under prenumerationsnamn".

Om du vill gå längre än den här gränsen kan du prova följande alternativ:
* välja en annan region om lämplig
* kontakta Azure-supporten genom [att öppna en supportbiljett](#increase-maximum-quota-limits) för att begära en kvotökning.

## <a name="default-adla-account-limits"></a>Standardbegränsningar för ADLA-konton

**Maximalt antal Analytics-enheter per konto:** 250, standard 32

Det här är det maximala antalet AUs som kan köras samtidigt i ditt konto. Om det totala antalet löpande RU:er för alla jobb överskrider den här gränsen ställs nyare jobb i kö automatiskt. Ett exempel:

* Om du bara har ett jobb som körs med 32 AUs väntar det i jobbkön när du skickar in ett andra jobb tills det första jobbet är klart.
* Om du redan har fyra jobb som körs och var och en använder 8 AUs, när du skickar ett femte jobb som behöver 8 AUs det väntar i jobbkön tills det finns 8 AUs tillgängliga.

    ![Gränser och kvotsida för Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Maximalt antal Analysenheter (AUs) per jobb:** 250, standard 32

Det här är det maximala antalet AUs som varje enskilt jobb kan tilldelas i ditt konto. Jobb som tilldelas mer än den här gränsen avvisas, såvida inte inlämnaren påverkas av en beräkningsprincip (gräns för jobbinlämning) som ger dem fler AUs per jobb. Den övre gränsen för det här värdet är AU-gränsen för kontot.

**Maximalt antal samtidiga U-SQL-jobb per konto:** 20

Det här är det maximala antalet jobb som kan köras samtidigt i ditt konto. Ovanför det här värdet ställs nyare jobb i kö automatiskt.

## <a name="adjust-adla-account-limits"></a>Justera adla-kontogränser

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj ett befintligt ADLA-konto.
3. Klicka på **Egenskaper**.
4. Justera värdena för **maximal aus,** **maximalt antal jobb som körs**och **jobböverföringsgränser** för att passa dina behov.

## <a name="increase-maximum-quota-limits"></a>Öka maximala kvotgränser

Du hittar mer information om [Azure-gränser i azure-tjänstspecifika gränser dokumentation](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Öppna en supportbegäran i Azure-portalen.

    ![Portalsida i Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Portalsida i Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Välj **problemtypen Kvot**.
3. Välj din **prenumeration** (se till att det inte är en "provprenumeration").
4. Välj kvottyp **DataSjöanalys**.

    ![Portalsida i Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. På problemsidan förklarar du din begärda ökningsgräns med **information om** varför du behöver den här extra kapaciteten.

    ![Portalsida i Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Verifiera din kontaktinformation och skapa supportbegäran.

Microsoft granskar din begäran och försöker tillgodose dina affärsbehov så snart som möjligt.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Övervaka och felsök Azure Data Lake Analytics-jobb med hjälp av Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
