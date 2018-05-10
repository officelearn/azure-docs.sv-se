---
title: Övervaka jobb i Azure Data Lake Analytics med hjälp av Azure Portal | Microsoft Docs
description: 'Lär dig hur du använder Azure Portal för att felsöka Data Lake Analytics-jobb. '
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 14b1f4ec9dff78e4b5d2480755a4b1f2579ec135
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Övervaka jobb i Azure Data Lake Analytics med hjälp av Azure-portalen

**Att se alla jobb**

1. Azure-portalen klickar du på **Microsoft Azure** i det övre vänstra hörnet.
2. Klicka på ikonen med Data Lake Analytics-kontonamnet.  Översikt över jobbet visas på den **jobbhantering** panelen.

    ![Hantering av Azure Data Lake Analytics-jobbet](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Jobbet Management ger dig en överblick över status för jobbet. Observera att det är ett jobb som misslyckades.
3. Klicka på den **jobbhantering** ruta visas jobben. Jobben kategoriseras i **kör**, **i kö**, och **avslutat**. Du bör se din misslyckade jobb i den **avslutat** avsnitt. Den skall är först i listan. När du har många jobb kan du klicka på **Filter** som hjälper dig att hitta jobb.

    ![Filtrera Azure Data Lake Analytics-jobb](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Klicka på det misslyckade jobbet från listan för att öppna Jobbinformationen:

    ![Azure Data Lake Analytics misslyckades jobb](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Observera den **skicka** knappen. När du har åtgärdat problemet kan du skicka jobbet.
5. Klicka på markerade del från föregående skärmbild öppna felinformationen.  Du bör se något som liknar:

    ![Azure Data Lake Analytics misslyckades jobbinformation](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Den visar källmappen inte hittas.
6. Klicka på **duplicera skriptet**.
7. Uppdatering av **FROM** sökvägen till:

    ”/ Samples/Data/SearchLog.tsv”
8. Klicka på **Skicka jobb**.

## <a name="see-also"></a>Se också
* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md)
