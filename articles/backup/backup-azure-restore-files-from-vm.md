---
title: 'Azure-säkerhetskopiering: Återställa filer och mappar från en virtuell dator i Azure-säkerhetskopiering'
description: Återställa filer från en återställningspunkt för virtuell Azure-dator
services: backup
author: pvrk
manager: shivamg
keywords: återställning på objektnivå; filåterställning från Virtuella Azure-säkerhetskopia. återställa filer från Azure VM
ms.service: backup
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: pullabhk
ms.openlocfilehash: 4be1ffcabed6667ab76ec790326a687d75c8b125
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958628"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Återställa filer från en säkerhetskopia av virtuell Azure-dator

Azure-säkerhetskopiering ger möjlighet att återställa [Azure virtuella datorer (VM) och diskar](./backup-azure-arm-restore-vms.md) från Virtuella Azure-säkerhetskopiering, även kallat återställningspunkter. Den här artikeln förklaras hur du återställer filer och mappar från en virtuell dator i Azure-säkerhetskopiering. Återställa filer och mappar är bara tillgängligt för Azure virtuella datorer distribueras med Resource Manager-modellen och skyddas i ett Recovery services-valv.

> [!Note]
> Den här funktionen är tillgänglig för Azure virtuella datorer distribueras med Resource Manager-modellen och skyddas i Recovery Services-valvet.
> Filåterställning från en krypterad säkerhetskopiering av VM stöds inte.
>

## <a name="mount-the-volume-and-copy-files"></a>Montera volymen och kopiera filer

Om du vill återställa filer och mappar från återställningspunkten, gå till den virtuella datorn och välj önskad återställningspunkt. 

