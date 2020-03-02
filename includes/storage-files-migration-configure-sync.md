---
title: Konfigurera Azure File Sync
description: Konfigurera Azure File Sync. Ett vanligt textblock som delas mellan migreringsjobb.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209589"
---
Det här steget samverkar med alla resurser och mappar som du har konfigurerat på Windows Server under föregående steg.

* Logga in på [Azure-portalen](https://portal.azure.com).
* Leta rätt på resursen för synkroniseringstjänsten för lagring.
* Skapa en ny *Sync-grupp* i resursens synkroniseringstjänst för lagring för varje Azure-filresurs. I Azure File Sync terminologi blir Azure-filresursen en *moln slut punkt* i den crawltopologi som du beskriver när du skapar en synkroniseringsresurs. När du skapar Sync-gruppen ger du den ett välbekant namn, så att du kan identifiera vilken uppsättning filer som synkroniseras här. Se till att du refererar till Azure-filresursen med ett matchande namn.
* När Sync-gruppen har skapats visas en rad för den i listan över Sync-grupper. Klicka på namnet (en länk) om du vill visa innehållet i Sync-gruppen. Azure-filresursen visas under "moln slut punkter".
* Leta upp kommando knappen till och *Lägg till Server slut punkt*. Mappen på den lokala servern som du har allokerat blir sökvägen till *Server slut punkten*.
