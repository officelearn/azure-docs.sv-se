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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: c27244af6da01163fa9ab554b6b9c1d9c99bab23
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104581"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrera data från StorSimple 5000-7000-serien till Azure File Sync

> [!IMPORTANT]
> Den 31 juli 2019 når StorSimple 5000/7000-serien slutet av supportstatus (EOS). Vi rekommenderar att StorSimple 5000/7000-kunder som migrerar till en av de alternativ som beskrivs i dokumentet.

Migrering av data är processen att flytta data från en lagringsplats till en annan. Detta innebär att göra en exakt kopia av en organisations aktuella data från en enhet till en annan enhet, helst utan att störa eller inaktivera aktiva program – och sedan omdirigera alla indata/utdata (I/O) aktiviteten till den nya enheten. 

StorSimple 5000- och 7000 serien lagringsenheter når slutet av tjänsten i juli 2019. Detta innebär att Microsoft inte längre att kunna stödja maskinvara och programvara för StorSimple 5000/7000-serien efter juli 2019. Kunder som använder dessa enheter ska migrera sina StorSimple-data till andra hybridlösningar för lagring på Azure. Den här artikeln beskriver migrering av data från en enhet i StorSimple 5000/7000-serien till Azure File Sync (AFS).

## <a name="intended-audience"></a>Målgrupp

Den här artikeln är avsedd för arbetet för IT-personal och kunskapsarbetare som ansvarar för att distribuera och hantera enheter i StorSimple 5000/7000-serien i datacentret. Kunder som använder sina StorSimple-enheter för filen server-arbetsbelastningar (med Windows Server) kanske den här migreringsövergången särskilt tilltalande. Om du är övertygade om att funktionerna i Azure File Sync fungerar bra för din organisation, så den här artikeln hjälper dig att förstå hur du flyttar lösningarna från StorSimple.

## <a name="migration-considerations"></a>Överväganden vid migrering

Den här processen fungerar för kunder som har konfigurerat en Windows-filresurs med hjälp av en StorSimple-volym för lagring. Migrera data från StorSimple 5000/7000 till Azure File Sync innebär att konvertera den filresursplatsen till en [Serverslutpunkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) och sedan använda lokalt anslutna enheter som en annan slutpunkt som blir sedan den nya platsen. 

Följande bör övervägas vid förflyttning till AFS:

