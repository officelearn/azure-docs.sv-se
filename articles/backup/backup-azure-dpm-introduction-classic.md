---
title: "Säkerhetskopiera DPM-arbetsbelastningar till klassiska Azure-portalen | Microsoft Docs"
description: "En introduktion till att säkerhetskopiera DPM-servrar med Azure Backup-tjänsten"
services: backup
documentationcenter: 
author: Nkolli1
manager: shreeshd
editor: 
keywords: "System Center Data Protection Manager data protection manager, dpm-säkerhetskopiering"
ms.assetid: 8f23972b-d167-4231-b331-e198db3b18b4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: nkolli;giridham;markgal
ms.openlocfilehash: 7f13739c2d3f7fba700b649f27e02913481ca5e5
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Förbereder för att säkerhetskopiera arbetsbelastningar till Azure med DPM
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
> * [Azure Backup-Server (klassisk)](backup-azure-microsoft-azure-backup-classic.md)
> * [SCDPM (klassisk)](backup-azure-dpm-introduction-classic.md)
>
>

Den här artikeln innehåller en introduktion till Microsoft Azure Backup för att skydda dina System Center Data Protection Manager (DPM) servrar och arbetsbelastningar. Genom att läsa det, måste du förstå:

* Hur fungerar Azure DPM server-säkerhetskopiering
* Förutsättningar för att uppnå en smidig säkerhetskopiering upplevelse
* Vanliga fel som inträffar och hur du hanterar dem.
* Scenarier som stöds

System Center DPM säkerhetskopierar filer och programdata. Data som säkerhetskopieras så att DPM kan lagras på band på disk, eller säkerhetskopiera till Azure med Microsoft Azure Backup. DPM samverkar med Azure Backup på följande sätt:

* **DPM distribueras som en fysisk server eller lokal virtuell dator** – om DPM distribueras som en fysisk server eller en lokal Hyper-V virtuell dator som du kan säkerhetskopiera data till ett Azure Backup-valv förutom disk och band säkerhetskopiering.
* **DPM distribueras som en virtuell Azure-dator** – från System Center 2012 R2 med uppdatering 3 kan DPM distribueras som en virtuell Azure-dator. Om DPM distribueras som en virtuell Azure-dator kan du säkerhetskopiera data till Azure-diskar kopplade till den virtuella DPM Azure-datorn eller du omfördela lagring av data genom att säkerhetskopiera upp till ett Azure Backup-valvet.

## <a name="why-backup-your-dpm-servers"></a>Varför säkerhetskopiera DPM-servrar?
Företagets fördelar med hjälp av Azure Backup för att säkerhetskopiera DPM-servrar är:

* Du kan använda Azure backup för lokal DPM-distribution som ett alternativ till långsiktig distribution till band.
* För DPM-distributioner i Azure kan Azure Backup du omfördela lagring från Azure-disken så att du kan skala upp genom att lagra äldre data i Azure Backup och nya data på disken.

## <a name="how-does-dpm-server-backup-work"></a>Hur fungerar säkerhetskopiering med DPM-servern?
Om du vill säkerhetskopiera en virtuell dator först krävs en tidpunkt i ögonblicksbild av informationen som. Tjänsten Azure Backup initierar säkerhetskopieringsjobbet på den schemalagda tiden och utlöser reservanknytning för att ta en ögonblicksbild. Sekundär anknytning samordnar med VSS-tjänsten i gästoperativsystemet för att få konsekvent och anropar API: N för blob-ögonblicksbild i Azure Storage-tjänsten när konsekvenskontrollen har uppnåtts. Detta görs för att få en programkonsekvent ögonblicksbild av diskarna för den virtuella datorn utan att behöva stänga av.

När ögonblicksbilden har tagits, överförs data av Azure Backup-tjänsten till säkerhetskopieringsvalvet. Tjänsten hand tar om identifiering och överför de block som har ändrats från den senaste säkerhetskopieringen som gör säkerhetskopior lagring och nätverk effektivt. När dataöverföringen har slutförts ögonblicksbilden tas bort och skapa en återställningspunkt. Den här återställningspunkten kan ses i den klassiska Azure-portalen.

> [!NOTE]
> Endast filkonsekventa säkerhetskopiering är möjligt för Linux virtuella datorer.
>
>

## <a name="prerequisites"></a>Krav
Förbered Azure Backup för att säkerhetskopiera DPM-data på följande sätt:

