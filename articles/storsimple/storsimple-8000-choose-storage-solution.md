---
title: Alternativ för data överföring till Azure med hjälp av en apparat | Microsoft Docs
description: Lär dig hur du väljer rätt apparat för att överföra data till Azure
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 9456d975ffb6d7726166f9badbcb8fe0ccbe5256
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "68965349"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Jämföra StorSimple med dataöverföringsalternativen Azure File Sync och Data Box Edge 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Det här dokumentet innehåller en översikt över alternativ för lokal data överföring till Azure, som jämför: Data Box Edge vs. Azure File Sync vs. StorSimple 8000-serien.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview)** – data Box Edge är en lokal nätverks enhet som flyttar data till och från Azure och har AI-aktiverad Edge-beräkning för att Förbearbeta data under uppladdningen. Data Box Gateway är en virtuell version av enheten med samma funktioner för data överföring.
- **[Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)** – Azure File Sync kan användas för att centralisera organisationens fil resurser i Azure Files, samtidigt som du behåller flexibilitet, prestanda och kompatibilitet för en lokal fil server. Windows Server omvandlas av Azure File Sync till ett snabbt cacheminne för Azure-filresursen. Allmän tillgänglighet för Azure File Sync annonserades tidigare i 2018.
- **[StorSimple](/azure/storsimple/storsimple-overview)** – StorSimple är en hybrid enhet som hjälper företag att konsolidera sin lagrings infrastruktur för primär lagring, data skydd, arkivering och haveri beredskap på en enskild lösning genom att noggrant integrera med Azure Storage. Produktens livs cykel för StorSimple hittar du [här](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Jämförelse Sammanfattning

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Översikt         |Nivå hybrid lagring och arkivering|Allmän fil Server lagring med moln nivåer och synkronisering på flera platser.  |Lagrings lösning för att Förbearbeta data och skicka den via nätverket till Azure.        |
|Scenarier        |Fil server, arkivering, säkerhets kopierings mål |Fil server, arkivering (flera platser)   |Data överföring, för bearbetning av data, inklusive ML inferencing, IoT, arkivering    |
|Edge-beräkning     |Inte tillgängligt |Inte tillgängligt |Stöder körning av behållare med hjälp av Azure IoT Edge    |
|Form faktor      |Fysisk enhet   |Agent som är installerad på Windows Server |Fysisk enhet   |
|Maskinvara         |Fysisk enhet som tillhandahålls från Microsoft som en del av tjänsten | Kunden tillhandahöll |Fysisk enhet som tillhandahålls från Microsoft som en del av tjänsten  |
|Dataformat      |Anpassat format   |Filer         |Blobbar eller filer    |
|Protokollstöd |iSCSI          |SMB, NFS    | SMB eller NFS      |
|Prissättning          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview) och [Azure Data Box Gateway](/azure/databox-online/data-box-gateway-overview)
- Läs mer om [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)
