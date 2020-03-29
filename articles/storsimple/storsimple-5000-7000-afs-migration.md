---
title: Migrera data från StorSimple 5000-7000-serien till Azure File Sync| Microsoft-dokument
description: Beskriver hur du migrerar data från StorSimple 5000/7000-serien till Azure File Sync (AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b46e9ee8fc3e14981a01cc2425a8ce55d06c5a9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65150741"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrera data från StorSimple 5000-7000-serien till Azure File Sync

> [!IMPORTANT]
> Den 9 juli 2019 kommer StorSimple 5000/7000-serien att nå slutet av supportstatusen (EOS). Vi rekommenderar att StorSimple 5000/7000-seriens kunder migrerar till något av alternativen som beskrivs i dokumentet.

Datamigrering är processen att flytta data från en lagringsplats till en annan. Detta innebär att en exakt kopia av en organisations aktuella data från en enhet till en annan enhet , helst utan att störa eller inaktivera aktiva program , och sedan omdirigera all in-/utdataaktivitet (I/O) till den nya enheten. 

LagringSimple 5000 och 7000-seriens lagringsenheter kommer att nå slutet av tjänsten i juli 2019. Detta innebär att Microsoft inte längre kommer att kunna stödja maskin- och programvara för StorSimple 5000/7000-serien efter juli 2019. Kunder som använder dessa enheter bör migrera sina StorSimple-data till andra hybridlagringslösningar på Azure. Den här artikeln beskriver migreringen av data från en StorSimple 5000/7000-serieenhet till Azure File Sync (AFS).

## <a name="intended-audience"></a>Målgrupp

Den här artikeln är avsedd för IT-proffs och kunskapsarbetare som ansvarar för att distribuera och hantera StorSimple 5000/7000-serieenheter i datacentret. De kunder som använder sina StorSimple-enheter för filserverarbetsbelastningar (med Windows Server) kan tycka att den här migreringsvägen är särskilt tilltalande. Om du tror att funktionerna i Azure File Sync fungerar bra för din organisation, kommer den här artikeln att hjälpa dig att förstå hur du flyttar till dessa lösningar från StorSimple.

## <a name="migration-considerations"></a>Överväganden vid migrering

Den här processen fungerar för kunder som har konfigurerat en Windows-filresurs med en StorSimple-volym för lagring. Migrera data från StorSimple 5000/7000 till Azure File Sync innebär att konvertera den filresursplatsen till en [serverslutpunkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) och sedan använda lokalt anslutna enheter som en annan slutpunkt som sedan blir din nya plats. 

När du flyttar till AFS bör följande punkter beaktas:

1. Azure-filer har för närvarande en begränsning på 5 TB/delning. Den här begränsningen kan övervinnas med hjälp av Azure File Sync med data spridda över flera Azure File-resurser. Mer information finns i [datatillväxtmönstret för Azure Files deployment](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Den här migreringen hämtar hela primärdatauppsättningen till en lokal enhet när datakopian görs från den lokala enheten. Se till att du har tillräcklig bandbredd för att hantera denna överföring.
3. Den här processen bevarar inte de ögonblicksbilder som redan har skapats. Den migrerar bara primärdata. Processen bevarar inte heller de associerade bandbreddsmallarna eller säkerhetskopieringsprinciperna. [Använd Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files) för att ställa in principer för säkerhetskopiering när data har migrerats på Azure File-resursen.
4. StorSimple tillhandahåller maskinvara från första part. Men med Azure Files/Azure File Sync använder du din egen lokala Windows Server-maskinvara som lokal cache. Du måste se till att du har tillräcklig lagringskapacitet för att behålla den datauppsättning som du väljer lokalt. Om du vill ha mer information om nivåindelning och ange nödvändiga mål för ledigt utrymme kan du läsa hur [du skapar en serverslutpunkt när du distribuerar Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Granska [priserna för Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/) eftersom det varierar från StorSimple. AFS har inte deduplicering och komprimering som StorSimple.

## <a name="migration-prerequisites"></a>Förutsättningar för migrering

Här hittar du migreringsförutsättningarna för din äldre enhet i 5 000 eller 7 000-serien till Azure File Sync. Innan du börjar, se till att du har:

- Tillgång till en StorSimple 5000/7000-serie enhet som kör programversion v2.1.1.518 eller senare. Tidigare versioner stöds inte. Det övre högra hörnet av webbgränssnittet på Din StorSimple-enhet bör visa den programvaruversion som körs.  
    Om enheten inte kör v2.1.1.518 uppgraderar du systemet till den minimala versionen som krävs. Detaljerade instruktioner finns i [Uppgradera systemet till v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Sök efter aktiva säkerhetskopieringsjobb som körs på källenheten. Detta inkluderar jobben på StorSimple Data Protection Console-värden. Vänta tills de aktuella jobben har slutförts. 
- Åtkomst till en Windows Server-värd som är ansluten till din StorSimple 5000-7000-serieenhet. Värden måste köra en Windows Server-version som stöds enligt beskrivningen i [Azure File Sync-interoperabilitet](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- StorSimple-volymer monteras på värden och innehåller filresurser.
- Värden har tillräckligt med lokal lagring för att lagra lokala cachelagrade data.
- Åtkomst på ägarnivå till Den Azure-prenumeration som du ska använda för att distribuera Azure File Sync. Du kan få problem när du skapar en molnslutpunkt för din synkroniseringsgrupp om du inte har behörighet på ägar- eller administratörsnivå.
- Åtkomst till ett [allmänt v2-lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-account-overview) med en Azure File Share som du vill synkronisera med. Mer information finns i [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - Skapa [en Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Migreringsprocessen

Att migrera data från StorSimple 5000-7000 till AFS är en tvåstegsprocess:
1.  Replikera data från den lokala filservern där StorSimple-volymerna är monterade på en Azure Files-resurs.  Replikering sker via en AFS-agent som du installerar.
2.  Koppla från StorSimple-enheten. De lokala diskarna fungerar sedan som den lokala cachen.

### <a name="migration-steps"></a>Migreringsanvisningar

Utför följande steg för att migrera Windows-filresursen som konfigurerats på StorSimple-volymer till en Azure File Sync-resurs. 
1.  Utför dessa steg på samma Windows Server-värd där StorSimple-volymerna är monterade eller använder ett annat system. 
    - [Förbered Windows Server som ska användas med Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Installera Azure File Sync-agenten](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Distribuera tjänsten Storage Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Registrera Windows Server med tjänsten Storage Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Skapa en synkroniseringsgrupp och en molnslutpunkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). Synkroniseringsgrupper måste göras för varje Windows-filresurs som måste migreras från värden.
    - [Skapa en serverslutpunkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Ange sökvägen som sökväg till den StorSimple-volym som innehåller filresursdata. Om storsimple-volymen till exempel `J`är enhet och `J:/<myafsshare>`dina data finns i lägger du till den här sökvägen som en serverslutpunkt. Lämna **nivåindelningen** som **inaktiverad**.
2.  Vänta tills filserversynkroniseringen är klar. Kontrollera att du måste se till att
    - Tidsstämplarna för den senaste synkroniseringen för både uppladdning och nedladdning är nya.
    - Statusen är grön för både uppladdning och nedladdning.
    - **Synkroniseringsaktiviteten** visar mycket få eller inga filer kvar att synkronisera.
    - **Filerna inte synkronisera är** 0 för både ladda upp och ladda ner.
    Mer information om när serversynkroniseringen är klar finns i [Felsöka Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). Synkroniseringen kan ta flera timmar till dagar, beroende på din datastorlek och bandbredd. När synkroniseringen är klar finns alla dina data på ett säkert sätt i Azure File Share. 
3.  Gå till aktierna på StorSimple-volymerna. Välj en resurs, högerklicka och välj **Egenskaper**. Observera resursbehörigheterna under **Säkerhet**. Dessa behörigheter måste tillämpas manuellt på den nya resursen i det senare steget.
4.  Beroende på om du använder samma Windows Server-värd eller en annan kommer nästa steg att vara olika.

    Hoppa över det här steget och gå till nästa steg om du använder en annan Windows Server-värd. Om du använder samma Windows File Server för AFS, kommer du nu att uppleva några minuters driftstopp. 
    - **Driftstopp startar** - Ta bort serverslutpunkten som du skapade i *steg 1F*. 
    - Skapa en ny serverslutpunkt med sökvägen dit du vill att data ska finnas framöver.
    - När serverslutpunkten visas som Felfri (det kan ta några minuter) visas data på den här nya platsen. Du kan nu konfigurera windows servervärden så att filer visas från den här nya platsen. - **Driftstopp slutar**.
5.  Om du använder en annan Windows File Server för Azure File Sync, kommer du inte att uppleva några driftstopp. 
    - Lägg till ytterligare en serverslutpunkt med sökvägen till den lokala lagring som du är beredd att använda som cache i stället för StorSimple-enheten. 
    - Du kommer att kunna se filerna i den nya servern på några minuter. Du är fri att göra övergången från din StorSimple-enhet till den här nya platsen på värden när som helst.

    > [!TIP] 
    > Överväg att konfigurera den nya filresursen med samma namn och samma sökväg som den som den ersätter, för att minimera störningar. Om du använder DFS-N kan det krävas att du gör ändringar i konfigurationen.
6.  Konfigurera om delningsbehörigheterna enligt *steg 3*.

Om du får problem under datamigreringen kontaktar du [Microsoft Support](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Nästa steg

Om AFS inte är rätt lösning för dig kan du läsa om hur du [migrerar data från en StorSimple 5000-7000-serie till en enhet i 8000-serien](storsimple-8000-migrate-from-5000-7000.md).

