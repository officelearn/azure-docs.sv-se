---
title: Alternativ för dataöverföring till Azure med hjälp av en installation | Microsoft-dokument
description: Lär dig hur du väljer rätt installation för att överföra data till Azure
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 9456d975ffb6d7726166f9badbcb8fe0ccbe5256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965349"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Jämföra StorSimple med dataöverföringsalternativen Azure File Sync och Data Box Edge 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Det här dokumentet innehåller en översikt över alternativ för lokal dataöverföring till Azure, där du jämför: Data Box Edge jämfört med Azure File Sync jämfört med StorSimple 8000-serien.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview)** – Data Box Edge är en lokal nätverksenhet som flyttar data till och från Azure och har AI-aktiverad Edge-beräkning för att förbehandla data under överföringen. Data Box Gateway är en virtuell version av enheten med samma dataöverföringsfunktioner.
- **[Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)** – Azure File Sync kan användas för att centralisera organisationens filresurser i Azure Files, samtidigt som flexibiliteten, prestandan och kompatibiliteten hos en lokal filserver bevaras. Windows Server omvandlas av Azure File Sync till ett snabbt cacheminne för Azure-filresursen. Allmän tillgänglighet för Azure File Sync tillkännagavs tidigare under 2018.
- **[StorSimple](/azure/storsimple/storsimple-overview)** – StorSimple är en hybridenhet som hjälper företag att konsolidera sin lagringsinfrastruktur för primär lagring, dataskydd, arkivering och haveriberedskap på en enda lösning genom att noggrant integrera med Azure-lagring. Produktens livscykel för StorSimple hittar du [här](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Jämförelse sammanfattning

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Översikt         |Nivåindelad hybridlagring och arkivering|Allmän filserverlagring med molnnivådelning och synkronisering på flera webbplatser.  |Lagringslösning för att förbehandla data och skicka dem via nätverk till Azure.        |
|Scenarier        |Filserver, arkivering, säkerhetskopieringsmål |Filserver, arkiv (flera webbplatser)   |Dataöverföring, dataförbehandling inklusive ML inferencing, IoT, arkivering    |
|Edge-beräkning     |Inte tillgängligt |Inte tillgängligt |Stöder körning av behållare med Azure IoT Edge    |
|Formfaktor      |Fysisk enhet   |Agent installerad på Windows Server |Fysisk enhet   |
|Maskinvara         |Fysisk enhet som tillhandahålls från Microsoft som en del av tjänsten | Kunden tillhandahålls |Fysisk enhet som tillhandahålls från Microsoft som en del av tjänsten  |
|Dataformat      |Anpassat format   |Filer         |Blobbar eller filer    |
|Protokollstöd |iSCSI          |SMB, NFS    | SMB eller NFS      |
|Prissättning          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview) och Azure Data Box [Gateway](/azure/databox-online/data-box-gateway-overview)
- Lär dig mer om [Synkronisering av Azure-filer](/azure/storage/files/storage-sync-files-deployment-guide)
