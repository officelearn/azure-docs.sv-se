---
title: 'Azure Backup: Återställa filer och mappar från en virtuell Azure-säkerhetskopiering'
description: Återställa filer från en återställningspunkt för virtuella Azure-datorer
services: backup
author: pvrk
manager: shivamg
keywords: återställning på objektnivå; filåterställning från virtuell Azure-säkerhetskopiering; återställa filer från virtuella Azure-datorer
ms.service: backup
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: pullabhk
ms.openlocfilehash: fecdb54af58faaf601ab74f89039a47e0d32e650
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493389"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Återställa filer från säkerhetskopiering av Azure virtuella datorer

Azure Backup ger möjlighet att återställa [Azure virtuella datorer (VM) och diskar](./backup-azure-arm-restore-vms.md) från Virtuella Azure-säkerhetskopieringar, även kallat återställningspunkter. Den här artikeln förklaras hur du återställer filer och mappar från en virtuell Azure-säkerhetskopiering. Återställa filer och mappar är endast tillgänglig för Azure virtuella datorer distribueras med hjälp av Resource Manager-modellen och skyddas i ett Recovery services-valv.

> [!Note]
> Den här funktionen är tillgänglig för Azure virtuella datorer distribueras med hjälp av Resource Manager-modellen och skyddas i ett Recovery Services-valv.
> Filåterställning från en krypterad säkerhetskopiering av virtuella datorer stöds inte.
>

## <a name="mount-the-volume-and-copy-files"></a>Montera volymen och kopiera filer

Om du vill återställa filer och mappar från återställningspunkten, gå till den virtuella datorn och välj önskad återställningspunkt.

