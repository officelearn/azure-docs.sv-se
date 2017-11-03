---
title: "Kvotgränserna för Azure Data Lake Analytics | Microsoft Docs"
description: "Lär dig hur du justerar och öka kvotgränserna i Azure Data Lake Analytics (ADLA)-konton."
services: data-lake-analytics
keywords: Azure Data Lake Analytics
documentationcenter: 
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: omidm
ms.openlocfilehash: 957f306ea0e80b5830ad64e5ef06c6d122d9eccc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Kvotgränserna för Azure Data Lake Analytics

Lär dig hur du justerar och öka kvotgränserna i Azure Data Lake Analytics (ADLA)-konton. Känna till dessa begränsningar kan hjälpa dig förstå din U-SQL-jobbet beteende. Alla kvotgränserna är mjuka, så du kan öka de maximala gränserna genom att nå ut till oss.

## <a name="azure-subscriptions-limits"></a>Azure-prenumerationer gränser

**Maximalt antal ADLA konton per prenumeration:** 5

 Detta är det maximala antalet ADLA konton som du kan skapa per prenumeration. Om du försöker skapa ett sjätte ADLA konto får du felmeddelandet ”du har nått det maximala antalet Data Lake Analytics-konton tillåts (5) i regionen under prenumerationsnamn”. I det här fallet antingen ta bort alla oanvända ADLA konton eller nå oss av [öppna ett supportärende](#increase-maximum-quota-limits).

## <a name="adla-account-limits"></a>ADLA gränser

**Maximalt antal Analytics (Australien) per konto:** 250

Detta är det maximala antalet Australien som kan köras samtidigt i ditt konto. Om din totala kör Australien över alla jobb som överskrider denna gräns, nya jobb ställs i kö automatiskt. Exempel:

* Om du har endast ett jobb körs med 250 Australien när du skickar en andra jobb den kommer att vänta i jobbkön tills det första jobbet har slutförts.
* Om du redan har fem jobb som körs och var och en med 50 Australien när du skickar ett sjätte jobb som behöver 20 Australien den väntar i jobbkön tills det finns 20 Australien som är tillgängliga.

**Maximalt antal samtidiga U-SQL-jobb per konto:** 20

Detta är det maximala antalet jobb som kan köras samtidigt i ditt konto. Ovanför det här värdet senare jobb ställs i kö automatiskt.

## <a name="adjust-adla-quota-limits-per-account"></a>Justera ADLA kvotgränserna per konto

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj ett befintligt ADLA-konto.
3. Klicka på **Egenskaper**.
4. Justera **parallellitet** och **samtidiga jobb** som passar dina behov.

    ![Azure Data Lake Analytics-portalblad](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="increase-maximum-quota-limits"></a>Öka maximal kvotgränser

1. Öppna ett supportärende i Azure-portalen.

    ![Azure Data Lake Analytics-portalblad](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics-portalblad](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Välj typ av problem **kvot**.
3. Välj din **prenumeration** (Kontrollera att den inte är en ”” utvärderingsprenumeration).
4. Välj typ av kvot **Datasjöanalys**.

    ![Azure Data Lake Analytics-portalblad](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. I bladet problemet förklarar begärda öka gränsen med **information** av varför du behöver den här extra kapacitet.

    ![Azure Data Lake Analytics-portalblad](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Kontrollera din kontaktinformation och skapa supportbegäran.

Microsoft har granskat din begäran och ett försök att passa dina behov så snart som möjligt.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Övervaka och felsök Azure Data Lake Analytics-jobb med hjälp av Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
