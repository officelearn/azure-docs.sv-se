---
title: Övervaka jobb i Azure Data Lake Analytics med hjälp av Azure portal
description: Den här artikeln beskriver hur du använder Azure-portalen för att felsöka Azure Data Lake Analytics-jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 1e18addc43e53cb45e92966607ad5d1db2b42c3c
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046727"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Övervaka jobb i Azure Data Lake Analytics med hjälp av Azure-portalen

**Se alla jobb**

1. Azure-portalen klickar du på **Microsoft Azure** i det övre vänstra hörnet.
2. Klicka på ikonen med Data Lake Analytics-kontonamnet.  Jobbsammanfattningen visas på den **jobbhantering** panelen.

    ![Hantering av Azure Data Lake Analytics-jobb](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Jobbet Management ger dig en snabb överblick över för jobbets status. Observera att det är ett jobb som misslyckades.
3. Klicka på den **jobbhantering** ikonen för att visa jobb. Jobben har kategoriserats i **kör**, **i kö**, och **avslutat**. Du bör se misslyckade jobbet i den **avslutat** avsnittet. Det bör vara första certifikatet i listan. När du har en massa jobb kan du klicka **Filter** som hjälper dig att hitta jobb.

    ![Azure Data Lake Analytics filtrera jobb](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Klicka på det misslyckade jobbet från listan för att öppna Jobbinformationen:

    ![Azure Data Lake Analytics misslyckades jobb](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Observera den **skicka** knappen. När du löser problemet, kan du skicka jobbet.
5. Klicka på markerade del från föregående skärmbild att öppna felinformationen.  Du bör se något som liknar:

    ![Azure Data Lake Analytics misslyckades jobbinformation](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Du kan ta reda källmappen inte finns.
6. Klicka på **duplicera skriptet**.
7. Uppdatera den **FROM** sökvägen till:

    ”/ Samples/Data/SearchLog.tsv”
8. Klicka på **Skicka jobb**.

## <a name="see-also"></a>Se också
* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md)
