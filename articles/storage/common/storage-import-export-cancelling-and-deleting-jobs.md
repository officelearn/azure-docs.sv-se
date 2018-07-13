---
title: Avbryta och ta bort ett Azure Import/Export-jobb | Microsoft Docs
description: Lär dig mer om att avbryta och ta bort jobb för Microsoft Azure Import/Export-tjänsten.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3524f1677baaa218b009b8498b851390c7b9da9a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38698697"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Avbryta och ta bort Azure Import/Export-jobb

 Att begära att ett jobb avbrytas innan den är i den `Packaging` tillstånd, anropa den [uppdatering jobbegenskaper](/rest/api/storageimportexport/jobs#Jobs_Update) igen och ange den `CancelRequested` elementet mot `true`. Jobbet avbryts efter bästa förmåga. Om enheter håller på att data överförs, kan data fortsätta överförs även efter avbrott har begärts.

 Ett avbrutna jobb har flyttats till den `Completed` tillstånd och sparas i 90 dagar, då tas den bort.

 Om du vill ta bort ett jobb, anropa den [ta bort jobbet](/rest/api/storageimportexport/jobs#Jobs_Delete) åtgärden innan jobbet har levererats (det vill säga när jobbet är i den `Creating` tillstånd). Du kan också ta bort ett jobb när det är inom den `Completed` tillstånd. När ett jobb har tagits bort, är det inte längre tillgängliga via REST API eller Azure-portalen med dess information och status.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av REST-API för Import/Export-tjänsten](storage-import-export-using-the-rest-api.md)