1. Azure Files har för närvarande en begränsning av 5 TB/dela. Den här begränsningen kan undvikas genom att använda Azure File Sync med data fördelade över flera Azure-filresurser. Mer information finns i [tillväxt datamönster för Azure Files-distribution](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Den här migreringen laddar ned hela primära datauppsättningen till en lokal enhet som Datakopieringen görs från den lokala enheten. Kontrollera att du har tillräckligt med bandbredd för den här överföringen.
3. Den här processen bevaras inte ögonblicksbilder som redan har skapats. Endast migrerar den primära data. Processen också bevaras inte den associerade bandbreddsmallar eller principer för säkerhetskopiering. [Använda Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files) för att ställa in principer för säkerhetskopiering när data migreras på Azure-File dela.
4. StorSimple ger den första part maskinvara. Med Azure filer/Azure-filsynkronisering använder du dock din egen lokala Windows Server-maskinvara som den lokala cachen. Du måste se till att du har tillräckligt med kapacitet att hålla datauppsättningen valfri lokala. Mer information om lagringsnivåer och inställningen krävs ledigt utrymme målet så [skapar en serverslutpunkt när du distribuerar Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Granska den [priser för Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/) eftersom det varierar från StorSimple. AFS har inte avduplicering och komprimering som StorSimple.

## <a name="migration-prerequisites"></a>Förutsättningar för migrering

Här hittar du migreringen krav för äldre 5000 eller 7000 serien enheten till Azure File Sync. Innan du börjar måste du kontrollera att du har:

- Åtkomst till en StorSimple 5000/7000 serien enhet som kör programvara version v2.1.1.518 eller senare. Tidigare versioner stöds inte. Det övre högra hörnet av webbgränssnittet på din StorSimple-enhet ska visa programvaruversion som körs.  
    Om enheten inte kör v2.1.1.518, uppgradera datorn till den minsta versionen som krävs. Detaljerade anvisningar finns i [uppgradera datorn till v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Sök efter active säkerhetskopieringsjobb som körs på källenheten. Detta inkluderar jobb på StorSimple-konsolvärden Data Protection. Vänta tills de aktuella jobb ska slutföras. 
- Åtkomst till en Windows Server-värd ansluten till StorSimple-enhet i 5000-7000-serien. Värden måste köra en Windows Server-version som stöds enligt beskrivningen i [Azure File Sync samverkan](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- StorSimple-volymer är monterade på värden och innehålla filresurser.
- Värden har tillräckligt med lokal lagring för dina lokala cachelagrade data.
- Ägare åtkomst på Azure-prenumerationen som du använder för att distribuera Azure File Sync. Det kan uppstå problem när du skapar en molnslutpunkt för din synkroniseringsgruppen om du inte har ägare eller administratör serverbehörigheter.
- Åtkomst till en [gpv2-konto](https://docs.microsoft.com/azure/storage/common/storage-account-overview) med en Azure-filresurs som du vill synkronisera. Mer information finns i [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - Så här [skapar en Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Migreringsprocessen

Migrera data från StorSimple är 5000-7000 till AFS en tvåstegsprocess:
1.  Replikera data från den lokala servern där StorSimple-volymer är monterade till en Azure Files dela.  Replikeringen sker via en AFS-agent som du installerar.
2.  Koppla från StorSimple-enheten. Lokala diskar fungerar sedan som den lokala cachen.

### <a name="migration-steps"></a>Migreringssteg

Utför följande steg för att migrera Windows-filresurs som konfigurerats på StorSimple-volymer till en Azure File Sync-resurs. 
1.  Utföra dessa steg på samma Windows Server-värd där StorSimple-volymer är monterade eller Använd ett annat system. 
    - [Förbereda Windows Server som ska användas med Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Installera Azure File Sync-agenten](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Distribuera tjänsten för synkronisering av lagring](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Registrera Windows Server med tjänsten för synkronisering av lagring](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Skapa en synkroniseringsgrupp och en molnslutpunkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). Synkroniseringsgrupper måste göras för varje Windows-filresurs som måste migreras från värden.
    - [Skapa en serverslutpunkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Ange sökvägen som sökväg för StorSimple-volym som innehåller din filresurs-data. Exempel: om StorSimple-volym är enhet `J`, och dina data finns i `J:/<myafsshare>`, lägger sedan till den här sökvägen som en serverslutpunkt. Lämna den **lagringsnivåer** som **inaktiverad**.
2.  Vänta tills den fil server-synkroniseringen har slutförts. Kontrollera att för varje server i en viss synkroniseringsgrupp:
    - Tidsstämplar för den senaste försök synkroniseringen för både överföring och hämtning är de senaste.
    - Statusen är grön för både överföring och hämtning.
    - Den **synkroniseringsaktivitet** visar mycket få eller inga filer återstår att synkronisera.
    - Den **filer inte synkroniserar** är 0 för både överföring och hämtning.
    Mer information om när servern synkroniseringen är klar går du till [felsöka Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). Synkroniseringen kan ta flera timmar och dagar, beroende på datastorlek och bandbredd. När synkroniseringen är klar, är alla dina data på ett säkert sätt i Azure-filresursen. 
3.  Gå till filresurser på StorSimple-volymer. Välj en resurs, högerklicka och välj **egenskaper**. Observera resursbehörigheter under **Security**. Behörigheterna måste tillämpas manuellt på den nya resursen i senare steg.
4.  Beroende på om du använder samma Windows Server-värd eller en annan, är nästa steg olika.

    Hoppa över det här steget och gå till nästa steg om du använder en annan Windows Server-värd. Om du använder samma Windows-filserver för AFS får nu ett par minuter stillestånd. 
    - **Avbrottstiden startar** -ta bort serverslutpunkt som du skapade i *steg 1F*. 
    - Skapa en ny serverslutpunkt med sökvägen där du vill att data ska finnas går framåt.
    - När Serverslutpunkten visas som Felfri (Detta kan ta några minuter), visas data i den här nya platsen. Du kan nu konfigurera Windows Server-värd för att hantera filer från den här nya platsen. - **Nedtid ends**.
5.  Om du använder en annan Windows-filserver för Azure File Sync, kommer du inte får någon avbrottstid. 
    - Lägg till en annan serverslutpunkt med sökvägen till den lokala lagringen som du är beredd att använda som en cache i stället för StorSimple-enheten. 
    - Du kommer att kunna se filerna i den nya servern om några minuter. Du kan med övergången från StorSimple-enheten till den nya platsen på värden när som helst.

    > [!TIP] 
    > Överväg att konfigurera den här nya filresursen med samma namn och samma sökväg som en it ersätter, för att minimera störningar. Du kan behöva om du använder DFS-N, du kan göra ändringar i konfigurationen.
6.  Konfigurera om delningsbehörigheterna som anges i *steg 3*.

Om du får problem vid migrering av data du [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Nästa steg

Om AFS inte är rätt lösning för dig, lär du dig hur du [migrera data från en StorSimple 5000-7000-serien till en enhet för 8000-serien](storsimple-8000-migrate-from-5000-7000.md).

