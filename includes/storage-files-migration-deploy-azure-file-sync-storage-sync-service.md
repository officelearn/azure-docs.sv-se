---
title: Distribuera en tjänst för synkronisering av lagring
description: Distribuera Azure File Sync moln resurs, en tjänst för synkronisering av lagring. Ett vanligt textblock som delas mellan migreringsjobb.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 248b61530b80a00aa10272dcb3d28b85c6ee04e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82143620"
---
I det här steget behöver du dina autentiseringsuppgifter för Azure-prenumerationen.

Den kärn resurs som ska konfigureras för Azure File Sync kallas för en *tjänst för synkronisering av lagring*. Vi rekommenderar att du endast distribuerar en för alla servrar som synkroniserar samma uppsättning filer nu eller i framtiden. Skapa bara flera tjänster för synkronisering av lagring om du har distinkta uppsättningar av servrar som aldrig måste utbyta data (till exempel: synkronisera samma Azure-filresurs). I annat fall är en enda tjänst för synkronisering av tjänster det bästa sättet.

Välj en Azure-region för din lagrings synkroniseringstjänst som ligger nära din plats. Alla andra moln resurser måste distribueras i samma region.
Du kan förenkla hanteringen genom att skapa en ny resurs grupp i din prenumeration som lagrar synkroniserings-och lagrings resurser.

Mer information finns i [avsnittet om hur du distribuerar Storage Sync-tjänsten](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) i artikeln om hur du distribuerar Azure File Sync. Följ bara den här delen av artikeln. Det kommer att finnas länkar till andra avsnitt i artikeln i senare steg.