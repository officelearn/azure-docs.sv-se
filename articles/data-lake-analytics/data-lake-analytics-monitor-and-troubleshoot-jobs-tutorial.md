---
title: Övervaka Azure Data Lake Analytics – Azure Portal
description: Den här artikeln beskriver hur du använder Azure Portal för att felsöka Azure Data Lake Analytics-jobb.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 7fb85f179f1e1e1ac873fc7d6d937b34c1d48ada
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "71316594"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Övervaka jobb i Azure Data Lake Analytics med Azure-portalen

**Visa alla jobb**

1. Klicka på **Microsoft Azure** i det övre vänstra hörnet i Azure Portal.
2. Klicka på ikonen med Data Lake Analytics-kontonamnet.  Jobb sammanfattningen visas på panelen **jobb hantering** .

    ![Azure Data Lake Analytics jobb hantering](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Jobb hanteringen ger dig en överblick över jobb statusen. Observera att det finns ett misslyckat jobb.
3. Klicka på panelen **jobb hantering** om du vill se jobben. Jobben kategoriseras i **Kör**, **köade**och **avslutade**. Du får se det misslyckade jobbet i avsnittet **Avsluta** . Den måste först vara en i listan. När du har många jobb kan du klicka på **filter** för att få hjälp att hitta jobb.

    ![Azure Data Lake Analytics filtrera jobb](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Klicka på det misslyckade jobbet i listan för att öppna jobb informationen:

    ![Azure Data Lake Analytics misslyckade jobbet](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Lägg märke till knappen **Skicka igen** . När du har åtgärdat problemet kan du skicka jobbet igen.
5. Öppna fel informationen genom att klicka på den markerade delen från föregående skärm bild.  Du ser något som liknar:

    ![Azure Data Lake Analytics information om misslyckade jobb](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Det visar att källmappen inte kan hittas.
6. Klicka på **duplicera skript**.
7. Uppdatera **från** sökväg till:

    /Samples/data/SearchLog.tsv
8. Klicka på **Skicka jobb**.

## <a name="see-also"></a>Se även
* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Hantera Azure Data Lake Analytics med Azure Portal](data-lake-analytics-manage-use-portal.md)
