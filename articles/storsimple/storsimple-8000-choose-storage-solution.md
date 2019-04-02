---
title: Alternativ för data Överför till Azure med hjälp av en installation | Microsoft Docs
description: Lär dig hur du väljer rätt installationen att överföra data till Azure
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: d727ed62f5a869f6eb67400281d2660607756abf
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802702"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Jämför StorSimple med Azure File Sync och Data Box Edge alternativ för överföring 
 
Det här dokumentet innehåller en översikt över alternativ för att överföra data för lokala till Azure, jämföra: Data Box Edge vs. Jämfört med Azure File Sync StorSimple 8000-serien.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview)**  – Data Box Edge är en lokal nätverksenhet som flyttar data till och från Azure och har AI-aktiverade Edge-beräkning att Förbearbeta data under överföringen. Data Box-Gateway är en virtuell version av enheten med samma funktioner för överföring av data.
- **[Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)**  – Azure File Sync kan användas för att centralisera din organisations filresurser i Azure Files, samtidigt som den flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure File Sync omvandlar Windows Server till ett snabbt cacheminne för din Azure-filresurs. Allmän tillgänglighet för Azure File Sync lanserades tidigare i 2018.
- **[StorSimple](/azure/storsimple/storsimple-overview)**  – StorSimple är en hybrid-enhet som hjälper företag att konsolidera sina lagringsinfrastrukturen för primär lagring, dataskydd, arkivering och katastrofåterställning på en enda lösning genom nära integrering med Azure storage. Produktens hela livscykel för StorSimple finns [här](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Jämförelse av sammanfattning

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Översikt         |Nivåer hybridlagring och arkivering|Allmän lagring på filserver med lagringsnivåer och flera platser molnsynkronisering.  |Lagringslösning att Förbearbeta data och skicka den via nätverket till Azure.        |
|Scenarier        |Filservern, arkivering, säkerhetskopiering mål |Filservern, arkivering (flera platser)   |Dataöverföring, data före bearbetnings inklusive ML inferensjobb, IoT, arkivering    |
|Databehandling på gränsenheter     |Inte tillgängligt |Inte tillgängligt |Stöder behållare med hjälp av Azure IoT Edge    |
|Formfaktor      |Fysisk enhet   |Agenten har installerats på Windows Server |Fysisk enhet   |
|Maskinvara         |Fysisk enhet som tillhandahålls från Microsoft som en del av tjänsten | Tillhandahållna av kunden |Fysisk enhet som tillhandahålls från Microsoft som en del av tjänsten  |
|Dataformat      |Anpassat format   |Filer         |BLOB-objekt eller filer    |
|Protokollstöd |iSCSI          |SMB, NFS    | SMB- eller NFS      |
|Prissättning          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Edge om Azure Data Box](/azure/databox-online/data-box-edge-overview) och [Gateway om Azure Data Box](/azure/databox-online/data-box-gateway-overview)
- Lär dig mer om [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)
