---
title: "Återställa data till en Windows Server eller Windows-klienten från Azure med hjälp av den klassiska distributionsmodellen | Microsoft Docs"
description: "Lär dig hur du återställer från en Windows Server eller Windows-klient."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 85585dfc-c764-4e8c-8f0e-40b969640ac2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: 300b2b17b44e21ed446fd63d572a2461e2fc1343
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-the-classic-deployment-model"></a>Återställ filer till en Windows-server eller Windows-klientdator med hjälp av den klassiska distributionsmodellen
> [!div class="op_single_selector"]
> * [Klassisk portal](backup-azure-restore-windows-server-classic.md)
> * [Azure Portal](backup-azure-restore-windows-server.md)
>
>

Den här artikeln beskriver hur du återställa data från ett säkerhetskopieringsvalv och återställer den till en server eller dator. Med början i mars 2017 kan skapa du inte längre säkerhetskopieringsvalv i den klassiska portalen.

> [!IMPORTANT]
> Nu kan du uppgradera dina säkerhetskopieringsvalv till Recovery Services-valv. Mer information finns i artikeln [Upgrade a Backup vault to a Recovery Services vault](backup-azure-upgrade-backup-to-recovery-services.md) (Uppgradera ett säkerhetskopieringsvalv till ett Recovery Services-valv). Microsoft rekommenderar att du uppgraderar dina säkerhetskopieringsvalv till Recovery Services-valv.<br/> **Den 15 oktober 2017** kan du inte längre använda PowerShell för att skapa säkerhetskopieringsvalv. <br/> **Från den 1 november 2017**:
>- Alla återstående säkerhetskopieringsvalv uppgraderas automatiskt till Recovery Services-valv.
>- Du kan inte längre komma åt dina säkerhetskopierade data i den klassiska portalen. Använd i stället Azure Portal till att få åtkomst till dina säkerhetskopierade data i Recovery Services-valv.
>

Om du vill återställa data använder du guiden återställa Data i Microsoft Azure Recovery Services MARS-agenten. När du återställer data är det möjligt att:

* Återställa data på samma dator där säkerhetskopieringar som utförts.
* Återställa data till en annan dator.

Microsoft släppt en förhandsgranskning uppdatering MARS-agenten i januari 2017. Tillsammans med felkorrigeringar, kan du använda snabbmeddelanden återställa, där du kan montera en skrivbar återställning punkt ögonblicksbild som en återställningspunkt-volym. Du kan sedan utforska återställningsfiler volym och kopiera till en lokal dator vilket selektivt återställer filer.