1. Logga in på den [Azure-portalen](http://portal.Azure.com) och i den vänstra menyn klickar du på **virtuella datorer**. Välj den virtuella datorn för att öppna instrumentpanelen för den virtuella datorn från listan över virtuella datorer. 

2. Klicka på den virtuella datorns menyn **säkerhetskopiering** att öppna instrumentpanelen för säkerhetskopiering.

    ![Öppna Recovery Services-valvet Säkerhetskopiera objekt](./media/backup-azure-restore-files-from-vm/open-vault-from-vm.png)

3. I menyn säkerhetskopiering instrumentpanelen klickar du på **filåterställning** menyn öppnas.

    ![Arkivmenyn för återställning](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Från den **Välj återställningspunkt** nedrullningsbara menyn, Välj den återställningspunkt som innehåller de filer som du vill använda. Den senaste återställningspunkten har redan valts som standard.

5. Hämta programvara som används för att kopiera filer från återställningspunkten, klicka på **hämta körbara** (för Windows Azure VM) eller **hämta skriptet** (för Linux virtuella Azure-datorn). 

    ![Genererat lösenord](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure laddar ned den körbara filen eller skriptet till den lokala datorn.

    ![Hämta meddelandet för den körbara filen eller skriptet](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Om du vill köra den körbara filen eller skriptet som administratör, rekommenderas du spara filen på datorn.

6. Den körbara filen eller skriptet är lösenordsskyddat och kräver ett lösenord. I den **filåterställning** -menyn, klicka på kopieringsknappen för att läsa in lösenordet i minnet.

    ![Genererat lösenord](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Högerklicka på den körbara filen eller skriptet från nedladdningsplatsen (vanligtvis hämtningsmapp) och kör den med administratörsbehörighet. När du uppmanas, ange lösenordet eller klistra in lösenordet från minnet och tryck på RETUR. När du har angett ett giltigt lösenord, ansluter skriptet till återställningspunkten.

    ![Arkivmenyn för återställning](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Om du kör skriptet på en dator med begränsad åtkomst, se till att det finns åtkomst till:

    - Download.microsoft.com
    - [Azure-slutpunkter som används för Virtuella Azure-säkerhetskopieringar](backup-azure-arm-vms-prepare.md#establish-network-connectivity)
    - utgående port 3260

    För Linux kräver skriptet 'Öppna iscsi' och 'lshw' komponenter för att ansluta till återställningspunkten. Om komponenterna som inte finns på den dator där skriptet körs begär skriptet behörighet att installera komponenterna. Ge ditt medgivande att installera nödvändiga komponenter.
    
    Åtkomst till download.microsoft.com krävs för att hämta komponenter som används för att skapa en säker kanal mellan den dator där skriptet körs och data i återställningspunkten.         

    Du kan köra skriptet på en dator som har samma (eller kompatibla)-operativsystem som den säkerhetskopierade virtuella datorn. Finns det [kompatibel OS tabell](backup-azure-restore-files-from-vm.md#system-requirements) för kompatibla operativsystem. Om den skyddade virtuella Azure-datorn använder lagringsutrymmen för Windows (för Windows Azure VM: ar) eller LVM/RAID-matriser (för virtuella Linux-datorer), kan du inte köra den körbara filen eller skriptet på samma virtuella dator. I stället köra den körbara filen eller skriptet på en dator med ett kompatibelt operativsystem.
 

### <a name="identifying-volumes"></a>Identifiera volymer

#### <a name="for-windows"></a>För Windows

När du kör den körbara filen operativsystemet monterar nya volymer och tilldelar enhetsbeteckningar. Du kan använda Utforskaren eller Utforskaren och bläddra dessa enheter. Enhetsbeteckningar till volymer får inte vara samma bokstäver som den ursprungliga virtuella datorn, men volymnamnet bevaras. Om volymen på den ursprungliga virtuella datorn har till exempel ”datadisk (E:`\`)”, att volymen kan kopplas på den lokala datorn som ”datadisk (alla bokstäver:`\`). Bläddra igenom alla volymer som nämns i utdata från skriptet tills du hittar din mappen.  
       
   ![Arkivmenyn för återställning](./media/backup-azure-restore-files-from-vm/volumes-attached.png)
           
#### <a name="for-linux"></a>För Linux

I Linux monteras volymer för återställningspunkten till mappen där skriptet körs. Därefter visas anslutna diskar, volymer och motsvarande monteringssökvägar. Dessa montera sökvägar är synliga för användare som har nivån rotåtkomst. Bläddra igenom de volymer som nämns i utdata från skriptet.

  ![Återställning av Linux Arkivmenyn](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  

## <a name="closing-the-connection"></a>Stänga anslutningen

Ta bort (eller demontera) på ytterligare enheter när du identifierar filerna och kopiera dem till en lokal lagringsplats. Demontera enheter, på den **filåterställning** -menyn i Azure-portalen klickar du på **demontera diskar**.

![Demontera diskar](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

När diskarna har demonterats, visas ett meddelande som du kan se den lyckades. Det kan ta några minuter innan anslutningen att uppdatera så att du kan ta bort diskarna.

I Linux, när anslutningen till återställningspunkten är fjärrdisken Operativsystemet inte ta bort motsvarande monteringssökvägar automatiskt. Monteringssökvägar finns som ”överblivna” volymer och de visas, men ett fel genereras när du åtkomst och skrivning filerna. De kan ta manuellt bort. Skriptet när den körs identifierar dessa volymer som befintliga från alla tidigare återställningspunkter och rensar dem vid medgivande.

## <a name="special-configurations"></a>Särskilda konfigurationer

### <a name="dynamic-disks"></a>Dynamiska diskar

Om den skyddade virtuella Azure-datorn har volymer med en eller båda av följande egenskaper, kan du inte köra skriptet körbara på samma virtuella dator. 

  - Volymer som sträcker sig över flera diskar (disklänkande och stripe-volymer)
  - Feltoleranta volymer (volymer speglad eller RAID-5) på dynamiska diskar 

I stället köra körbara skriptet på alla datorer med ett kompatibelt operativsystem.

### <a name="windows-storage-spaces"></a>Lagringsutrymmen för Windows

Lagringsutrymmen för Windows är en Windows-teknik som gör det möjligt att virtualisera lagring. Du kan gruppera standarddiskar i lagringspooler med lagringsutrymmen för Windows. Du kan använda det tillgängliga utrymmet i dessa lagringspooler för att skapa virtuella diskar som kallas lagringsutrymmen.

Om den skyddade virtuella Azure-datorn använder lagringsutrymmen i Windows, kan du inte köra skriptet körbara på samma virtuella dator. I stället köra körbara skriptet på en dator med ett kompatibelt operativsystem.

### <a name="lvmraid-arrays"></a>LVM/RAID-matriser

I Linux för logiska volymer (LVM) och/eller programvara RAID-matriser att hantera logiska volymer över flera diskar. Om den skyddade Linux VM använder LVM och/eller RAID-matriser, kan du inte köra skriptet på samma virtuella dator. I stället köra skriptet på en dator med ett kompatibelt operativsystem och som har stöd för filsystemet på den skydda virtuella datorn.

Följande skriptutdata visar LVM och/eller RAID-matriser diskar och volymer med partitionstypen.

   ![Menyn Linux LVM utdata](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)
   
Kör kommandon i följande avsnitt för att göra dessa partitioner online. 

**För LVM partitioner**

Att visa namnen på volym under fysiska enheter.
```
$ pvs <volume name as shown above in the script output> 
```
Att lista alla logiska volymer, namn och sökvägarna i en grupp för volymen.

```
$ lvdisplay <volume-group-name from the pvs command’s results> 
```

Att montera logiska volymer till sökvägen för ditt val.

```
$ mount <LV path> </mountpath>
```



**För RAID-matriser**

Följande kommando visar information om alla raid-diskar.

```
$ mdadm –detail –scan
```
 Relevanta RAID disken visas som `/dev/mdm/<RAID array name in the protected VM>`

Använd monteringskommandot om RAID-disken har fysiska volymer.
```
$ mount [RAID Disk Path] [/mountpath]
```

Om RAID-disken har en annan LVM som konfigurerats i, ska du använda föregående procedur för LVM partitioner men använda volymnamn i stället för namnet på RAID-Disk

## <a name="system-requirements"></a>Systemkrav

### <a name="for-windows"></a>För Windows

I följande tabell visas kompatibiliteten mellan servern och datorn operativsystem. När du återställer filer, kan du återställa filer till en tidigare eller framtida operativsystemversion. Exempelvis kan du inte återställa en fil från en Windows Server 2016 VM till Windows Server 2012 eller en dator med Windows 8. Du kan återställa filer från en virtuell dator på samma server-operativsystem eller kompatibel klientens operativsystem.   

|Server-OS | Kompatibel klient-OS  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>För Linux OS

I Linux stöder OS som används för att återställa filer filsystemet på den skyddade virtuella datorn. När du väljer en dator för att köra skriptet, se till att datorn har ett kompatibelt operativsystem och använder en av de versioner som anges i följande tabell:

|Linux OS | Versioner  |
| --------------- | ---- |
| Ubuntu | 12.04 och senare |
| CentOS | 6.5 och senare  |
| RHEL | 6.7 och senare |
| Debian | 7 och senare |
| Oracle Linux | 6.4 och senare |
| SLES | 12 och senare |
| openSUSE | 42.2 och senare |

Skriptet kräver också Python och bash komponenter som du vill köra på ett säkert sätt ansluta till återställningspunkten.

|Komponent | Version  |
| --------------- | ---- |
| Bash | 4 och senare |
| python | 2.6.6 och senare  |
| TLS | 1.2 ska stödjas  |

## <a name="troubleshooting"></a>Felsökning

Kontrollera i följande tabell för ytterligare information om det uppstår problem vid återställning av filer från de virtuella datorerna.

| Felmeddelande / Scenario | Möjlig orsak | Rekommenderad åtgärd |
| ------------------------ | -------------- | ------------------ |
| Exe utdata: *undantag som ansluter till målet* |Skriptet kan inte komma åt återställningspunkten | Kontrollera om datorn uppfyller de tidigare åtkomstkraven. |  
|   Exe utdata: *mål har redan loggats in via en ISCSI-session.* | Skriptet har körts redan på samma dator och enheterna som har bifogats | Volymer för återställningspunkten har redan bifogats. Kan inte vara monterade med samma enhetsbeteckningar för den ursprungliga virtuella datorn. Bläddra igenom alla tillgängliga volymer i Utforskaren för filen |
| Exe utdata: *skriptet är ogiltigt eftersom diskarna har demonterats via portalen/överskred 12-hr gränsen. Hämta ett nytt skript från portalen.* | Diskarna har demonterats från portalen eller 12 hr överskriden |    Den här viss exe nu är ogiltig och kan inte köras. Om du vill komma åt filerna för att återställa i tidpunkt Besök portalen för en ny exe-filen|
| På den dator där den exe-filen körs: nya volymer är inte demonteras när användaren klickar på knappen dismount |    ISCSI-initieraren på datorn inte svarar/uppdatera sin anslutning till målet och underhålla cachen |    Vänta några minuter efter att demontera knappen är nedtryckt. Om de nya volymerna inte är fortfarande demonteras, bläddra igenom alla volymer. Detta gör att initieraren att uppdatera anslutningen och volymen demonteras med felmeddelandet att disken inte är tillgänglig|
| Exe utdata: skript köras men ”nya volymer ansluten” visas inte på utdata från skriptet | Detta är ett tillfälligt fel   | Volymerna skulle har redan bifogats. Öppna Utforskaren och Bläddra. Om du använder samma dator för att köra skript varje gång du överväga att starta om datorn och listan som ska visas i efterföljande exe-körs. |
| Linux-specifika: går inte att visa önskade volymerna | Operativsystemet på den dator där skriptet körs kanske inte kan identifiera det underliggande filsystemet på den skydda virtuella datorn | Kontrollera om krasch konsekvent eller filkonsekventa återställningspunkten. Om filen konsekvent, kör skriptet på en annan dator vars operativsystem kan du identifierar skyddade Virtuellt filsystem |
| Windows-specifika: går inte att visa önskade volymerna | Diskarna kopplade men volymerna har inte konfigurerats | Identifiera ytterligare diskar rör återställningspunkten från skärmen för hantering av disken. Om någon av dessa diskar är offline tillstånd försök att göra dem online genom att högerklicka på disken och klicka på 'Online'|
