---
title: Återställa systemtillstånd till en Windows-server
description: Steg för steg förklaring för att återställa Windows Server System State från en säkerhetskopia i Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 08/18/2017
ms.openlocfilehash: 6d46a091a4e620e26d05735f12a201009663e65d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602464"
---
# <a name="restore-system-state-to-windows-server"></a>Återställa systemtillstånd till Windows Server

I den här artikeln beskrivs hur du återställer säkerhetskopior av Windows Server System-tillstånd från ett Azure Recovery Services-valv. Om du vill återställa systemtillståndet måste du ha en säkerhetskopia av systemtillståndet (skapad med hjälp av instruktionerna i [Säkerhetskopieringstillstånd](backup-azure-system-state.md#back-up-windows-server-system-state)och se till att du har installerat den [senaste versionen av MARS-agenten (Microsoft Azure Recovery Services).](https://aka.ms/azurebackup_agent) Att återställa Windows Server System State-data från ett Azure Recovery Services-valv är en tvåstegsprocess:

1. Återställ systemtillstånd som filer från Azure Backup. När du återställer systemtillstånd som filer från Azure Backup kan du antingen:
   * Återställ systemtillståndet till samma server där säkerhetskopiorna togs, eller
   * Återställ systemtillståndsfilen till en alternativ server.

2. Använda de återställda systemtillståndsfilerna på en Windows Server.

## <a name="recover-system-state-files-to-the-same-server"></a>Återställa systemtillståndsfiler till samma server

I följande steg beskrivs hur du återställer Windows Server-konfigurationen till ett tidigare tillstånd. Det kan vara mycket värdefullt att återställa serverkonfigurationen till ett känt, stabilt tillstånd. Följande steg återställer serverns systemtillstånd från ett recovery services-valv.

1. Öppna snapin-modulen **Microsoft Azure Backup**. Om du inte vet var snapin-modulen installerades söker du i datorn eller servern efter **Microsoft Azure Backup**.

    Skrivbordsappen ska visas i sökresultaten.

2. Klicka på **Återställ data** för att starta guiden.

    ![Återställa data](./media/backup-azure-restore-windows-server/recover.png)

3. Om du vill återställa data till samma server eller dator i fönstret **Komma igång** väljer du Den här servern **`<server name>`( )** och klickar på **Nästa**.

    ![Välj det här serveralternativet för att återställa data till samma dator](./media/backup-azure-restore-system-state/samemachine.png)

4. I fönstret **Välj återställningsläge** väljer du **Systemtillstånd** och klickar sedan på **Nästa**.

    ![Bläddra bland filer](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. Välj en **återställningspunkt** i kalendern i fönstret Välj volym och Datum.

    Du kan återställa från valfri återställningspunkt i tiden. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt. När du har valt ett datum, om flera återställningspunkter är tillgängliga, väljer du den specifika återställningspunkten på den **nedrullningsbara** menyn Tid.

    ![Volym och datum](./media/backup-azure-restore-system-state/select-date.png)

6. När du har valt återställningspunkten för att återställa klickar du på **Nästa**.

    Azure Backup monterar den lokala återställningspunkten och använder den som en återställningsvolym.

7. I nästa fönster anger du målet för de återställda systemtillståndsfilerna och klickar på **Bläddra** för att öppna Utforskaren och hitta de filer och mappar du vill ha. Alternativet Skapa **kopior så att du har båda versionerna**skapar kopior av enskilda filer i ett befintligt systemtillståndsfilarkiv i stället för att skapa en kopia av hela systemtillståndsarkivet.

    ![Alternativ för återställning](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Kontrollera information om återställning i **bekräftelsefönstret** och klicka på **Återställ**.

   ![Klicka på Återställ för att bekräfta återställningsåtgärden](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Kopiera *WindowsImageBackup-katalogen* i återställningsmålet till en icke-kritisk volym på servern. Vanligtvis är Windows OS-volymen den kritiska volymen.

10. När återställningen har slutförts följer du stegen i avsnittet [Använd återställda systemtillståndsfiler på Windows Server](backup-azure-restore-system-state.md)för att slutföra återställningsprocessen för systemtillstånd.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Återställa systemtillståndsfiler till en alternativ server

Om Windows Server är skadad eller otillgänglig och du vill återställa den till ett stabilt tillstånd genom att återställa tillståndet i Windows Server System, kan du återställa den skadade serverns systemtillstånd från en annan server. Följ följande steg till återställningssystemets tillstånd på en separat server.  

Terminologin som används i dessa steg omfattar:

* *Källmaskin* – Den ursprungliga datorn som säkerhetskopian togs från och som för närvarande inte är tillgänglig.
* *Måldatorn* – Den dator som data återställs till.
* *Provvalv* – Valvet för återställningstjänster som *källdatorn* och *måldatorn* är registrerade till. <br/>

> [!NOTE]
> Säkerhetskopior som tas från en dator kan inte återställas till en dator som kör en tidigare version av operativsystemet. Säkerhetskopior som hämtats från en Windows Server 2016-dator kan till exempel inte återställas till Windows Server 2012 R2. Men det omvända är möjligt. Du kan använda säkerhetskopior från Windows Server 2012 R2 för att återställa Windows Server 2016.
>

1. Öppna snapin-modulen **Microsoft Azure Backup** på *måldatorn*.
2. Kontrollera att *Måldatorn* och *källdatorn* är registrerade i samma Recovery Services-valv.
3. Klicka på **Återställ data** om du vill starta arbetsflödet.
4. Välj **en annan server**

    ![En annan server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Ange den arkivautentiseringsfil som motsvarar *exempelvalvet*. Om arkivautentiseringsfilen är ogiltig (eller har upphört att gälla) hämtar du en ny arkivautentiseringsfil från *exempelvalvet* i Azure-portalen. När arkivautentiseringsfilen har angetts visas valvet för Återställningstjänster som är associerat med valvautentiseringsfilen.

6. Välj *källdatorn* i listan över datorer som visas i fönstret Välj säkerhetskopieringsserver.
7. I fönstret Välj återställningsläge väljer du **Systemtillstånd** och klickar på **Nästa**.

    ![Search](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. Välj en återställningspunkt i fönstret **Välj volym och datum.** Du kan återställa från valfri återställningspunkt i tiden. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt. När du har valt ett datum, om flera återställningspunkter är tillgängliga, väljer du den specifika återställningspunkten på den **nedrullningsbara** menyn Tid.

    ![Sök efter objekt](./media/backup-azure-restore-system-state/select-date.png)

9. När du har valt återställningspunkten för att återställa klickar du på **Nästa**.

10. I fönstret **Välj systemtillståndsåterställningsläge** anger du det mål där du vill att systemtillståndsfiler ska återställas och klickar sedan på **Nästa**.

    ![Kryptering](./media/backup-azure-restore-system-state/recover-as-files.png)

    Alternativet Skapa **kopior så att du har båda versionerna**skapar kopior av enskilda filer i ett befintligt systemtillståndsfilarkiv i stället för att skapa en kopia av hela systemtillståndsarkivet.

11. Kontrollera information om återställning i bekräftelsefönstret och klicka på **Återställ**.

    ![Klicka på knappen Återställ för att bekräfta återställningsprocessen](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Kopiera *WindowsImageBackup-katalogen* till en icke-kritisk volym på\)servern (till exempel D: . Vanligtvis är Windows OS-volymen den kritiska volymen.

13. Om du vill slutföra återställningsprocessen använder du följande avsnitt för att [tillämpa de återställda systemtillståndsfilerna på en Windows Server](#apply-restored-system-state-on-a-windows-server).

## <a name="apply-restored-system-state-on-a-windows-server"></a>Tillämpa återställt systemtillstånd på en Windows Server

När du har återställt systemtillståndet som filer med Azure Recovery Services Agent använder du verktyget Windows Server Backup för att tillämpa det återställda systemtillståndet på Windows Server. Verktyget Windows Server Backup är redan tillgängligt på servern. I följande steg beskrivs hur du tillämpar det återställda systemtillståndet.

1. Använd följande kommandon för att starta om servern i *reparationsläge för katalogtjänster*. I en upphöjd kommandotolk:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

2. Öppna snapin-modulen Windows Server Backup efter omstarten. Om du inte vet var snapin-modulen installerades söker du i datorn eller servern efter **Windows Server Backup**.

    Skrivbordsappen visas i sökresultaten.

3. I snapin-modulen väljer du **Lokal säkerhetskopiering**.

    ![välj Lokal säkerhetskopiering för att återställa därifrån](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. Öppna återställningsguiden i **åtgärdsfönstret**på konsolen Lokal säkerhetskopiering. **Recover**

5. Välj **alternativet, En säkerhetskopia som lagras på en annan plats**och klicka på **Nästa**.

   ![välja att återställa till en annan server](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. När du anger platstypen väljer du **Fjärrdelad mapp** om säkerhetskopian av systemtillstånd återställdes till en annan server. Om systemtillståndet återställdes lokalt väljer du **Lokala enheter**.

    ![välja om återställning från lokal server eller annan](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Ange sökvägen till *WindowsImageBackup-katalogen* eller välj den lokala enhet som innehåller den här katalogen (till exempel D:\WindowsImageBackup), som återställs som en del av återställningen av systemtillståndsfiler med Hjälp av Azure Recovery Services Agent och klicka på **Nästa**.

    ![sökväg till den delade filen](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Markera den systemtillståndsversion som du vill återställa och klicka på **Nästa**.

9. I fönstret Välj återställningstyp väljer du **Systemtillstånd** och klickar på **Nästa**.

10. Om du vill placera systemtillståndsåterställning väljer du **Ursprunglig plats**och klickar på **Nästa**.

11. Granska bekräftelseinformationen, verifiera omstartsinställningarna och klicka på **Återställ** för att tillämpa de återställda systemtillståndsfilerna.

    ![starta filerna för återställning av systemtillstånd](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Särskilda överväganden för återställning av systemtillstånd på Active Directory-servern

Säkerhetskopiering av systemtillstånd innehåller Active Directory-data. Använd följande steg för att återställa AD DS (Active Directory Domain Service) från dess aktuella tillstånd till ett tidigare tillstånd.

1. Starta om domänkontrollanten i DSRM (Directory Services Restore Mode).
2. Följ stegen [här](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore) för att använda cmdlets för Windows Server Backup för att återställa AD DS.

## <a name="troubleshoot-failed-system-state-restore"></a>Felsökning av misslyckad återställning av systemtillstånd

Om den föregående processen med att använda systemtillstånd inte slutförs använder du Windows Recovery Environment (Win RE) för att återställa Windows Server. I följande steg förklaras hur du återställer med Win RE. Använd det här alternativet endast om Windows Server inte startar normalt efter en återställning av systemtillstånd. Följande process raderar data som inte är systemdata, var försiktig.

1. Starta Windows Server i Windows Recovery Environment (Win RE).

2. Välj Felsöka bland de tre tillgängliga alternativen.

    ![öppna menyn](./media/backup-azure-restore-system-state/winre-1.png)

3. På skärmen **Avancerade alternativ** väljer du **Kommandotolken** och anger användarnamn och lösenord för serveradministratören.

   ![öppna menyn](./media/backup-azure-restore-system-state/winre-2.png)

4. Ange användarnamn och lösenord för serveradministratören.

    ![öppna menyn](./media/backup-azure-restore-system-state/winre-3.png)

5. När du öppnar kommandotolken i administratörsläge kör du följande kommando för att hämta säkerhetskopieringsversionerna för systemtillstånd.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![hämta säkerhetskopieringsversioner för systemtillstånd](./media/backup-azure-restore-system-state/winre-4.png)

6. Kör följande kommando för att hämta alla volymer som är tillgängliga i säkerhetskopian.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![hämta säkerhetskopieringsversioner för systemtillstånd](./media/backup-azure-restore-system-state/winre-5.png)

7. Följande kommando återställer alla volymer som ingår i säkerhetskopieringen av systemtillstånd. Observera att det här steget återställer endast de kritiska volymer som ingår i systemtillståndet. Alla data som inte är systemdata raderas.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![hämta säkerhetskopieringsversioner för systemtillstånd](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Nästa steg

* Nu när du har återställt dina filer och mappar kan du [hantera dina säkerhetskopior](backup-azure-manage-windows-server.md).
