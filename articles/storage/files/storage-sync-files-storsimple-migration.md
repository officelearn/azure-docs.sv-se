---
title: Migrera från StorSimple till Azure File Sync
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: fauhse
ms.subservice: files
description: StorSimple är en livs cykel produkt och Azure File Sync den lösning som ska migreras till. Lär dig mer om migrations konceptet och kontakta AzureFiles@microsoft.com för att få hjälp med anpassad migrering.
ms.openlocfilehash: edad4d1c6be2c39dbf8150b6ab8979ae3924fb53
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666668"
---
# <a name="storsimple-migration-to-azure-file-sync"></a>StorSimple migrering till Azure File Sync

StorSimple är en avvecklad Microsoft-produkt. Utökat stöd för den här produkten och dess moln tjänst kommer att ligga sist i slutet av 2022.
Det är viktigt att du börjar planera för en migrering av StorSimple omedelbart.

Standard-och Strategic StorSimple-apparater för Azure-tjänster kan migreras till, är Azure File Sync. Det är en allmänt tillgänglig Azure-tjänst med en super-uppsättning funktioner över StorSimple.

## <a name="full-cloud-side-migration-with-limited-downtime"></a>Fullständig migrering på moln sidan med begränsad stillestånds tid
I den här artikeln beskrivs begreppet hur en migrering börjar.
Det är absolut nödvändigt att Observera att kunder som behöver migreras från StorSimple och på Azure File Sync inte behöver gå vidare på egen hand.

> [!IMPORTANT]
> Microsoft strävar efter att hjälpa kunder i sin migrering. E-AzureFiles@microsoft.com för en anpassad migrations plan samt hjälp vid migreringen.

## <a name="migration-approach"></a>Metod för migrering
Migreringen till Azure File Sync börjar i molnet med minimal påverkan på lokala och begränsade stillestånds tider.
Konceptet nedan är riktad mot StorSimple 8000-seriens apparater.
Om du behöver migrera från StorSimple 7000-serien, innebär det första steget en kostnads fri uppgradering till en matchande enhet med 8000-seriens låne enhet från Microsoft.
Kontakta AzureFiles@microsoft.com så får vi hjälp att organisera ett lämpligt antal låne enheter.

### <a name="general-approach"></a>Allmän metod
![Tangenten](media/storage-sync-files-storsimple-migration/storsimple-docs-overview-concept.png "Illustrera migrering på moln sidan via en tillfällig virtuell installation och Windows Server till en ny lokal Windows Server som ersätter den lokala StorSimple-enheten")

1. Ta en volym kloning av din lokala StorSimple-installation och montera den på en tillfällig StorSimple virtuell installation.
2. Anslut den virtuella enheten via iSCSI till en tillfällig virtuell Azure-dator.
3. Installera Azure File Sync på den temporära Windows Server-VM: en speciell register nyckel för migreringen måste anges innan synkronisering har kon figurer ATS på servern.
    * Beroende på antalet resurser på din StorSimple-volym, distribuera precis så många Azure-filresurser. (Vi rekommenderar att du distribuerar en Azure-filresurs per lagrings konto.)
    * Konfigurera synkronisering mellan enskilda resurser på den virtuella datorn i Windows Server-molnet och en Azure-filresurs. (1:1-mappning)
    * Du kan också lägga till en lokal server som en lokal prestanda-cache med aktiverat moln skiktning. Det här steget är nödvändigt om du vill ersätta dina lokala StorSimple med en mer omfattande, lokal cache, som drivs av Windows Server och Azure File Sync moln nivåer. Din lokala Windows Server kan vara en fysisk dator eller ett kluster, eller en virtuell dator. Det behöver inte ha lika mycket lagring som distribueras som data uppsättnings storlek. Det behöver bara tillräckligt med lagrings utrymme för att cachelagra de filer som används oftast.

## <a name="minimizing-downtime"></a>Minimera drift stopp
Efter steg 3 ovan används den lokala StorSimple fortfarande av användare och program. Det innebär att den uppsättning filer som synkroniseras från den första volym klonen är något inaktuell vid tidpunkten då synkroniseringen slutförs.
Metoden för att minimera avbrotts tiden är att upprepa synkroniseringen från volymens klonings process, så att synkroniseringen går snabbare och snabbare med varje iteration, vilket i sin tur aktive ras genom att ändringarna mellan volym kloner blir färre och färre.
Du kan upprepa den här processen tills synkroniseringen från en volym klon kan slutföras inom den tid som du tycker är acceptabel för stillestånds tid.
När så är fallet blockerar du användare och program från att göra ändringar i StorSimple-installationen. Nedtid börjar.
Ta en annan volym klona och låt den synkronisera till serverns (a) anslutna servrar.
Upprätta åtkomst till dina användare och program till din nya, Azure File Sync säkerhets kopie rad Windows Server.
Överväg att distribuera/justera en DFS-namnrymd för att skära över från den gamla StorSimple-installationen till din nya Windows Server som är transparent för appar och användare.
Migreringen är klar.

## <a name="migration-goal"></a>Mål för migrering
När migreringen är klar kan den tillfälliga virtuella StorSimple-installationen och den virtuella Azure-datorn avetableras.

Dessutom kan den lokala StorSimple-enheten avetableras eftersom dina användare och program redan har åtkomst till Windows Server i stället.
Det du har kvar med visas i bilden nedan. En standard Azure File Sync-distribution har ett antal Azure-filresurser och Windows-servrar som är anslutna till dem via Azure File Sync. Kom ihåg att en enskild server kan ansluta olika lokala mappar till olika fil resurser på samma gång.
Dessutom kan en Azure-filresurs synkronisera till flera olika servrar, om du behöver cachelagrade data på avdelnings kontor. Kontrol lera också om du kan optimera dina principer för moln nivåer för att effektivt använda ditt lokala lagrings utrymme.

![Tangenten](media/storage-sync-files-storsimple-migration/storsimple-docs-goal.PNG "En bild som visar målet när migreringen är klar. Det visar ett antal fil resurser som synkroniseras med en lokal Windows Server med användare och program som har åtkomst till filer i molnet eller på Windows Server.")

## <a name="next-steps"></a>Nästa steg
Bekanta dig med Azure Files och Azure File Sync. Det är viktigt att förstå Azure File Sync terminologi och distributions mönster för en lyckad migrering. Mer detaljerad information finns i varje steg i den här översikts artikeln. Se till att kontakta Microsoft för att få en anpassad hjälp vid planering och körning av migreringen.

> [!IMPORTANT]
> Microsoft strävar efter att hjälpa kunder i sin migrering. E-AzureFiles@microsoft.com för en anpassad migrations plan samt hjälp vid migreringen.

## <a name="additional-resources"></a>Ytterligare resurser
Azure File Sync, som mål tjänst, har två grundläggande dokument som vi rekommenderar att du läser, om du är nybörjare på Azure File Sync.
* [Azure File Sync-översikt](storage-sync-files-planning.md)
* [Guide för Azure File Sync distribution](storage-sync-files-deployment-guide.md)

Azure Files är en lagrings tjänst i Azure som erbjuder fil resurser som en tjänst. Du behöver inte betala för eller underhålla en virtuell dator eller en associerad VM-lagring.
* [Azure Files-översikt](storage-files-introduction.md)
* [Azure Files – distribuera en Azure-filresurs](storage-how-to-create-file-share.md)