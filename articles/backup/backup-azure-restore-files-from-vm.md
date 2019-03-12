---
title: 'Azure Backup: Återställa filer och mappar från en virtuell Azure-säkerhetskopiering'
description: Återställa filer från en återställningspunkt för virtuella Azure-datorer
services: backup
author: pvrk
manager: shivamg
keywords: återställning på objektnivå; filåterställning från virtuell Azure-säkerhetskopiering; återställa filer från virtuella Azure-datorer
ms.service: backup
ms.topic: conceptual
ms.date: 3/01/2019
ms.author: pullabhk
ms.openlocfilehash: 9ab271943d33ad63be0fae705ac703d1d98524ef
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542513"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Återställa filer från säkerhetskopiering av Azure virtuella datorer

Azure Backup ger möjlighet att återställa [Azure virtuella datorer (VM) och diskar](./backup-azure-arm-restore-vms.md) från Virtuella Azure-säkerhetskopieringar, även kallat återställningspunkter. Den här artikeln förklaras hur du återställer filer och mappar från en virtuell Azure-säkerhetskopiering. Återställa filer och mappar är endast tillgänglig för Azure virtuella datorer distribueras med hjälp av Resource Manager-modellen och skyddas i ett Recovery services-valv.

> [!Note]
> Den här funktionen är tillgänglig för Azure virtuella datorer distribueras med hjälp av Resource Manager-modellen och skyddas i ett Recovery Services-valv.
> Filåterställning från en krypterad säkerhetskopiering av virtuella datorer stöds inte.
>

## <a name="mount-the-volume-and-copy-files"></a>Montera volymen och kopiera filer

Om du vill återställa filer och mappar från återställningspunkten, gå till den virtuella datorn och välj önskad återställningspunkt.