> [!NOTE]
> Den [januari 2017 Azure Backup update](https://support.microsoft.com/en-us/help/3216528?preview) krävs om du vill använda snabbmeddelanden återställning för att återställa data. Säkerhetskopierade data måste också skyddas i valv i språk som anges i support-artikeln. Läs den [januari 2017 Azure Backup update](https://support.microsoft.com/en-us/help/3216528?preview) för den senaste listan över språk som har stöd för omedelbar återställning. Omedelbar återställning **inte** är tillgänglig i alla språk.
>

Omedelbar återställning är tillgänglig för användning i Recovery Services-valv i Azure portal och säkerhetskopieringsvalv i den klassiska portalen. Om du vill använda snabbmeddelanden återställa hämta MARS-uppdatering och följa procedurerna som nämner omedelbar återställning.


## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Använd Instant Restore för att återställa data på samma dator

Om du av misstag har tagit bort en fil och vill återställa den till samma dator (där säkerhetskopian tas), hjälper följande steg dig att återställa data.

1. Öppna den **Microsoft Azure Backup** fästa i. Om du inte vet var i snapin-modulen installerades, söka dator eller server för **Microsoft Azure Backup**.

    Skrivbordsappen ska visas i sökresultaten.

2. Klicka på **återställa Data** så startas guiden.

    ![Återställa Data](./media/backup-azure-restore-windows-server/recover.png)

3. På den **komma igång** rutan om du vill återställa data till samma server eller dator, Välj **servern (`<server name>`)** och på **nästa**.

    ![Välj serveralternativet för att återställa data på samma dator](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. På den **Välj återställningsläge** fönstret Välj **enskilda filer och mappar** och klicka sedan på **nästa**.

    ![Bläddra bland filer](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. På den **Välj volym och datum** fönstret väljer du den volym som innehåller filer och/eller mappar som du vill återställa.

    Välj en återställningspunkt i kalendern. Du kan återställa från valfri återställningspunkt i tid. Datum i **fetstil** indikerar tillgängligheten för minst en återställningspunkt. När du har valt ett datum, om det finns flera återställningspunkter, Välj specifik återställningspunkt från den **tid** nedrullningsbara menyn.

    ![Volym och datum](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. När du har valt återställningspunkt att återställa, klickar du på **montera**.

    Azure-säkerhetskopiering monterar lokala återställningspunkten och använder den som en återställningspunkt-volym.

7. På den **Bläddra och återställa filer** rutan klickar du på **Bläddra** att öppna Utforskaren och hitta filer och mappar som du vill använda.

    ![Återställningsalternativ](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. Kopiera filer och/eller mappar som du vill återställa och klistra in dem lokalt på servern eller datorn var i Utforskaren. Du kan öppna eller strömma filer direkt från volymen återställning och kontrollera att rätt version har återställts.

    ![Kopiera och klistra in filer och mappar från monterad volym till den lokala platsen](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. När du är klar med att återställa filer och/eller mappar på den **Bläddra och återställningsfiler** rutan klickar du på **koppla från**. Klicka på **Ja** bekräfta att du vill att montera volymen.

    ![Demontera volymen och bekräfta](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Om du inte klicka på Koppla från, förblir återställning volymen monterade under sex timmar från den tidpunkt när den är monterad. Inga säkerhetskopieringsåtgärder kommer att köras när volymen är monterad. Alla Säkerhetskopieringsåtgärden schemalagda att köras under den tid när volymen har monterats, körs när återställning volymen är frånkopplade.
    >


## <a name="recover-data-to-the-same-machine"></a>Återställa data på samma dator
Om du av misstag har tagit bort en fil och vill återställa den till samma dator (där säkerhetskopian tas), hjälper följande steg dig att återställa data.

1. Öppna den **Microsoft Azure Backup** fästa i.
2. Klicka på **återställa Data** att starta arbetsflödet.

    ![Återställa Data](./media/backup-azure-restore-windows-server-classic/recover.png)
3. Välj den  **servern (*yourmachinename*) ** alternativet för att återställa kopierade filen på samma dator.

    ![Samma dator](./media/backup-azure-restore-windows-server-classic/samemachine.png)
4. Välja att **Bläddra efter filer** eller **söka efter filer**.

    Lämna alternativet om du vill återställa en eller flera filer vars sökväg är känd. Om du inte är säker mappstrukturen men vill söka efter en fil, Välj den **söka efter filer** alternativet. Vi kommer att fortsätta med standardalternativet för det här avsnittet.

    ![Bläddra bland filer](./media/backup-azure-restore-windows-server-classic/browseandsearch.png)
5. Väljer du den volym som du vill återställa filen.

    Du kan återställa från valfri punkt i tiden. Datum som visas i **fetstil** indikerar tillgängligheten för en återställningspunkt i kalendern. När ett datum har valts baserat på schemat för säkerhetskopiering (och genomförandet av en säkerhetskopiering) kan du välja en punkt i tiden från den **tid** listrutan.

    ![Volym och datum](./media/backup-azure-restore-windows-server-classic/volanddate.png)
6. Välj de objekt som ska återställas. Du kan välja flera mappar/filer som du vill återställa.

    ![Välj filer](./media/backup-azure-restore-windows-server-classic/selectfiles.png)
7. Ange parametrar för återställning.

    ![Återställningsalternativ](./media/backup-azure-restore-windows-server-classic/recoveroptions.png)

   * Har du möjlighet att återställa till den ursprungliga platsen (där filen/mappen skulle skrivas över) eller till en annan plats på samma dator.
   * Om du vill återställa filen/mappen finns på målplatsen, kan du skapa kopior (två versioner av samma fil), skriva över filer på målplatsen eller hoppa över återställning av filer som finns i målet.
   * Vi rekommenderar starkt att du behåller du standardalternativet återställa ACL: er för filer som är som återställs.
8. När dessa indata är tillgängliga, klickar du på **nästa**. Återställningsarbetsflöde, vilket återställer filerna till den här datorn, börjar.

## <a name="recover-to-an-alternate-machine"></a>Återställ till en annan dator
Om din hela servern tappas bort, kan du fortfarande återställa data från Azure Backup till en annan dator. Följande steg visar arbetsflödet.  

De termer som används i dessa steg omfattar:

* *Källdatorn* – den ursprungliga datorn från säkerhetskopian skapades och som inte är tillgänglig för tillfället.
* *Måldatorn* – datorn som data återställs.
* *Exempel valvet* – det säkerhetskopieringsvalv som den *källdatorn* och *måldatorn* registreras. <br/>

> [!NOTE]
> Säkerhetskopieringar som utförts från en dator kan inte återställas på en dator som kör en tidigare version av operativsystemet. Till exempel om säkerhetskopiering utförs från en Windows 7-dator, kan den återställas på en Windows 8 eller senare datorn. Dock har vice versa inte värdet true.
>
>

1. Öppna den **Microsoft Azure Backup** fästa i på den *måldatorn*.
2. Se till att den *måldatorn* och *källdatorn* är registrerade på samma säkerhetskopieringsvalvet.
3. Klicka på **återställa Data** att starta arbetsflödet.

    ![Återställa Data](./media/backup-azure-restore-windows-server-classic/recover.png)
4. Välj **en annan server**

    ![En annan Server](./media/backup-azure-restore-windows-server-classic/anotherserver.png)
5. Ange valvautentiseringsfilen som motsvarar den *exempel valvet*. Om valvautentiseringsfilen är ogiltig (eller har upphört att gälla) hämta en ny valvautentiseringsfil från den *exempel valvet* i den klassiska Azure-portalen. När valvautentiseringsfilen tillhandahålls visas säkerhetskopieringsvalvet mot valvautentiseringsfilen.
6. Välj den *källdatorn* från listan över visade datorer.

    ![Lista över datorer](./media/backup-azure-restore-windows-server-classic/machinelist.png)
7. Välj antingen den **söka efter filer** eller **Bläddra efter filer** alternativet. I det här avsnittet kommer vi att använda den **söka efter filer** alternativet.

    ![Söka](./media/backup-azure-restore-windows-server-classic/search.png)
8. Välj volym och datum på nästa skärm. Sök efter mappen/filen namnet som du vill återställa.

    ![Sökobjekt](./media/backup-azure-restore-windows-server-classic/searchitems.png)
9. Välj den plats där filerna måste återställas.

    ![Återställa en plats](./media/backup-azure-restore-windows-server-classic/restorelocation.png)
10. Ange den krypteringslösenfras som du angav under *källdatorns Målegenskaper* registrering till *exempel valvet*.

    ![Kryptering](./media/backup-azure-restore-windows-server-classic/encryption.png)
11. När indata anges, klickar du på **återställa**, vilket utlöser återställning av säkerhetskopierade filerna till mål som angavs.

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Använda snabbmeddelanden återställa för att återställa data till en annan dator
Om din hela servern tappas bort, kan du fortfarande återställa data från Azure Backup till en annan dator. Följande steg visar arbetsflödet.

De termer som används i dessa steg omfattar:

* *Källdatorn* – den ursprungliga datorn från säkerhetskopian skapades och som inte är tillgänglig för tillfället.
* *Måldatorn* – datorn som data återställs.
* *Exempel valvet* – det Recovery Services-valvet som den *källdatorn* och *måldatorn* registreras. <br/>

> [!NOTE]
> Säkerhetskopieringar kan inte återställas till en måldator som kör en tidigare version av operativsystemet. Till exempel återställts en säkerhetskopia som gjorts från en Windows 7-dator kan vara på en Windows 8 eller senare, dator. En säkerhetskopia som gjorts från en Windows 8-dator kan inte återställas till en dator med Windows 7.
>
>

1. Öppna den **Microsoft Azure Backup** fästa i på den *måldatorn*.

2. Se till att den *måldatorn* och *källdatorn* är registrerade på samma Recovery Services-valvet.

3. Klicka på **återställa Data** att öppna den **guiden återställa Data**.

    ![Återställa Data](./media/backup-azure-restore-windows-server/recover.png)

4. På den **komma igång** väljer **en annan server**

    ![En annan Server](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Ange valvautentiseringsfilen som motsvarar den *exempel valvet*, och klicka på **nästa**.

    Om valvautentiseringsfilen är ogiltig (eller har upphört att gälla), hämta en ny valvautentiseringsfil från den *exempel valvet* i Azure-portalen. När du har angett en giltig valvautentiseringen visas namnet på motsvarande Säkerhetskopieringsvalvet.

6. På den **Välj Backup Server** väljer du den *källdatorn* från listan över visade datorer och lösenfrasen. Klicka sedan på **Nästa**.

    ![Lista över datorer](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. På den **Välj återställningsläge** väljer **enskilda filer och mappar** och på **nästa**.

    ![Söka](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. På den **Välj volym och datum** fönstret väljer du den volym som innehåller filer och/eller mappar som du vill återställa.

    Välj en återställningspunkt i kalendern. Du kan återställa från valfri återställningspunkt i tid. Datum i **fetstil** indikerar tillgängligheten för minst en återställningspunkt. När du har valt ett datum, om det finns flera återställningspunkter, Välj specifik återställningspunkt från den **tid** nedrullningsbara menyn.

    ![Sökobjekt](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Klicka på **montera** lokalt montera återställningspunkten som en återställningspunkt-volym på din *måldatorn*.

10. På den **Bläddra och återställa filer** rutan klickar du på **Bläddra** att öppna Utforskaren och hitta filer och mappar som du vill använda.

    ![Kryptering](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. I Utforskaren, kopiera filer och mappar från volymen återställning och klistra in dem till din *måldatorn* plats. Du kan öppna eller strömma filer direkt från volymen återställning och kontrollera att rätt version har återställts.

    ![Kryptering](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. När du är klar med att återställa filer och/eller mappar på den **Bläddra och återställningsfiler** rutan klickar du på **koppla från**. Klicka på **Ja** bekräfta att du vill att montera volymen.

    ![Kryptering](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Om du inte klicka på Koppla från, förblir återställning volymen monterade under sex timmar från den tidpunkt när den är monterad. Inga säkerhetskopieringsåtgärder kommer att köras när volymen är monterad. Alla Säkerhetskopieringsåtgärden schemalagda att köras under den tid när volymen har monterats, körs när återställning volymen är frånkopplade.
    >


## <a name="next-steps"></a>Nästa steg
* [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md)
* Besök den [Azure Backup-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933).

## <a name="learn-more"></a>Läs mer
* [Översikt över Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=222425)
* [Säkerhetskopiera Azure virtuella datorer](backup-azure-vms-introduction.md)
* [Säkerhetskopiera Microsoft-arbetsbelastningar](backup-azure-dpm-introduction.md)
