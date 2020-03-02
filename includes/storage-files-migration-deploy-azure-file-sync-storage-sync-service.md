---
title: Distribuera en tjänst för synkronisering av lagring
description: Distribuera Azure File Sync moln resurs. En tjänst för synkronisering av lagring. Ett vanligt textblock som delas mellan migreringsjobb.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4468d8c6b57b38bdbaaa01208cfc4915f734f6bb
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209459"
---
I det här steget behöver du dina autentiseringsuppgifter för Azure-prenumerationen. Den Azure-prenumeration du använder kan vara en annan än den som du använder för StorSimple.

Kärn resursen för att konfigurera Azure File Sync kallas för "Storage Sync service".
Vi rekommenderar att du endast distribuerar en för alla servrar i företaget som synkroniserar samma uppsättning filer nu eller i framtiden. Om du har mer än en StorSimple-enhet kan du överväga att skapa en resurs för Storage Sync-tjänsten för var och en av dem. Du bör dock bara skapa flera tjänster för synkronisering av lagring om du har distinkta uppsättningar av servrar som aldrig måste utbyta data. I annat fall är en enskild tjänst för synkronisering av tjänster den bästa metoden.

Välj en Azure-region för lagrings tjänsten för synkronisering som ligger nära din arbets plats. Alla andra moln resurser måste distribueras i samma region.
Bästa praxis är att skapa en ny resurs grupp i din prenumeration för att dela med sig av synkroniserings-och lagrings resurser för enklare hantering.

I följande artikel beskrivs hur du distribuerar en tjänst för synkronisering av lagring. Följ bara den här delen av dokumentet. Det kommer att finnas länkar till andra underavsnitt i dokumentet i senare steg.

[Lär dig hur du distribuerar en tjänst för synkronisering av lagring.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
