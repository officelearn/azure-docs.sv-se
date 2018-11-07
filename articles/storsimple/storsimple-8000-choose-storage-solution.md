---
title: Alternativ för data Överför till Azure med hjälp av en installation | Microsoft Docs
description: Lär dig hur du väljer rätt installationen att överföra data till Azure
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 11/01/2018
ms.author: alkohli
ms.openlocfilehash: 0cb1a0bccbb989506988f36c515d59cddb832265
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263549"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Jämför StorSimple med Azure File Sync och Data Box Edge alternativ för överföring 
 
Det här dokumentet innehåller en översikt över alternativ för att överföra data för lokala till Azure, jämföra: Data Box Edge vs Azure File Sync (AFS) jämfört med StorSimple 8000-serien.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview.md)**  – Data Box Edge är en lokal nätverksenhet som flyttar data till och från Azure och har AI-aktiverade Edge-beräkning att Förbearbeta data under överföringen. Den presenterades på Ignite 2018 och är i offentlig förhandsversion. Data Box-Gateway är en virtuell version av enheten med samma funktioner för överföring av data.
- **[Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide.md)**  – Azure File Sync kan användas för att centralisera din organisations filresurser i Azure Files, samtidigt som den flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure File Sync omvandlar Windows Server till ett snabbt cacheminne för din Azure-filresurs. Allmän tillgänglighet för AFS lanserades tidigare i 2018.
- **[StorSimple](/azure/storsimple/storsimple-overview.md)**  – StorSimple är en hybrid-enhet som hjälper företag att konsolidera sina lagringsinfrastrukturen för primär lagring, dataskydd, arkivering och katastrofåterställning på en enda lösning genom nära integrering med Azure storage. Produktens hela livscykel för StorSimple finns [här](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Jämförelse av sammanfattning

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge (förhandsversion)           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Översikt         |Nivåer hybridlagring och arkivering|Allmän lagring på filserver med lagringsnivåer och flera platser molnsynkronisering.  |Lagringslösning att Förbearbeta data och skicka den via nätverket till Azure.        |
|Scenarier        |Filservern, arkivering, säkerhetskopiering mål |Filservern, arkivering (flera platser)   |Dataöverföring, data före bearbetnings inklusive ML inferensjobb, IoT, arkivering    |
|Databehandling på gränsenheter     |Inte tillgängligt |Inte tillgängligt |Stöder behållare med hjälp av Azure IoT Edge    |
|Formfaktor      |Fysisk enhet   |Agenten har installerats på Windows Server |Fysisk enhet   |
|Maskinvara         |Fysisk enhet som tillhandahålls från Microsoft som en del av tjänsten | Tillhandahållna av kunden |Fysisk enhet som tillhandahålls från Microsoft som en del av tjänsten  |
|Dataformat      |Anpassat format   |Filer         |BLOB-objekt eller filer    |
|Protokollstöd |iSCSI          |SMB, NFS    | SMB- eller NFS      |
|Prissättning          |[Priser för StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[AFS priser](https://azure.microsoft.com/pricing/details/storage/files/)  |[Priser för data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Edge om Azure Data Box](/azure/databox-online/data-box-edge-overview.md) och [Gateway om Azure Data Box](/azure/databox-online/data-box-gateway-overview.md)
- Lär dig mer om [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide.md)