1. Logga in på den [Azure-portalen](http://portal.Azure.com) och i den vänstra rutan klickar du på **virtuella datorer**. Välj den virtuella datorn och öppna instrumentpanelen för den virtuella datorn från listan över virtuella datorer.

2. Klicka på den virtuella datorns menyn **Backup** att öppna instrumentpanelen för säkerhetskopiering.

    ![Öppna Recovery Services-valv säkerhetskopieringsobjekt](./media/backup-azure-restore-files-from-vm/open-vault-from-vm.png)

3. I menyn Backup instrumentpanelen klickar du på **filåterställning** menyn öppnas.

    ![Arkiv-menyn för återställning](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Från den **Välj återställningspunkt** nedrullningsbara menyn, Välj den återställningspunkt som innehåller de filer som du vill. Den senaste återställningspunkten har redan valts som standard.

5. Om du vill hämta programvaran som används för att kopiera filer från återställningspunkten klickar du på **ladda ned körbar fil** (för Windows Azure-VM) eller **ladda ned skriptet** (för Linux Azure VM).

    ![Genererat lösenord](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure hämtar körbara filer eller skript till den lokala datorn.

    ![Hämta meddelande för körbara filer eller skript](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Om du vill köra körbara filer eller skript som en administratör, rekommenderas du spara den på din dator.

6. Körbara filer eller skript är lösenordsskyddat och kräver ett lösenord. I den **filåterställning** menyn, klicka på kopieringsknappen för att läsa in lösenordet i minnet.

    ![Genererat lösenord](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Högerklicka på den körbara filen eller skriptet från nedladdningsplatsen (vanligtvis mappen nedladdningar) och kör den med administratörsbehörighet. När du uppmanas, anger du lösenordet eller klistra in lösenordet från minnet och tryck på RETUR. När du har angett ett giltigt lösenord, ansluts skriptet till återställningspunkten.

    ![Arkiv-menyn för återställning](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Om du kör skriptet på en dator med begränsad åtkomst, se till att det finns åtkomst till:

    - Download.microsoft.com
    - Recovery Service-URL: er (geo-namn som refererar till den region där det recovery service-valvet finns)
        - <https://pod01-rec2.geo-name.backup.windowsazure.com> (För offentliga Azure-regioner)
        - <https://pod01-rec2.geo-name.backup.windowsazure.cn> (För Azure Kina)
        - <https://pod01-rec2.geo-name.backup.windowsazure.us> (För Azure Government för USA)
        - <https://pod01-rec2.geo-name.backup.windowsazure.de> (För Azure Tyskland)
    - utgående port 3260

    För Linux kräver skriptet 'iscsi-öppen' och 'lshw' komponenter att ansluta till återställningspunkten. Om komponenterna inte finns på den dator där skriptet körs begär skriptet behörighet att installera komponenterna. Ge medgivande till att installera de nödvändiga komponenterna.

    Åtkomst till download.microsoft.com krävs för att hämta komponenter som används för att skapa en säker kanal mellan datorn där skriptet körs och data i återställningspunkten.

    Du kan köra skriptet på en dator som har samma (eller kompatibla)-operativsystem som den säkerhetskopierade virtuella datorn. Se den [kompatibla OS tabell](backup-azure-restore-files-from-vm.md#system-requirements) för kompatibla operativsystem. Om skyddade virtuella Azure-datorer använder Windows-lagringsutrymmen (för Windows Azure virtuella datorer) eller LVM/RAID-matriser (för virtuella Linux-datorer) kan köra du inte körbara filer eller skript på samma virtuella dator. I stället köra körbara filer eller skript på någon annan dator med ett kompatibelt operativsystem.

### <a name="identifying-volumes"></a>Identifiera volymer

#### <a name="for-windows"></a>För Windows

När du kör den körbara filen, monterar de nya volymerna i operativsystemet och tilldelar enhetsbeteckningar. Du kan använda Windows Explorer eller Utforskaren för att bläddra bland dessa enheter. Enhetsbeteckningar till volymer kan inte vara samma bokstäverna som den ursprungliga virtuella datorn, men volymens namn bevaras. Om volymen på den ursprungliga virtuella datorn har till exempel ”Data-Disk (E:`\`)”, att volymen kan kopplas på den lokala datorn som ”Data-Disk (alla bokstäver:`\`). Bläddra igenom alla volymer som nämns i utdata från skriptet tills du hittar dina filer/mappar.  

   ![Arkiv-menyn för återställning](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>För Linux

I Linux monteras volymer för återställningspunkten till den mapp där skriptet körs. Därefter visas anslutna diskar, volymer och motsvarande mount-sökvägar. Dessa montera sökvägar är synliga för användare med nivån rotåtkomst. Bläddra igenom de volymer som nämns i utdata från skriptet.

  ![Linux recovery filmenyn](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  
## <a name="closing-the-connection"></a>Stänga anslutningen

Ta bort (eller demontera) på ytterligare enheter när du identifierar filerna och kopiera dem till en lokal lagringsplats. Demontera enheter, på den **filåterställning** menyn i Azure-portalen klickar du på **demontera diskar**.

![Demontera diskar](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

När diskarna har demonterats, får du ett meddelande som du kan se det lyckades. Det kan ta några minuter innan anslutningen att uppdatera så att du kan ta bort diskarna.

I Linux, när anslutningen till återställningspunkten är avskiljes Operativsystemet tar inte bort motsvarande monteringssökvägar automatiskt. Monteringssökvägar finns som ”överblivna” volymer och de är synliga men returnerade ett fel när du åtkomst/Skriv-filerna. De kan tas bort manuellt. Skriptet när den körs identifierar dessa volymer som befintlig från alla tidigare återställningspunkterna och rensar dem vid godkännande.

## <a name="special-configurations"></a>Särskilda konfigurationer

### <a name="dynamic-disks"></a>Dynamiska diskar

Om den skyddade virtuella Azure-datorn har volymer med en eller båda av följande egenskaper kan köra du inte körbara skriptet på samma virtuella dator.

    - Volymer som sträcker sig över flera diskar (som omfattas och stripe-volymer)
    - Feltoleranta volymer (volymer speglad eller RAID-5) på dynamiska diskar

I stället köra skriptet körbara på andra datorer med ett kompatibelt operativsystem.

### <a name="windows-storage-spaces"></a>Windows-lagringsutrymmen

Lagringsutrymmen för Windows är en Windows-teknik som gör det möjligt att virtualisera lagring. Du kan gruppera standarddiskar i lagringspooler med lagringsutrymmen för Windows. Du kan använda det tillgängliga utrymmet i dessa lagringspooler för att skapa virtuella diskar som kallas lagringsutrymmen.

Om den skyddade virtuella Azure-datorer använder lagringsutrymmen för Windows, kan du inte köra körbara skriptet på samma virtuella dator. I stället köra skriptet körbara på någon annan dator med ett kompatibelt operativsystem.

### <a name="lvmraid-arrays"></a>LVM/RAID-matriser

I Linux för logiska volymer (LVM) och/eller programvara RAID-matriser att hantera logiska volymer över flera diskar. Om den skyddade Linux VM använder LVM och/eller RAID-matriser kan köra du inte skriptet på samma virtuella dator. I stället köra skriptet på någon annan dator med ett kompatibelt operativsystem och som har stöd för filsystemet för den skyddade virtuella datorn.

Följande skriptutdata visar LVM och/eller RAID-matriser diskar och volymer med partitionstypen.

   ![Menyn Linux LVM-utdata](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Kör kommandon i följande avsnitt för att göra de här partitionerna online.

#### <a name="for-lvm-partitions"></a>För LVM partitioner

Listan över namnen på volym under fysiska enheter.

```bash
#!/bin/bash
$ pvs <volume name as shown above in the script output>
```

Listan över alla logiska volymer, namn och deras sökvägar i en grupp för volymen.

```bash
#!/bin/bash
$ lvdisplay <volume-group-name from the pvs command’s results>
```

Att montera logiska volymerna till sökvägen för ditt val.

```bash
#!/bin/bash
$ mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>För RAID-matriser

Följande kommando visar information om alla raid-diskar.

```bash
#!/bin/bash
$ mdadm –detail –scan
```

 Den relevanta RAID-disken visas som `/dev/mdm/<RAID array name in the protected VM>`

Använd monteringskommandot om RAID-disk har fysiska volymer.

```bash
#!/bin/bash
$ mount [RAID Disk Path] [/mountpath]
```

Om RAID-disk har en annan LVM som konfigurerats i den, sedan använder föregående procedur för LVM partitioner utan volymens namn i stället för RAID diskens namn

## <a name="system-requirements"></a>Systemkrav

### <a name="for-windows-os"></a>För Windows OS

I följande tabell visar kompatibiliteten mellan servern och datorn operativsystem. När du återställer filer, kan du återställa filer till en tidigare och framtida operativsystemversion. Exempelvis kan du inte återställa en fil från en Windows Server 2016 VM till Windows Server 2012 eller en dator med Windows 8. Du kan återställa filer från en virtuell dator till samma server-operativsystem eller operativsystemet kompatibel klient.

|Serveroperativsystem | Kompatibel klient-OS  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>För Linux OS

I Linux, måste Operativsystemet på den dator som används för att återställa filer stödja filsystemet på den skyddade virtuella datorn. Kontrollera att datorn har ett kompatibelt operativsystem och använder en av de versioner som anges i tabellen nedan när du väljer en dator för att köra skriptet:

|Linux OS | Versioner  |
| --------------- | ---- |
| Ubuntu | 12.04 och senare |
| CentOS | 6.5 och senare  |
| RHEL | 6.7 och senare |
| Debian | 7 och senare |
| Oracle Linux | 6.4 och senare |
| SLES | 12 och senare |
| openSUSE | 42.2 och senare |

Skriptet kräver också Python och bash komponenter för att köra och Anslut säkert till återställningspunkten.

|Komponent | Version  |
| --------------- | ---- |
| Bash | 4 och senare |
| python | 2.6.6 och senare  |
| TLS | 1.2 ska stödjas  |

## <a name="troubleshooting"></a>Felsökning

Om du har problem vid återställning av filer från de virtuella datorerna, kontrollerar du i följande tabell för ytterligare information.

| Felmeddelande / Scenario | Möjlig orsak | Rekommenderad åtgärd |
| ------------------------ | -------------- | ------------------ |
| Exe-utdata: *undantag som ansluter till mål* |Skriptet kan inte komma åt återställningspunkten    | Kontrollera om datorn uppfyller de tidigare åtkomstkraven. |  
| Exe-utdata: *målet har redan loggats in via en iSCSI-session.* | Skriptet har redan gjorts på samma dator och enheterna som har bifogats | Volymer för återställningspunkten har redan bifogats. De kan inte monteras med samma enhetsbeteckningar för den ursprungliga virtuella datorn. Bläddra igenom alla tillgängliga volymer i Utforskaren-filen |
| Exe-utdata: *det här skriptet är ogiltig eftersom diskarna har demonterats via portalen/överskred 12-hr gränsen. Hämta ett nytt skript från portalen.* |    Diskarna har demonterats från portalen eller den 12 – hr har överskridits | Den här specifika exe nu är ogiltig och kan inte köras. Om du vill komma åt den recovery point-in-time-filer, gå till portalen för en ny exe-fil|
| På den dator där den exe-filen kör: nya volymer är inte demonteras när du klickar på knappen dismount | ISCSI-initieraren på datorn inte svarar/uppdatera sin anslutning till målet och underhålla cachen |    Vänta några minuter efter att demontera knappen trycks. Om de nya volymerna inte är fortfarande demonteras, bläddra igenom alla volymer. Detta gör att initieraren att uppdatera anslutningen och volymen demonteras med ett felmeddelande att disken inte är tillgänglig|
| Exe-utdata: skript köras men ”nya volymer ansluten” visas inte på utdata från skriptet |    Det här är ett tillfälligt fel    | Volymerna skulle har redan bifogats. Öppna Utforskaren och Bläddra. Om du använder samma dator för att köra skript varje gång, Överväg att starta om datorn och listan ska visas i efterföljande exe-körningar. |
| Linux specifikt: går inte att visa de önskade volymerna | Operativsystemet där skriptet körs kanske inte kan identifiera det underliggande filsystemet för skyddad virtuell dator | Kontrollera om krascher konsekvent eller filkonsekvent återställningspunkten. Om filen konsekvent, kör skriptet på en annan dator vars operativsystem kan du identifierar den skyddade Virtuella datorns filsystem |
| Windows-specifika: går inte att visa de önskade volymerna | Diskarna har bifogats men volymerna har inte konfigurerats | Identifiera ytterligare diskar rör återställningspunkten från hanteringsskärmen disk. Om något av dessa diskar är offline tillstånd försök att göra dem online genom att högerklicka på disken och klicka på ”Online”|