1. **Skapa ett säkerhetskopieringsvalv**. Om du inte har skapat ett säkerhetskopieringsvalv i din prenumeration, finns i Azure portal versionen av den här artikeln - [förbereda för att säkerhetskopiera arbetsbelastningar till Azure med DPM](backup-azure-dpm-introduction.md).

  > [!IMPORTANT]
  > Från och med mars 2017 kan du inte längre använda den klassiska portalen för att skapa säkerhetskopieringsvalv.
  > Nu kan du uppgradera dina säkerhetskopieringsvalv till Recovery Services-valv. Mer information finns i artikeln [Upgrade a Backup vault to a Recovery Services vault](backup-azure-upgrade-backup-to-recovery-services.md) (Uppgradera ett säkerhetskopieringsvalv till ett Recovery Services-valv). Microsoft rekommenderar att du uppgraderar dina säkerhetskopieringsvalv till Recovery Services-valv.<br/> Efter den 30 November 2017 kommer du inte att kunna använda PowerShell för att skapa säkerhetskopieringsvalv. **Med 30 November 2017**:
  >- Alla återstående säkerhetskopieringsvalv uppgraderas automatiskt till Recovery Services-valv.
  >- Du kan inte längre komma åt dina säkerhetskopierade data i den klassiska portalen. Använd i stället Azure Portal till att få åtkomst till dina säkerhetskopierade data i Recovery Services-valv.
  >

2. **Ladda ned valvautentiseringsuppgifter** – i Azure Backup överföra det hanteringscertifikat som du skapade för valvet.
3. **Installera Azure Backup-agenten och registrera servern** – från Azure Backup, installera agenten på varje DPM-servern och registrera DPM-servern i valvet.

[!INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[!INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## <a name="requirements-and-limitations"></a>Krav (och begränsningar)
* DPM kan köras som en fysisk server eller en virtuell dator för Hyper-V som är installerade på System Center 2012 SP1 eller System Center 2012 R2. Det kan också köras som en Azure-dator som körs på System Center 2012 R2 med minst DPM 2012 R2 Samlad uppdatering 3 eller en Windows-dator i VMWare som körs på System Center 2012 R2 med minst Samlad uppdatering 5.
* Om du kör DPM med System Center 2012 SP1 måste installera du Samlad uppdatering 2 för System Center Data Protection Manager SP1. Detta krävs innan du kan installera Azure Backup-agenten.
* DPM-servern måste ha Windows PowerShell och .net Framework 4.5 installerat.
* DPM kan säkerhetskopiera de flesta arbetsbelastningar till Azure Backup. En fullständig lista över vad har som stöds finns stöd på Azure Backup för objekten nedan.
* Data som lagras i Azure Backup kan inte återställas med alternativet ”Kopiera till band”.
* Du behöver ett Azure-konto med Azure Backup-funktionen aktiverad. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Läs mer om [priser för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Med Azure Backup kräver Azure Backup-agenten installeras på servrar som du vill säkerhetskopiera. Alla servrar måste ha minst 10% av storleken på de data som säkerhetskopieras, tillgänglig som lokala ledigt utrymme. Till exempel kräver säkerhetskopiera 100 GB data minst 10 GB ledigt utrymme på den virtuella platsen. Medan minst 10% rekommenderas 15% av lokalt ledigt lagringsutrymme som ska användas för cacheplatsen.
* Data lagras i Azure valvet lagringen. Det finns ingen gräns på mängden data som du kan säkerhetskopiera till en Azure-säkerhetskopiering valvet men storleken för en datakälla (till exempel en virtuell dator eller en databas) får inte överskrida 54,400 GB.

Dessa filtyper stöds för säkerhetskopiera till Azure:

* Krypterade (endast fullständiga säkerhetskopior)
* Komprimerade (inkrementella säkerhetskopior stöds)
* Sparse-filer (inkrementella säkerhetskopior stöds)
* Komprimerade och sparse-filer (hanteras som sparse-filer)

Och de stöds inte:

* Servrar i skiftlägeskänsliga filsystem stöds inte.
* Hårda länkar (ignoreras)
* Referenspunkt (ignoreras)
* Krypterade och komprimerade (ignoreras)
* Krypterade och utspridda (ignoreras)
* Komprimerad ström
* Utspridd ström

> [!NOTE]
> Från i System Center 2012 DPM med SP1 och senare, kan du säkerhetskopiera upp arbetsbelastningar skyddade av DPM till Azure med Microsoft Azure Backup.
>
>