1. Logga in på den [Azure-portalen](https://portal.Azure.com) och i den vänstra rutan klickar du på **virtuella datorer**. Välj den virtuella datorn och öppna instrumentpanelen för den virtuella datorn från listan över virtuella datorer.

2. Klicka på den virtuella datorns menyn **Backup** att öppna instrumentpanelen för säkerhetskopiering.

    ![Öppna Recovery Services-valv säkerhetskopieringsobjekt](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. I menyn Backup instrumentpanelen klickar du på **filåterställning**.

    ![Knappen för återställning av fil](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Den **filåterställning** menyn öppnas.

    ![Arkiv-menyn för återställning](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Från den **Välj återställningspunkt** nedrullningsbara menyn, Välj den återställningspunkt som innehåller de filer som du vill. Den senaste återställningspunkten har redan valts som standard.

5. Om du vill hämta programvaran som används för att kopiera filer från återställningspunkten klickar du på **ladda ned körbar fil** (för Windows Azure-VM) eller **ladda ned skriptet** (för Linux Azure-dator, en python-skriptet genereras).

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

> [!Note]
>
* Hämtade skriptfilnamnet har den **geo-name** fyllas i URL: en. För t.ex.: Hämtade skriptets namn som börjar med \'VMname\'\_\'geoname\'_\'GUID\', som ContosoVM_wcus_12345678...<br><br>
* URL: en är ”https://pod01-rec2.wcus.backup.windowsazure.com”


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

När diskarna har demonterats, får du ett meddelande. Det kan ta några minuter innan anslutningen att uppdatera så att du kan ta bort diskarna.

I Linux, när anslutningen till återställningspunkten är avskiljes Operativsystemet tar inte bort motsvarande monteringssökvägar automatiskt. Monteringssökvägar finns som ”överblivna” volymer och de är synliga men returnerade ett fel när du åtkomst/Skriv-filerna. De kan tas bort manuellt. Skriptet när den körs identifierar dessa volymer som befintlig från alla tidigare återställningspunkterna och rensar dem vid godkännande.

## <a name="special-configurations"></a>Särskilda konfigurationer

### <a name="dynamic-disks"></a>Dynamic Disks

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

|Server OS | Kompatibel klient-OS  |
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

> [!Note]
> Vi har hittat några problem i filen recovery skriptet har körts på virtuella datorer med SLES 12 SP4 OS. För att undersöka med SLES-teamet.
> För närvarande, körs fungerar filåterställningsskript på virtuella datorer med SLES 12 SP2 och SP3 OS-versioner.
>

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
| Exe-utdata: *Undantag som ansluter till mål* |Skriptet kan inte komma åt återställningspunkten    | Kontrollera om datorn uppfyller de tidigare åtkomstkraven. |  
| Exe-utdata: *Målet har redan loggats in via en iSCSI-session.* | Skriptet har redan gjorts på samma dator och enheterna som har bifogats | Volymer för återställningspunkten har redan bifogats. De kan inte monteras med samma enhetsbeteckningar för den ursprungliga virtuella datorn. Bläddra igenom alla tillgängliga volymer i Utforskaren-filen |
| Exe-utdata: *Det här skriptet är ogiltig eftersom diskarna har demonterats via portalen/överskred 12-hr gränsen. Hämta ett nytt skript från portalen.* |    Diskarna har demonterats från portalen eller den 12 – hr har överskridits | Den här specifika exe nu är ogiltig och kan inte köras. Om du vill komma åt den recovery point-in-time-filer, gå till portalen för en ny exe-fil|
| Kör på datorn där den exe-filen är: De nya volymerna är inte demonteras när du klickar på knappen dismount | ISCSI-initieraren på datorn inte svarar/uppdatera sin anslutning till målet och underhålla cachen. |  När du klickar på **demontera**, Vänta några minuter. Om de nya volymerna inte är demonteras, bläddra igenom alla volymer. Bläddra alla volymer tvingar initieraren att uppdatera anslutningen och volymen demonteras med ett felmeddelande att disken inte är tillgänglig.|
| Exe-utdata: Skriptet har körts men ”nya volymer ansluten” visas inte på utdata från skriptet |    Det här är ett tillfälligt fel    | Volymerna skulle har redan bifogats. Öppna Utforskaren och Bläddra. Om du använder samma dator för att köra skript varje gång, Överväg att starta om datorn och listan ska visas i efterföljande exe-körningar. |
| Linux specifika: Det går inte att visa de önskade volymerna | Operativsystemet där skriptet körs kanske inte kan identifiera det underliggande filsystemet för skyddad virtuell dator | Kontrollera om krascher konsekvent eller filkonsekvent återställningspunkten. Om filen konsekvent, kör skriptet på en annan dator vars operativsystem kan du identifierar den skyddade Virtuella datorns filsystem |
| Windows specifika: Det går inte att visa de önskade volymerna | Diskarna har bifogats men volymerna har inte konfigurerats | Identifiera ytterligare diskar rör återställningspunkten från hanteringsskärmen disk. Om något av dessa diskar är offline tillstånd försök att göra dem online genom att högerklicka på disken och klicka på ”Online”|

## <a name="security"></a>Säkerhet

Det här avsnittet beskrivs de olika security-åtgärder som vidtagits för att genomföra filåterställning från Virtuella Azure-säkerhetskopieringar så att användarna är medvetna om säkerhetsaspekter i funktionen.

### <a name="feature-flow"></a>Funktionen flöde

Den här funktionen har skapats för att komma åt virtuella datorns data utan att behöva återställa hela virtuella datorn eller Virtuella diskar och i minsta steg. Åtkomst till VM-data tillhandahålls av ett skript (montera återställningsvolymen när kör som visas nedan) och kan därför den utgör en hörnsten i alla säkra implementeringar

  ![Security funktionen flöde](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementeringar av säkerhet

#### <a name="select-recovery-point-who-can-generate-script"></a>Välj återställningspunkt (som kan generera skript)

Skriptet ger åtkomst till data för virtuell dator, är det viktigt att reglera vem som kan generera i första hand. En behöver logga in på Azure-portalen och bör vara [RBAC behörighet](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) för att kunna generera skript.

Filåterställning behöver samma nivå av auktorisering som krävs för återställning av virtuella datorer och diskar återställning. Med andra ord bara behöriga användare kan visa virtuella datorns data kan generera skriptet.

Det här genererade skriptet har registrerats med officiella Microsoft-certifikat för Azure Backup-tjänsten. Obehörig ändring skriptet innebär att signaturen är bruten och alla försök att köra skriptet markeras som en möjlig risk med Operativsystemet.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Montera återställningsvolymen (som kan köra skript)

Endast administratören kan köra skriptet och ska köras med förhöjd. Skriptet endast kör en förgenererade uppsättning steg och tar inte emot indata från en extern källa.

Om du vill köra skriptet, kräver en ett lösenord som endast visas till behöriga användare vid tidpunkten för generering av skript i Azure portal eller PowerShell/CLI. Detta är att se till att den auktoriserade användare som laddar ned skriptet också för att köra skriptet.

#### <a name="browse-files-and-folders"></a>Bläddra bland filer och mappar

Om du vill bläddra bland filer och mappar, skriptet använder iSCSI-initieraren på datorn och ansluta till den återställningspunkt som är konfigurerad som en iSCSI-mål. Här kan en anta scenarier där en försöker att imitera/spoof antingen/alla komponenter.

Vi använder ömsesidig CHAP-autentiseringsmekanismen så att varje komponent autentiserar den andra. Det innebär att det är mycket svårt för en falsk initierare kan ansluta till iSCSI-målet och ett falska mål som är anslutna till datorn där skriptet körs.

Dataflödet mellan recovery-tjänsten och datorn skyddas genom att skapa en säker SSL-tunnel via TCP ([TLS 1.2 ska stödjas](#system-requirements) på den dator där skriptet körs)

Alla filer åtkomstkontrollistan (ACL) finns i det överordnade/säkerhetskopierat virtuella datorn bevaras också i det monterade filsystemet.

Skriptet ger läsbehörighet till en återställningspunkt och gäller endast 12 timmar. Om du vill ta bort åtkomsten tidigare logga sedan in i Azure Portal/PowerShell/CLI och utföra de **demontera diskar** för den specifika återställningspunkten. Skriptet kommer att ogiltigförklaras omedelbart.
