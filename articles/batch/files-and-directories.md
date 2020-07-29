---
title: Filer och kataloger i Azure Batch
description: Lär dig mer om filer och kataloger och hur de används i ett Azure Batch arbets flöde från en utvecklings synpunkt.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: e7babb7e2cfdbbe78f61be766c549c1e80cacf98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83791122"
---
# <a name="files-and-directories-in-azure-batch"></a>Filer och kataloger i Azure Batch

I Azure Batch har varje aktivitet en arbets katalog där den skapar noll eller flera filer och kataloger. Den här arbetskatalogen kan användas för att lagra programmet som körs av aktiviteten, de data som bearbetas och resultatet från bearbetningen. Alla filer och kataloger för en aktivitet ägs av aktivitetsanvändaren.

Batch-tjänsten exponerar en del av filsystemet på en nod som *rotkatalogen*. Aktiviteter kan komma åt rotkatalogen genom att referera till `AZ_BATCH_NODE_ROOT_DIR`-miljövariabeln. Mer information om hur du använder miljövariabler finns i [Miljöinställningar för aktiviteter](jobs-and-tasks.md#environment-settings-for-tasks).

Rotkatalogen innehåller följande katalogstruktur:

! [Struktur för Compute Node-katalog] [media\files-and-directories\node-folder-structure.png]

- **program**: innehåller information om programpaket som har installerats på Compute-noden. Aktiviteter kan komma åt den här katalogen genom att referera till `AZ_BATCH_APP_PACKAGE`-miljövariabeln.

- **fsmounts**: katalogen innehåller alla fil system som är monterade på en Compute-nod. Aktiviteter kan komma åt den här katalogen genom att referera till `AZ_BATCH_NODE_MOUNTS_DIR`-miljövariabeln. Mer information finns i [montera ett virtuellt fil system på en batch-pool](virtual-file-mount.md).

- **shared**: Den här katalogen ger läs-/skrivbehörighet till *alla* aktiviteter som körs på en nod. Alla aktiviteter som körs på noden kan skapa, läsa, uppdatera och ta bort filer i den här katalogen. Aktiviteter kan komma åt den här katalogen genom att referera till `AZ_BATCH_NODE_SHARED_DIR`-miljövariabeln.

- **startup**: Den här katalogen används av en startaktivitet som dess arbetskatalog. Alla filer som laddas ned till noden av startaktiviteten lagras här. Startaktiviteten kan skapa, läsa, uppdatera och ta bort filer under den här katalogen. Aktiviteter kan komma åt den här katalogen genom att referera till `AZ_BATCH_NODE_STARTUP_DIR`-miljövariabeln.

- **volatile**: den här katalogen är avsedd för internt bruk. Det finns ingen garanti för att filer i katalogen eller själva katalogen kommer att finnas i framtiden.

- **WorkItems**: den här katalogen innehåller kataloger för jobb och deras aktiviteter på Compute-noden.

    I **WorkItems** -katalogen skapas en **aktivitets** katalog för varje aktivitet som körs på noden. Den här katalogen kan nås genom att referera till `AZ_BATCH_TASK_DIR` miljövariabeln.
    
    I varje **aktivitets** katalog skapar batch-tjänsten en arbets katalog ( `wd` ) vars unika sökväg anges av `AZ_BATCH_TASK_WORKING_DIR` miljö variabeln. Den här katalogen ger läs-/skrivbehörighet till aktiviteten. Aktiviteten kan skapa, läsa, uppdatera och ta bort filer under den här katalogen. Den här katalogen lagras baserat på *RetentionTime*-begränsningen som angetts för aktiviteten.

    `stdout.txt`Filerna och `stderr.txt` skrivs till mappen **tasks** under körningen av uppgiften.

> [!IMPORTANT]
> När en nod tas bort från poolen tas alla filer som lagras på noden bort.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [fel hantering och identifiering](error-handling.md) i Azure Batch.