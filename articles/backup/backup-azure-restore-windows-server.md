---
title: Återställa data i Azure till en Windows Server eller Windows-dator
description: Lär dig hur du återställer data som lagras i Azure för att en Windows Server eller Windows-dator.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2018
ms.author: saurse
ms.openlocfilehash: 16f0460dea75b0dc52c3852d9947db0ad15f8fbe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606332"
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Återställ filer till en Windows-server eller Windows-klientdator med hjälp av Resource Manager-distributionsmodellen

Den här artikeln beskriver hur du återställer data från ett säkerhetskopieringsvalv. Om du vill återställa data använder du guiden återställa Data i Microsoft Azure Recovery Services MARS-agenten. När du återställer data är det möjligt att:

* Återställa data på samma dator där säkerhetskopieringar som utförts.
* Återställa data till en annan dator.

Microsoft släppt en förhandsgranskning uppdatering MARS-agenten i januari 2017. Tillsammans med felkorrigeringar, kan du använda snabbmeddelanden återställa, där du kan montera en skrivbar återställning punkt ögonblicksbild som en återställningspunkt-volym. Du kan sedan utforska återställningsfiler volym och kopiera till en lokal dator vilket selektivt återställer filer.

> [!NOTE]
> Den [januari 2017 Azure Backup update](https://support.microsoft.com/en-us/help/3216528?preview) krävs om du vill använda snabbmeddelanden återställning för att återställa data. Säkerhetskopierade data måste också skyddas i valv i språk som anges i support-artikeln. Läs den [januari 2017 Azure Backup update](https://support.microsoft.com/en-us/help/3216528?preview) för den senaste listan över språk som har stöd för omedelbar återställning. Omedelbar återställning **inte** är tillgänglig i alla språk.
>

Använda snabbmeddelanden återställa med Recovery Services-valv i Azure-portalen. Om du lagras data i säkerhetskopieringsvalv, har de konverterats till Recovery Services-valv. Om du vill använda snabbmeddelanden återställa hämta MARS-uppdatering och följa procedurerna som nämner omedelbar återställning.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

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
    > Om du inte klicka på Koppla från, förblir återställning volymen monterade i 6 timmar från den tidpunkt när den är monterad. Monteringstid är dock utökade upp till 24 timmar vid en pågående filkopiering maximalt. Inga säkerhetskopieringsåtgärder kommer att köras när volymen är monterad. Alla Säkerhetskopieringsåtgärden schemalagda att köras under den tid när volymen har monterats, körs när återställning volymen är frånkopplade.
    >


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

    ![Search](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

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
    > Om du inte klicka på Koppla från, förblir återställning volymen monterade i 6 timmar från den tidpunkt när den är monterad. Monteringstid är dock utökade upp till 24 timmar vid en pågående filkopiering maximalt. Inga säkerhetskopieringsåtgärder kommer att köras när volymen är monterad. Alla Säkerhetskopieringsåtgärden schemalagda att köras under den tid när volymen har monterats, körs när återställning volymen är frånkopplade.
    >

## <a name="troubleshooting"></a>Felsökning
Om Azure Backup har inte monterar återställning volymen även efter flera minuter om du klickar på **montera** eller går inte att montera volymen återställning med en eller flera fel, Följ stegen nedan för att börja återställa normalt.

1.  Avbryta pågående montera processen om det körs under flera minuter.

2.  Se till att du är på den senaste versionen av Azure Backup-agenten. Om du vill ta reda på versionsinformation för Azure Backup-agenten kan du klicka på **om Microsoft Azure Recovery Services-agenten** på den **åtgärder** fönstret Microsoft Azure Backup konsolen och se till att **Version** antalet är lika med eller högre än den version som anges i [i den här artikeln](https://go.microsoft.com/fwlink/?linkid=229525). Du kan hämta den senaste versionen från [här](https://go.microsoft.com/fwLink/?LinkID=288905)

3.  Gå till **Enhetshanteraren** -> **lagringsstyrenheter** och se till att du kan hitta **Microsoft iSCSI Initiator**. Om du kan hitta kan gå direkt till steg 7 nedan. 

4.  Om du inte kan hitta Microsoft iSCSI Initiator service som anges i steg 3, kontrollerar du om du kan hitta en post under **Enhetshanteraren** -> **lagringsstyrenheter** kallas  **Okänd enhet** med maskinvaru-ID **ROOT\ISCSIPRT**.

5.  Högerklicka på **okänd enhet** och välj **Uppdatera drivrutin**.

6.  Uppdatera drivrutinen genom att välja alternativet att **Sök automatiskt efter uppdaterade drivrutiner**. Uppdateringen har slutförts bör ändra **okänd enhet** till **Microsoft iSCSI Initiator** enligt nedan. 

    ![Kryptering](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Gå till **Aktivitetshanteraren** -> **tjänster (lokala)** -> **Microsoft iSCSI Initiator Service**. 

    ![Kryptering](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Starta om Microsoft iSCSI Initiator service genom att högerklicka på tjänsten, klicka på **stoppa** och ytterligare Högerklicka igen och klicka på **starta**.

9.  Försök återställa med hjälp av omedelbar återställning. 

Om återställningen misslyckas fortfarande starta om din server eller-klienten. Om en omstart är inte önskvärt eller återställningen fortfarande misslyckas efter att servern startas om, försök att återställa från en annan dator och kontakta Azure stöder genom att gå till [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) och skicka en supportförfrågan.

## <a name="next-steps"></a>Nästa steg
* Nu när du har återställt dina filer och mappar, kan du [hantera dina säkerhetskopieringar](backup-azure-manage-windows-server.md).
