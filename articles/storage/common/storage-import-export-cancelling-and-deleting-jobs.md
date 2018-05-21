---
title: Avbryt och ta bort ett Azure Import/Export-jobb | Microsoft Docs
description: Lär dig mer om att avbryta och ta bort jobb för tjänsten Microsoft Azure Import/Export.
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
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Avbryter och ta bort Azure Import/Export-jobb

 Att begära att ett jobb avbrytas innan den är i den `Packaging` tillstånd, anropar den [uppdatering jobbegenskaper](/rest/api/storageimportexport/jobs#Jobs_Update) igen och ange den `CancelRequested` elementet så att `true`. Jobbet har avbrutits för bästa prestanda. Om enheter håller på att överföra data, fortsätta data överförs även efter annullering har begärts.

 Avbrutna jobb har flyttats till den `Completed` tillstånd och sparas i 90 dagar, då det har tagits bort.

 Om du vill ta bort ett jobb, anropar den [ta bort jobbet](/rest/api/storageimportexport/jobs#Jobs_Delete) åtgärden innan jobbet har skickats (det vill säga när jobbet är i den `Creating` tillstånd). Du kan också ta bort ett jobb i den `Completed` tillstånd. När ett jobb har tagits bort, är det inte längre tillgängligt via REST API- eller Azure-portalen med dess information och status.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av tjänsten Import/Export REST API](storage-import-export-using-the-rest-api.md)
