---
title: 'Azure Backup: Återställa systemtillstånd på en Windows-Server'
description: Steg för steg förklaring för återställning av systemtillståndet i Windows Server från en säkerhetskopia i Azure.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 8/18/2017
ms.author: saurse
ms.openlocfilehash: ab307548853a545c4aa8ee5a573ca5b1ca67c91d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310265"
---
# <a name="restore-system-state-to-windows-server"></a>Återställa systemtillståndet till Windows Server

Den här artikeln förklarar hur du återställer systemtillståndet i Windows Server från Azure Recovery Services-valvet. Om du vill återställa Systemtillstånd måste du ha en säkerhetskopia av systemtillståndet (skapats med hjälp av anvisningarna i [säkerhetskopiera systemtillståndet](backup-azure-system-state.md#back-up-windows-server-system-state), och kontrollera att du har installerat den [senaste versionen av Microsoft Azure Recovery Services (MARS) agenten](https://aka.ms/azurebackup_agent). Återställa Systemtillstånd för Windows Server-data från ett Azure Recovery Services-valv är en tvåstegsprocess:

1. Återställa Systemtillstånd som filer från Azure Backup. När du återställer systemtillståndet som filer från Azure Backup kan du antingen:
  * Återställa System-tillstånd till samma server där säkerhetskopiorna vidtogs, eller
  * Återställa Systemtillstånd fil till en annan server.

2. Gäller de återställda filerna i systemtillståndet för en Windows-Server.


## <a name="recover-system-state-files-to-the-same-server"></a>Återställ systemtillståndsfiler till samma server
Följande steg beskriver hur du återställa din Windows Server-konfiguration till ett tidigare tillstånd. Kan vara bra att återställa din serverkonfiguration till kända, stabilt tillstånd. Följande steg återställer serverns systemtillstånd från Recovery Services-valvet. 

1. Öppna snapin-modulen **Microsoft Azure Backup**. Om du inte vet där snapin-modulen har installerats, Sök dator eller server för **Microsoft Azure Backup**.

    Skrivbordsappen ska visas i sökresultaten.

2. Klicka på **Återställ Data** så startas guiden.

    ![Återställa Data](./media/backup-azure-restore-windows-server/recover.png)

3. På den **komma igång** , att återställa data på samma server eller dator, väljer **den här servern (`<server name>`)** och klicka på **nästa**.

    ![Välj det här serveralternativet att återställa data på samma dator](./media/backup-azure-restore-system-state/samemachine.png)

4. På den **Välj återställningsläge** fönstret Välj **systemtillstånd** och klicka sedan på **nästa**.

    ![Bläddra efter filer](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. I kalendern i **Välj volym och datum** peka fönstret väljer du en återställning. 

    Du kan återställa från valfri återställningspunkt i tid. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt. När du har valt ett datum om flera återställningspunkter är tillgängliga, Välj den specifika återställningspunkten från den **tid** nedrullningsbara menyn.

    ![Volym och datum](./media/backup-azure-restore-system-state/select-date.png)

6. När du har valt återställningspunkt att återställa, klickar du på **nästa**.

    Azure Backup monterar lokal återställningspunkt och använder dem som en volym för återställning.

7. Ange mål för de återställda filerna i systemtillståndet på nästa ruta och klickar på **Bläddra** att öppna Windows Explorer och hitta de filer och mappar som du vill. Alternativet **skapa kopior så att du har båda versionerna**, skapar kopior av enskilda filer i en befintlig systemtillstånd filarkiv istället för att skapa kopia av hela systemtillstånd arkivet.

    ![Återställningsalternativ](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Kontrollera information för återställning på den **bekräftelse** och klicka på **återställa**.

   ![Klicka på Återställ för att bekräfta åtgärden Återställ](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Kopiera den *WindowsImageBackup* katalogen på återställningsplatsen till en icke-kritiska volym på servern. Windows-operativsystemvolymen är vanligtvis den kritiska volymen.

10. När återställningen är klar följer du stegen i avsnittet [tillämpa återställs systemtillståndsfiler till Windows Server](backup-azure-restore-system-state.md), för att slutföra återställningsprocessen systemtillstånd.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Återställer du systemtillståndet filer till en annan server

Om Windows Server är skadad eller otillgänglig och du vill återställa den till ett stabilt tillstånd genom att återställa Systemtillstånd för Windows Server, kan du återställa skadat serverns systemtillstånd från en annan server. Använd följande steg för att återställa systemtillståndet på en separat server.  

De termer som används i de här stegen omfattar:

- *Källdatorn* – den ursprungliga datorn från säkerhetskopian skapades och som är för närvarande inte tillgänglig.
- *Måldatorn* – den datorn som data återställs.
- *Exemplet vault* – The Recovery Services-valv som den *källdatorn* och *måldatorn* registreras. <br/>

> [!NOTE]
> Säkerhetskopieringar som utförts från en dator kan inte återställas till en dator som kör en tidigare version av operativsystemet. Till exempel kan säkerhetskopieringar som utförts från en Windows Server 2016-dator inte återställas till Windows Server 2012 R2. Inversen är dock möjligt. Du kan använda säkerhetskopior från Windows Server 2012 R2 för att återställa Windows Server 2016.
>

1. Öppna den **Microsoft Azure Backup** snapin-modulen på den *måldatorn*.
2. Se till att den *måldatorn* och *källdatorn* är registrerade i samma Recovery Services-valvet.
3. Klicka på **Återställ Data** att starta arbetsflödet.
4. Välj **en annan server**

    ![En annan Server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Ange valvautentiseringsfilen som motsvarar den *exempel vault*. Om valvautentiseringsfilen är ogiltigt (eller har upphört att gälla) kan du ladda ned en ny valvautentiseringsfil från den *exempel vault* i Azure-portalen. När valvautentiseringsfilen har angetts visas Recovery Services-valvet som är associerade med valvautentiseringsfilen.

6. I Välj Backup Server-rutan, väljer den *källdatorn* i listan visas datorer.
7. I fönstret Välj återställningsläge väljer **systemtillstånd** och klicka på **nästa**. 

    ![Search](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. I kalendern i den **Välj volym och datum** peka fönstret väljer du en återställning. Du kan återställa från valfri återställningspunkt i tid. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt. När du har valt ett datum om flera återställningspunkter är tillgängliga, Välj den specifika återställningspunkten från den **tid** nedrullningsbara menyn. 

    ![Sökobjekt](./media/backup-azure-restore-system-state/select-date.png)

9. När du har valt återställningspunkt att återställa, klickar du på **nästa**.

10. På den **Välj återställningsläge för systemet tillstånd** rutan Ange mål där du vill att systemtillståndsfiler till att återställa och klicka sedan på **nästa**.

    ![Kryptering](./media/backup-azure-restore-system-state/recover-as-files.png)

    Alternativet **skapa kopior så att du har båda versionerna**, skapar kopior av enskilda filer i en befintlig systemtillstånd filarkiv istället för att skapa kopia av hela systemtillstånd arkivet.

11. Kontrollera information för återställning i fönstret bekräftelse och klicka på **återställa**. 

    ![Klicka på knappen Återställ för att bekräfta återställningsprocessen](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Kopiera den *WindowsImageBackup* katalogen till en icke-kritisk volym på servern (till exempel D:\). Windows-operativsystemvolymen är vanligtvis den kritiska volymen.

13. För att slutföra återställningen måste du använda följande avsnitt för att [gäller de återställda filerna i systemtillståndet på en Windows Server](backup-azure-restore-system-state.md#apply-restored-system-state-on-a-windows-server).




## <a name="apply-restored-system-state-on-a-windows-server"></a>Tillämpa återställda systemtillstånd på en Windows Server

När du har återställt systemtillstånd som filer med Azure Recovery Services-agenten kan använda verktyget Windows Server Backup för att tillämpa återställda systemtillståndet till Windows Server. Verktyget Windows Server Backup finns redan på servern. Följande steg beskriver hur du använder återställda systemtillståndet.

1. Använd följande kommandon för att starta om din server i *Directory Services Repair Mode*. I en upphöjd kommandotolk:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. Öppna snapin-modulen för Windows Server Backup efter omstarten. Om du inte vet där snapin-modulen har installerats, Sök dator eller server för **Windows Server Backup**.

    Skrivbordsappen visas i sökresultaten.

3. I snapin-modulen, väljer **lokal säkerhetskopia**.

    ![Välj lokal säkerhetskopia att återställa därifrån](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. I konsolen lokal säkerhetskopiering i den **åtgärdsfönstret**, klickar du på **återställa** att öppna Återställningsguiden.

5. Välj alternativet **en säkerhetskopia som lagras i en annan plats**, och klicka på **nästa**.

   ![välja att återställa till en annan server](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. När du anger platstypen, Välj **delad fjärrmapp** om säkerhetskopiering av systemtillstånd har återställts till en annan server. Om systemets tillstånd har återställts lokalt, väljer du **lokala enheter**. 

    ![Välj om att återställning från lokal server eller en annan](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Ange sökvägen till den *WindowsImageBackup* directory, eller Välj den lokala enheten som innehåller den här katalogen (till exempel D:\WindowsImageBackup) återställas som en del av systemtillstånd filer med hjälp av Azure Recovery Services Agent och klicka på **nästa**.

    ![sökvägen till den delade filen](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Välj systemtillstånd-version som du vill återställa och klickar på **nästa**.

9. I fönstret Välj typ av återställning väljer **systemtillstånd** och klicka på **nästa**.

10. Platsen för den återställning av systemtillståndet, Välj **ursprungsplatsen**, och klicka på **nästa**.

11. Läs mer om bekräftelse och kontrollera inställningarna för omstart, klicka **återställa** att tillämpa de återställda filerna i systemtillståndet.

    ![Starta återställningen systemtillståndsfiler](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Att tänka på för återställning av systemtillstånd på Active Directory-server

Säkerhetskopiering av systemtillstånd omfattar Active Directory-data. Använd följande steg för att återställa Active Directory Domain Service (AD DS) från det aktuella tillståndet till ett tidigare tillstånd.

1. Starta om domänkontrollanten i Directory Services återställningsläge (DSRM).
2. Följ stegen [här](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) du använder Windows Server Backup-cmdletar för att återställa AD DS.


## <a name="troubleshoot-failed-system-state-restore"></a>Felsöka misslyckade återställning av systemtillstånd

Om en föregående process för tillämpning av systemtillståndet inte slutförs korrekt kan du använda Windows Recovery Environment (Win RE) för att återställa Windows Server. Följande steg beskriver hur du återställer med hjälp av vinna RE. Använd det här alternativet endast om Windows Server inte startas normalt efter en återställning av systemtillstånd. Följande process raderar andra data, var försiktig. 

1. Starta Windows Server i Windows Återställningsmiljö (Windows RE).

2. Välj Felsök från de tre tillgängliga alternativen.

    ![Öppna menyn](./media/backup-azure-restore-system-state/winre-1.png)

3. Från den **avancerade alternativ** väljer **kommandotolk** ange server administratörens användarnamn och lösenord.

   ![Öppna menyn](./media/backup-azure-restore-system-state/winre-2.png)

4. Ange server administratörens användarnamn och lösenord.

    ![Öppna menyn](./media/backup-azure-restore-system-state/winre-3.png)

5. När du öppnar Kommandotolken i administratörsläge, kör du följande kommando för att hämta säkerhetskopior för systemtillstånd.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![Hämta systemtillstånd säkerhetskopior](./media/backup-azure-restore-system-state/winre-4.png)

6. Kör följande kommando för att hämta alla volymer som är tillgängliga i säkerhetskopian.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Hämta systemtillstånd säkerhetskopior](./media/backup-azure-restore-system-state/winre-5.png)

7. Kommandot återställer alla volymer som ingår i säkerhetskopian av systemtillståndet. Observera att det här steget återställer endast kritiska volymer som är en del av systemets tillstånd. Alla andra data raderas.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![Hämta systemtillstånd säkerhetskopior](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>Nästa steg
* Nu när du har återställt filer och mappar, kan du [hantera dina säkerhetskopior](backup-azure-manage-windows-server.md).
