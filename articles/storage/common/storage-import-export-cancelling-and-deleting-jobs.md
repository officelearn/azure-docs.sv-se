---
title: Avbryta och ta bort ett Azure Import/Export-jobb | Microsoft Docs
description: Lär dig mer om att avbryta och ta bort jobb för Microsoft Azure Import/Export-tjänsten.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 6eb56413319208feef1b4ab51296fe12a1e0bcf2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483153"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Avbryta och ta bort Azure Import/Export-jobb

 Att begära att ett jobb avbrytas innan den är i den `Packaging` tillstånd, anropa den [uppdatering jobbegenskaper](/rest/api/storageimportexport/jobs) igen och ange den `CancelRequested` elementet mot `true`. Jobbet avbryts efter bästa förmåga. Om enheter håller på att data överförs, kan data fortsätta överförs även efter avbrott har begärts.

 Ett avbrutna jobb har flyttats till den `Completed` tillstånd och sparas i 90 dagar, då tas den bort.

 Om du vill ta bort ett jobb, anropa den [ta bort jobbet](/rest/api/storageimportexport/jobs) åtgärden innan jobbet har levererats (det vill säga när jobbet är i den `Creating` tillstånd). Du kan också ta bort ett jobb när det är inom den `Completed` tillstånd. När ett jobb har tagits bort, är det inte längre tillgängliga via REST API eller Azure-portalen med dess information och status.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av REST-API för Import/Export-tjänsten](storage-import-export-using-the-rest-api.md)
