---
title: Övervaka Azure Data Lake Analytics – Azure-portal
description: I den här artikeln beskrivs hur du använder Azure-portalen för att felsöka Azure Data Lake Analytics-jobb.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 7fb85f179f1e1e1ac873fc7d6d937b34c1d48ada
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71316594"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Övervaka jobb i Azure Data Lake Analytics med Azure-portalen

**Så här visar du alla jobb**

1. Klicka på Microsoft **Azure** i det övre vänstra hörnet på Azure-portalen.
2. Klicka på ikonen med Data Lake Analytics-kontonamnet.  Jobbsammanfattningen visas på panelen **Jobbhantering.**

    ![Jobbhantering i Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Jobbhanteringen ger dig en överblick över jobbstatusen. Observera att det finns ett misslyckat jobb.
3. Klicka på panelen **Jobbhantering** om du vill visa jobben. Jobben kategoriseras i **Kör,** **Kö och Avslutad**. **Ended** Du ska se ditt misslyckade jobb i avsnittet **Slut.** Den skall vara först upp i förteckningen. När du har många jobb kan du klicka på **Filter** för att hjälpa dig att hitta jobb.

    ![Azure Data Lake Analytics-filterjobb](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Klicka på det misslyckade jobbet i listan för att öppna jobbinformationen:

    ![Azure Data Lake Analytics misslyckades med jobbet](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Lägg märke till knappen **Skicka in igen.** När du har åtgärdat problemet kan du skicka in jobbet igen.
5. Klicka på markerad del från föregående skärmbild för att öppna felinformationen.  Du ska se något i stil med:

    ![Azure Data Lake Analytics misslyckades med jobbinformation](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Källmappen visas inte.
6. Klicka på **Duplicera skript**.
7. Uppdatera **FROM-sökvägen** till:

    "/Prover/Data/SearchLog.tsv"
8. Klicka på **Skicka jobb**.

## <a name="see-also"></a>Se även
* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Hantera Azure Data Lake Analytics med Azure-portal](data-lake-analytics-manage-use-portal.md)
