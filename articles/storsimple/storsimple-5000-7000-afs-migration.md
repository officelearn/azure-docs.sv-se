---
title: Migrera data från StorSimple 5000-7000-serien till Azure File Sync | Microsoft Docs
description: Beskriver hur du migrerar data från StorSimple 5000/7000-serien till Azure File Sync (AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 5d656fd8757580b8ce96acf168e92fc847d400ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514057"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrera data från StorSimple 5000-7000-serien till Azure File Sync

> [!IMPORTANT]
> Den 9 juli 2019 når StorSimple 5000/7000-serien status för slut på support (EOS). Vi rekommenderar att StorSimple 5000/7000-seriens kunder migrerar till något av de alternativ som beskrivs i dokumentet.

Datamigrering är en process för att flytta data från en lagrings plats till en annan. Detta innebär att skapa en exakt kopia av en organisations aktuella data från en enhet till en annan enhet, helst utan att störa eller inaktivera aktiva program, och sedan omdirigera all I/O-aktivitet (in-/utdata) till den nya enheten. 

Lagrings enheter i StorSimple 5000 och 7000-serien kommer att uppnå tjänstens slut i juli 2019. Detta innebär att Microsoft inte längre kommer att kunna stödja maskin vara och program vara för StorSimple 5000/7000-serien efter 2019 juli. Kunder som använder dessa enheter bör migrera sina StorSimple-data till andra hybrid lagrings lösningar på Azure. Den här artikeln beskriver migrering av data från en StorSimple 5000/7000-serien till Azure File Sync (AFS).

## <a name="intended-audience"></a>Målgrupp

Den här artikeln är avsedd för IT-proffs och kunskaps arbetare som ansvarar för att distribuera och hantera StorSimple 5000/7000-serie enheter i data centret. De kunder som använder sina StorSimple-enheter för fil Server arbets belastningar (med Windows Server) kan hitta den här migreringen, särskilt tilltalande. Om du tror att funktionerna i Azure File Sync fungerar bra för din organisation, kommer den här artikeln att hjälpa dig att förstå hur du flyttar till dessa lösningar från StorSimple.

## <a name="migration-considerations"></a>Överväganden vid migrering

Den här processen fungerar för kunder som har konfigurerat en Windows-filresurs med hjälp av en StorSimple-volym för lagring. Migrering av data från StorSimple 5000/7000 till Azure File Sync innebär att konvertera fil resurs platsen till en [Server slut punkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) och sedan använda lokalt anslutna enheter som en annan slut punkt som sedan blir din nya plats. 

När du flyttar till AFS bör följande punkter beaktas:

1. Azure Files har för närvarande en begränsning på 5 TB/resurs. Den här begränsningen kan lösas genom att använda Azure File Sync med data spridning över flera Azure-filresurser. Mer information finns i [mönster för data tillväxt för Azure Files distribution](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Den här migreringen hämtar hela den primära data uppsättningen till en lokal enhet eftersom data kopieringen görs från den lokala enheten. Se till att du har tillräckligt med bandbredd för att kunna hantera den här överföringen.
3. Den här processen bevarar inte de ögonblicks bilder som redan har skapats. Den migrerar bara primär data. Processen bevarar inte heller de associerade mallarna för bandbredd eller säkerhets kopierings principer. [Använd Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files) för att ställa in säkerhets kopierings principerna när data har migrerats på Azure-filresursen.
4. StorSimple tillhandahåller maskin vara från första part. Men med Azure Files/Azure File Sync använder du din egen lokala Windows Server-maskinvara som lokal cache. Du måste se till att du har tillräckligt med lagrings kapacitet för att behålla den data uppsättning som du väljer lokalt. Läs mer om hur du [skapar en server slut punkt när du distribuerar Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Granska [prissättningen för Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/) varierar från StorSimple. AFS saknar deduplicering och komprimering som StorSimple.

## <a name="migration-prerequisites"></a>Krav för migrering

Här hittar du förutsättningarna för migrering för din äldre 5000-eller 7000-serie enhet till Azure File Sync. Innan du börjar ska du kontrol lera att du har:

- Åtkomst till en StorSimple 5000/7000-serie enhet som kör program varu version v 2.1.1.518 eller senare. Tidigare versioner stöds inte. Det övre högra hörnet av webb gränssnittet i StorSimple-enheten bör visa den program varu version som körs.  
    Om enheten inte kör v-2.1.1.518 måste du uppgradera systemet till den minimala versionen som krävs. Detaljerade anvisningar finns i [uppgradera systemet till v 2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Kontrol lera om det finns aktiva säkerhets kopierings jobb som körs på käll enheten. Detta inkluderar jobben på värden för StorSimple Data Protection-konsolen. Vänta tills de aktuella jobben har slutförts. 
- Åtkomst till en Windows Server-värd som är ansluten till din StorSimple 5000-7000-serie enhet. Värden måste köra en Windows Server-version som stöds på det sätt som beskrivs i [Azure File Sync samverkan](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- StorSimple-volymer monteras på värden och innehåller fil resurser.
- Värden har tillräckligt med lokal lagring för att lagra dina lokalt cachelagrade data.
- Ägar nivå åtkomst till den Azure-prenumeration som du ska använda för att distribuera Azure File Sync. Det kan uppstå problem när du skapar en moln slut punkt för din Sync-grupp om du inte har behörighet som ägare eller administratörs nivå.
- Åtkomst till ett [Allmänt lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-account-overview) med en Azure-filresurs som du vill synkronisera till. Mer information finns i [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - Så här [skapar du en Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Migreringsprocessen

Migrering av data från StorSimple 5000-7000 till AFS är en två stegs process:
1.  Replikera data från den lokala fil servern där StorSimple-volymerna monteras till en Azure Files-resurs.  Replikering görs via en AFS-agent som du installerar.
2.  Koppla från StorSimple-enheten. De lokala diskarna fungerar sedan som lokalt cacheminne.

### <a name="migration-steps"></a>Migreringsanvisningar

Utför följande steg för att migrera Windows-filresursen som kon figurer ATS på StorSimple-volymer till en Azure File Sync-resurs. 
1.  Utför de här stegen på samma Windows Server-värd där StorSimple-volymerna är monterade eller Använd ett annat system. 
    - [Förbered Windows Server för användning med Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Installera Azure File Sync agenten](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Distribuera tjänsten för synkronisering av lagring](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Registrera Windows Server med Storage Sync-tjänsten](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Skapa en Sync-grupp och en moln slut punkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). Sync-grupper måste göras för varje Windows-filresurs som måste migreras från värden.
    - [Skapa en server slut punkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Ange sökvägen som sökvägen till StorSimple-volymen som innehåller dina fil resurs data. Om StorSimple-volymen till exempel är enhet `J` , och dina data finns i `J:/<myafsshare>` , lägger du till den här sökvägen som en server slut punkt. Låt **nivån** vara **inaktive rad**.
2.  Vänta tills fil serverns synkronisering har slutförts. Kontrol lera följande för varje server i en specifik Sync-grupp:
    - Tidsstämplar för senaste försök till synkronisering för både överföring och nedladdning har nyligen gjorts.
    - Statusen är grön för både överföring och hämtning.
    - **Sync-aktiviteten** visar väldigt få eller inga filer kvar för synkronisering.
    - **Filerna som inte synkroniseras** är 0 för både överföring och hämtning.
    Om du vill ha mer information om när serverns synkronisering är klar går du till [felsöka Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). Synkroniseringen kan ta flera timmar till dagar, beroende på data storlek och bandbredd. När synkroniseringen är klar är alla dina data på ett säkert sätt i Azure-filresursen. 
3.  Gå till resurserna på StorSimple-volymerna. Välj en resurs, högerklicka och välj **Egenskaper**. Observera resurs behörigheterna under **säkerhet**. Dessa behörigheter måste appliceras manuellt på den nya resursen i senare steg.
4.  Nästa steg varierar beroende på om du använder samma Windows Server-värd eller en annan.

    Hoppa över det här steget och gå till nästa steg om du använder en annan Windows Server-värd. Om du använder samma Windows-fil server för AFS kommer du nu att uppleva några minuter av drift stopp. 
    - **Nedtid startar** – tar bort Server slut punkten som du skapade i *steg 1f*. 
    - Skapa en ny server slut punkt med den sökväg där du vill att data ska placeras framåt.
    - När Server slut punkten visas som felfri (det kan ta några minuter) visas data på den nya platsen. Nu kan du konfigurera Windows Server-värden för att betjäna filer från den här nya platsen. - **Avbrotts tiden är slut**.
5.  Om du använder en annan Windows-filserver för Azure File Sync kommer du inte att uppleva några avbrott. 
    - Lägg till en annan server slut punkt med sökvägen till den lokala lagring som du är redo att använda som cache i stället för StorSimple-enheten. 
    - Du kommer att kunna se filerna på den nya servern om några minuter. Du är kostnads fri att göra växeln från din StorSimple-enhet till den nya platsen på värden när som helst.

    > [!TIP] 
    > Överväg att konfigurera den här nya fil resursen med samma namn och samma sökväg som den ersätter, för att minimera störningar. Om du använder DFS-N kan det krävas att du gör ändringar i konfigurationen.
6.  Konfigurera om delnings behörigheterna enligt vad som anges i *steg 3*.

Om du får problem under datamigreringen kan du [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Nästa steg

Om AFS inte är rätt lösning för dig, lär du dig hur du [migrerar data från en StorSimple 5000-7000-serie till en enhet med 8000-serien](storsimple-8000-migrate-from-5000-7000.md).

