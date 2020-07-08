---
title: Konfigurera Azure File Sync
description: Konfigurera Azure File Sync. Ett vanligt textblock som delas mellan migreringsjobb.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 562099c65273cad50596268b89055ec78d92d18a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82143549"
---
Det här steget samverkar med alla resurser och mappar som du har konfigurerat på din Windows Server-instans under föregående steg.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Leta rätt på resursen för synkroniseringstjänsten för lagring.
1. Skapa en ny *Sync-grupp* i resursens synkroniseringstjänst för lagring för varje Azure-filresurs. I Azure File Sync terminologi blir Azure-filresursen en *moln slut punkt* i den crawltopologi som du beskriver när du skapade en synkroniseringsresurs. När du skapar Sync-gruppen ger du den ett välbekant namn så att du kan identifiera vilken uppsättning filer som synkroniseras här. Se till att du refererar till Azure-filresursen med ett matchande namn.
1. När Sync-gruppen har skapats visas en rad för den i listan över Sync-grupper. Välj namnet (en länk) om du vill visa innehållet i Sync-gruppen. Du ser Azure-filresursen under **moln slut punkter**.
1. Leta upp knappen **+ Lägg till Server slut punkt** . Mappen på den lokala servern som du har etablerad blir sökvägen till den här *Server slut punkten*.
