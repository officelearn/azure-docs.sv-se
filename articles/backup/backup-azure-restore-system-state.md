---
title: 'Azure-säkerhetskopiering: Återställa systemtillståndet till en Windows Server'
description: Steg för steg förklaring för återställning av systemtillståndet för Windows Server från en säkerhetskopia i Azure.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 8/18/2017
ms.author: saurse
ms.openlocfilehash: f8011fb3e7e1c5267f259a43f06d605690ffd281
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606164"
---
# <a name="restore-system-state-to-windows-server"></a>Återställa systemtillståndet till Windows Server

Den här artikeln förklarar hur du återställer systemtillståndet i Windows Server från en Azure Recovery Services-valvet. Om du vill återställa Systemtillstånd måste du ha en säkerhetskopia av systemtillståndet (skapat med hjälp av anvisningarna i [säkerhetskopiera systemtillståndet](backup-azure-system-state.md#back-up-windows-server-system-state), och kontrollera att du har installerat den [senaste versionen av Microsoft Azure Recovery Services (MARS) agenten](http://aka.ms/azurebackup_agent). Återställa Systemtillstånd för Windows Server-data från ett Azure Recovery Services-valv är en tvåstegsprocess:

1. Återställa Systemtillstånd som filer från Azure Backup. När du återställer systemtillståndet som filer från Azure Backup kan du antingen:
  * Återställa systemtillståndet till samma server där säkerhetskopiorna togs, eller
  * Återställa Systemtillstånd fil till en alternativ server.

2. Gäller de återställda filerna i systemtillståndet för en Windows-Server.


## <a name="recover-system-state-files-to-the-same-server"></a>Återställer du systemtillståndet filer på samma server
Följande steg beskriver hur du återställer konfigurationen av Windows Server till ett tidigare tillstånd. Kan vara bra att återställa serverkonfiguration till en känd, stabilt tillstånd. Följande steg kan du återställa serverns systemtillstånd från en Recovery Services-valvet. 

1. Öppna snapin-modulen **Microsoft Azure Backup**. Om du inte vet där snapin-modulen installerades, söka dator eller server för **Microsoft Azure Backup**.

    Skrivbordsappen ska visas i sökresultaten.

2. Klicka på **återställa Data** så startas guiden.

    ![Återställa Data](./media/backup-azure-restore-windows-server/recover.png)

3. På den **komma igång** rutan om du vill återställa data till samma server eller dator, Välj **servern (`<server name>`)** och på **nästa**.

    ![Välj serveralternativet för att återställa data på samma dator](./media/backup-azure-restore-system-state/samemachine.png)

4. På den **Välj återställningsläge** fönstret Välj **systemtillstånd** och klicka sedan på **nästa**.

    ![Bläddra bland filer](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. I kalendern i **Välj volym och datum** pekar du väljer en återställning. 

    Du kan återställa från valfri återställningspunkt i tid. Datum i **fetstil** indikerar tillgängligheten för minst en återställningspunkt. När du har valt ett datum, om det finns flera återställningspunkter, Välj specifik återställningspunkt från den **tid** nedrullningsbara menyn.

    ![Volym och datum](./media/backup-azure-restore-system-state/select-date.png)

6. När du har valt återställningspunkt att återställa, klickar du på **nästa**.

    Azure-säkerhetskopiering monterar lokala återställningspunkten och använder den som en återställningspunkt-volym.

7. Ange mål för de återställda filerna i systemtillståndet på nästa ruta och klickar på **Bläddra** att öppna Utforskaren och hitta filer och mappar som du vill använda. Alternativet **skapa kopior så att du har båda versionerna**, skapar kopior av enskilda filer i en befintlig systemtillstånd filarkivet istället för att skapa en kopia av hela systemtillstånd arkivet.

    ![Återställningsalternativ](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Kontrollera informationen för återställning på den **bekräftelse** och klickar på **återställa**.

   ![Klicka på Återställ om du vill bekräfta åtgärden Återställ](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Kopiera den *WindowsImageBackup* katalogen på återställningsplatsen till en icke-kritisk volym på servern. Windows-Operativsystemet som volymen är vanligtvis den kritiska volymen.

10. När återställningen är klar följer du stegen i avsnittet [tillämpa återställa systemtillståndet filer till Windows Server](backup-azure-restore-system-state.md#apply-restored-system-state-files-to-the-windows-server), för att slutföra återställningen systemtillstånd.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Återställer du systemtillståndet filer till en alternativ server

Om din Windows-Server är skadad eller inte tillgänglig och du vill återställa den till ett stabilt tillstånd genom att återställa Systemtillstånd för Windows Server, kan du återställa skadade serverns systemtillstånd från en annan server. Använd följande steg för att återställa systemtillståndet på en separat server.  

De termer som används i dessa steg omfattar:

- *Källdatorn* – den ursprungliga datorn från säkerhetskopian skapades och som inte är tillgänglig för tillfället.
- *Måldatorn* – datorn som data återställs.
- *Exempel valvet* – det Recovery Services-valvet som den *källdatorn* och *måldatorn* registreras. <br/>

> [!NOTE]
> Säkerhetskopieringar som utförts från en dator kan inte återställas till en dator som kör en tidigare version av operativsystemet. Till exempel kan inte säkerhetskopieringar som utförts från en Windows Server 2016-dator återställas till Windows Server 2012 R2. Inversen är dock möjligt. Du kan använda säkerhetskopior från Windows Server 2012 R2 för att återställa Windows Server 2016.
>

1. Öppna den **Microsoft Azure Backup** snapin-modulen på den *måldatorn*.
2. Se till att den *måldatorn* och *källdatorn* är registrerade på samma Recovery Services-valvet.
3. Klicka på **återställa Data** att starta arbetsflödet.
4. Välj **en annan server**

    ![En annan Server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Ange valvautentiseringsfilen som motsvarar den *exempel valvet*. Om valvautentiseringsfilen är ogiltig (eller har upphört att gälla), hämta en ny valvautentiseringsfil från den *exempel valvet* i Azure-portalen. Recovery Services-valvet som är kopplade till valvautentiseringsfilen visas när valvautentiseringsfilen har angetts.

6. I fönstret Välj Server för säkerhetskopiering markerar du den *källdatorn* från listan över visade datorer.
7. I fönstret Välj återställningsläge väljer **systemtillstånd** och på **nästa**. 

    ![Search](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. I kalendern i den **Välj volym och datum** pekar du väljer en återställning. Du kan återställa från valfri återställningspunkt i tid. Datum i **fetstil** indikerar tillgängligheten för minst en återställningspunkt. När du har valt ett datum, om det finns flera återställningspunkter, Välj specifik återställningspunkt från den **tid** nedrullningsbara menyn. 

    ![Sökobjekt](./media/backup-azure-restore-system-state/select-date.png)

9. När du har valt återställningspunkt att återställa, klickar du på **nästa**.

10. På den **Välj återställningsläge för systemet tillstånd** rutan Ange mål där du vill att systemtillståndet filer ska återställas och klicka sedan på **nästa**.

    ![Kryptering](./media/backup-azure-restore-system-state/recover-as-files.png)

    Alternativet **skapa kopior så att du har båda versionerna**, skapar kopior av enskilda filer i en befintlig systemtillstånd filarkivet istället för att skapa en kopia av hela systemtillstånd arkivet.

11. Kontrollera informationen för återställning i rutan Bekräfta och klicka på **återställa**. 

    ![Klicka på knappen Återställ om du vill bekräfta återställningen](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Kopiera den *WindowsImageBackup* katalogen till en icke-kritisk volym på servern (till exempel D:\). Windows-Operativsystemet som volymen är vanligtvis den kritiska volymen.

13. För att slutföra återställningen måste du använda följande avsnitt för att [gäller de återställda filerna i systemtillståndet på en Windows Server](backup-azure-restore-system-state.md#apply-restored-system-state-on-a-windows-server).




## <a name="apply-restored-system-state-on-a-windows-server"></a>Tillämpa återställda systemtillståndet på en Windows Server

När du har återställt systemtillstånd som filer med Azure Recovery Services-agenten kan använda verktyget Windows Server Backup för att tillämpa återställda systemtillståndet till Windows Server. Verktyget Windows Server Backup finns redan på servern. Följande steg förklarar hur du använder återställda systemtillståndet.

1. Använd följande kommandon för att starta om servern i *reparationsläge för katalogtjänster*. I en upphöjd kommandotolk:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. Öppna snapin-modulen Windows Server Backup efter omstarten. Om du inte vet där snapin-modulen installerades, söka dator eller server för **Windows Server Backup**.

    Skrivbordsappen visas i sökresultaten.

3. I snapin-modulen, Välj **lokal säkerhetskopia**.

    ![Välj lokal säkerhetskopia att återställa därifrån](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. I konsolen lokal säkerhetskopia i den **åtgärdsfönstret**, klickar du på **återställa** att öppna Återställningsguiden.

5. Välj alternativet **en säkerhetskopia lagrad på en annan plats**, och klicka på **nästa**.

   ![Välj att återställa till en annan server](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. När du anger platstypen, Välj **Remote delade mappen** om din säkerhetskopia av systemtillståndet återställts till en annan server. Om systemets tillstånd återställts lokalt, välj sedan **lokala enheter**. 

    ![Välj om du ska återställa från en lokal server eller en annan](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Ange sökvägen till den *WindowsImageBackup* directory, eller Välj den lokala enheten som innehåller den här katalogen (till exempel D:\WindowsImageBackup) som en del av de filer återställning av systemtillstånd med hjälp av Azure Recovery Services-agenten och välj **nästa**.

    ![sökvägen till den delade filen](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Välj den version av systemtillståndet som du vill återställa och klicka på **nästa**.

9. Välj i rutan Välj typ av återställning **systemtillstånd** och på **nästa**.

10. Platsen för den återställning av systemtillstånd, Välj **ursprungsplatsen**, och klicka på **nästa**.

11. Granska informationen bekräftelse, verifiera inställningarna för omstart och på **återställa** till applly återställda systemtillståndet filer.

    ![Starta återställningen systemtillstånd filer](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Speciella överväganden för återställning av systemtillstånd på Active Directory-server

Säkerhetskopian av systemtillstånd innehåller Active Directory-data. Använd följande steg för att återställa Active Directory Domain Service (AD DS) från det aktuella tillståndet till ett tidigare tillstånd.

1. Starta om domänkontrollanten i Directory återställningsläge för katalogtjänster (DSRM).
2. Följ stegen [här](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) att använda Windows Server Backup-cmdletar för att återställa AD DS.


## <a name="troubleshoot-failed-system-state-restore"></a>Felsökning av misslyckade återställning av systemtillståndet

Om den föregående processen för tillämpning av systemtillståndet inte slutförs korrekt kan du använda Windows Recovery Environment (Win RE) för att återställa din Windows-Server. Följande steg förklarar hur du återställer med Win RE. Använd det här alternativet endast om Windows Server inte startar normalt efter en återställning av systemtillstånd. Följande process raderar-system data, var försiktig. 

1. Starta Windows-Server i Windows Återställningsmiljö (Windows RE).

2. Välj Felsök tre tillgängliga alternativ.

    ![Öppna menyn](./media/backup-azure-restore-system-state/winre-1.png)

3. Från den **avancerade alternativ** väljer **kommandotolk** och ange server-administratörsanvändarnamn och lösenord.

   ![Öppna menyn](./media/backup-azure-restore-system-state/winre-2.png)

4. Ange server-administratörsanvändarnamn och lösenord.

    ![Öppna menyn](./media/backup-azure-restore-system-state/winre-3.png)

5. När du öppnar Kommandotolken i administratörsläge, kör du följande kommando för att hämta tillståndet för filsystemet säkerhetskopior.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![Hämta systemtillstånd säkerhetskopior](./media/backup-azure-restore-system-state/winre-4.png)

6. Kör följande kommando för att hämta alla volymer som är tillgängliga i säkerhetskopian.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Hämta systemtillstånd säkerhetskopior](./media/backup-azure-restore-system-state/winre-5.png)

7. Följande kommando återställer alla volymer som ingår i säkerhetskopian av systemtillståndet. Observera att det här steget återställer endast kritiska volymer som är en del av systemtillståndet. Alla andra data raderas.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![Hämta systemtillstånd säkerhetskopior](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>Nästa steg
* Nu när du har återställt dina filer och mappar, kan du [hantera dina säkerhetskopieringar](backup-azure-manage-windows-server.md).
