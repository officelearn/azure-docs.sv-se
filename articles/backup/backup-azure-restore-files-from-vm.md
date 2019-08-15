---
title: 'Azure Backup: Återställa filer och mappar från en virtuell Azure-säkerhetskopiering'
description: Återställa filer från en återställnings punkt för en virtuell Azure-dator
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: återställning på objekt nivå; fil återställning från virtuell Azure-säkerhetskopiering; återställa filer från virtuell Azure-dator
ms.service: backup
ms.topic: conceptual
ms.date: 3/01/2019
ms.author: dacurwin
ms.openlocfilehash: 67ba9ba9bc85213ec479a90d205ed9570c62bf8c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954612"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Återställa filer från säkerhets kopiering av virtuella Azure-datorer

Azure Backup ger möjlighet att återställa [virtuella Azure-datorer (VM) och diskar](./backup-azure-arm-restore-vms.md) från virtuella Azure-säkerhetskopieringar, även kallade återställnings punkter. Den här artikeln förklarar hur du återställer filer och mappar från en virtuell Azure-säkerhetskopiering. Det går bara att återställa filer och mappar för virtuella Azure-datorer som distribueras med Resource Manager-modellen och skyddas till ett Recovery Services-valv.

> [!Note]
> Den här funktionen är tillgänglig för virtuella Azure-datorer som distribueras med Resource Manager-modellen och skyddas till ett Recovery Services-valv.
> Fil återställning från en krypterad VM-säkerhetskopiering stöds inte.
>

## <a name="mount-the-volume-and-copy-files"></a>Montera volymen och kopiera filer

Om du vill återställa filer eller mappar från återställnings punkten går du till den virtuella datorn och väljer önskad återställnings punkt.

1. Logga in på [Azure Portal](https://portal.Azure.com) och klicka på **virtuella datorer**i det vänstra fönstret. I listan över virtuella datorer väljer du den virtuella datorn för att öppna den virtuella datorns instrument panel.

2. I den virtuella datorns meny klickar du på **säkerhets kopiering** för att öppna instrument panelen för säkerhets kopiering.

    ![Öppna Recovery Services valv säkerhets kopierings objekt](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. På instrument panelen för säkerhets kopiering klickar du på **fil återställning**.

    ![Knappen fil återställning](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Menyn **fil återställning** öppnas.

    ![Fil återställnings meny](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. I list rutan **Välj återställnings punkt** väljer du den återställnings punkt som innehåller de filer som du vill använda. Som standard är den senaste återställnings punkten redan vald.

5. Om du vill ladda ned program varan som används för att kopiera filer från återställnings punkten klickar du på **Hämta körbar fil** (för Windows Azure VM) eller **Ladda ned skript** (för Linux Azure VM genereras ett Python-skript).

    ![Genererat lösen ord](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure laddar ned den körbara filen eller skriptet till den lokala datorn.

    ![Hämta meddelandet för den körbara filen eller skriptet](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Om du vill köra den körbara filen eller skriptet som administratör rekommenderar vi att du sparar nedladdningen på datorn.

6. Den körbara filen eller skriptet är lösenordsskyddat och kräver ett lösen ord. I **fil** återställnings menyn klickar du på knappen Kopiera för att läsa in lösen ordet i minnet.

    ![Genererat lösen ord](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Från nedladdnings platsen (vanligt vis mappen Hämtade filer) högerklickar du på den körbara filen eller skriptet och kör det med administratörs behörighet. När du uppmanas till det anger du lösen ordet eller klistrar in lösen ordet från minnet och trycker på RETUR. När du har angett ett giltigt lösen ord ansluter skriptet till återställnings punkten.

    ![Fil återställnings meny](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Om du kör skriptet på en dator med begränsad åtkomst kontrollerar du att det finns åtkomst till:

    - download.microsoft.com
    - URL: er för återställnings tjänsten (geo-Name refererar till den region där Recovery Service-valvet finns)
        - https:\//pod01-rec2.geo-Name.backup.windowsazure.com (för offentliga Azure-geografiska områden)
        - https:\//pod01-rec2.geo-Name.backup.windowsazure.cn (för Azure Kina)
        - https:\//pod01-rec2.geo-Name.backup.windowsazure.us (för Azure amerikanska myndigheter)
        - https:\//pod01-rec2.geo-Name.backup.windowsazure.de (för Azure Germany)
    - utgående port 3260

> [!Note]
> 
> * Det nedladdade skript fil namnet kommer att ha **geo-namnet** ifyllt i URL: en. För t. ex.: Det nedladdade skript namnet \'börjar\'med VMName\'\_\'-\'namnet\'_ GUID, som ContosoVM_wcus_12345678....<br><br>
> * URL: en är "https:\//pod01-rec2.wcus.backup.windowsazure.com"


   För Linux kräver skriptet "Open-iSCSI"-och ' lshw '-komponenter för att ansluta till återställnings punkten. Om komponenterna inte finns på den dator där skriptet körs, ställer skriptet efter behörighet att installera komponenterna. Ange medgivande för att installera de nödvändiga komponenterna.

   Åtkomst till download.microsoft.com krävs för att ladda ned komponenter som används för att skapa en säker kanal mellan den dator där skriptet körs och data i återställnings punkten.

   Du kan köra skriptet på vilken dator som helst som har samma (eller kompatibla) operativ system som den säkerhetskopierade virtuella datorn. Se den [kompatibla OS-tabellen](backup-azure-restore-files-from-vm.md#system-requirements) för kompatibla operativ system. Om den skyddade virtuella Azure-datorn använder Windows lagrings utrymmen (för virtuella Windows Azure-datorer) eller LVM/RAID-matriser (för virtuella Linux-datorer) kan du inte köra den körbara filen eller skriptet på samma virtuella dator. Kör i stället den körbara filen eller skriptet på någon annan dator med ett kompatibelt operativ system.

### <a name="identifying-volumes"></a>Identifiera volymer

#### <a name="for-windows"></a>För Windows

När du kör den körbara filen monterar operativ systemet de nya volymerna och tilldelar enhets beteckningar. Du kan använda Utforskaren eller Utforskaren för att bläddra bland enheterna. De enhets beteckningar som är tilldelade till volymerna får inte vara samma bokstäver som den ursprungliga virtuella datorn, men volym namnet bevaras. Om volymen på den ursprungliga virtuella datorn exempelvis var "datadisk (E:`\`)" kan den volymen anslutas på den lokala datorn som "data disk (" valfri bokstav ":`\`). Bläddra igenom alla volymer som anges i skriptets utdata tills du hittar dina filer/mappar.  

   ![Fil återställnings meny](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>För Linux

I Linux monteras volymerna för återställnings punkten på den mapp där skriptet körs. De anslutna diskarna, volymerna och motsvarande monterings Sök vägar visas i enlighet med detta. Dessa monterings Sök vägar visas för användare som har åtkomst till rot nivå. Bläddra igenom de volymer som anges i utdata för skriptet.

  ![Fil återställnings meny för Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Stänger anslutningen

När du har identifierat filerna och kopierat dem till en lokal lagrings plats, ta bort (eller demontera) de ytterligare enheterna. Om du vill demontera enheterna går du till **fil** återställnings menyn i Azure Portal och klickar på **demontera diskar**.

![Demontera diskar](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

När diskarna har demonterats visas ett meddelande. Det kan ta några minuter innan anslutningen har uppdaterats så att du kan ta bort diskarna.

I Linux tar operativ systemet inte bort motsvarande monterings Sök vägar automatiskt när anslutningen till återställnings punkten har brutits. Monterings Sök vägarna finns som "föräldralösa" volymer och de visas, men genererar ett fel när du öppnar/skriver filerna. De kan tas bort manuellt. Skriptet, vid körning, identifierar eventuella sådana volymer som är befintliga från alla tidigare återställnings punkter och rensar dem vid medgivande.

## <a name="special-configurations"></a>Särskilda konfigurationer

### <a name="dynamic-disks"></a>Dynamiska diskar

Om den skyddade virtuella Azure-datorn har volymer med en eller båda av följande egenskaper kan du inte köra det körbara skriptet på samma virtuella dator.

- Volymer som sträcker sig över flera diskar (utsträckta och stripade volymer)
- Feltoleranta volymer (speglade volymer och RAID-5-volymer) på dynamiska diskar

Kör i stället det körbara skriptet på en annan dator med ett kompatibelt operativ system.

### <a name="windows-storage-spaces"></a>Windows lagrings utrymmen

Windows Storage Spaces är en Windows-teknik som gör det möjligt att virtualisera lagring. Med Windows lagrings utrymmen kan du gruppera bransch standard diskar i lagringspooler. Sedan använder du det tillgängliga utrymmet i dessa lagringspooler för att skapa virtuella diskar, som kallas lagrings utrymmen.

Om den skyddade virtuella Azure-datorn använder Windows lagrings utrymmen kan du inte köra det körbara skriptet på samma virtuella dator. Kör i stället det körbara skriptet på en annan dator med ett kompatibelt operativ system.

### <a name="lvmraid-arrays"></a>LVM/RAID-matriser

I Linux används Logical Volume Manager (LVM) och/eller programvaru-RAID-matriser för att hantera logiska volymer över flera diskar. Om den skyddade virtuella Linux-datorn använder LVM-och/eller RAID-matriser kan du inte köra skriptet på samma virtuella dator. Kör i stället skriptet på en annan dator med ett kompatibelt operativ system och som stöder fil systemet på den skyddade virtuella datorn.

Följande skript utdata visar diskarna LVM och/eller RAID-matriser och volymerna med partitionstypen.

   ![Menyn för Linux LVM-utdata](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Kör kommandona i följande avsnitt om du vill ta med dessa partitioner online.

#### <a name="for-lvm-partitions"></a>För LVM-partitioner

För att lista volym grupp namnen under en fysisk volym.

```bash
#!/bin/bash
$ pvs <volume name as shown above in the script output>
```

Om du vill visa en lista över alla logiska volymer, namn och deras sökvägar i en volym grupp.

```bash
#!/bin/bash
$ lvdisplay <volume-group-name from the pvs command’s results>
```

Montera de logiska volymerna till valfri sökväg.

```bash
#!/bin/bash
$ mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>För RAID-matriser

Följande kommando visar information om alla RAID-diskar.

```bash
#!/bin/bash
$ mdadm –detail –scan
```

 Den relevanta RAID-disken visas som`/dev/mdm/<RAID array name in the protected VM>`

Använd monterings kommandot om RAID-disken har fysiska volymer.

```bash
#!/bin/bash
$ mount [RAID Disk Path] [/mountpath]
```

Om RAID-disken har en annan LVM som kon figurer ATS i den, använder du den föregående proceduren för LVM-partitioner men använder volym namnet i stället för RAID-diskens namn

## <a name="system-requirements"></a>Systemkraven

### <a name="for-windows-os"></a>För Windows OS

I följande tabell visas kompatibiliteten mellan server-och dator operativ system. När du återställer filer kan du inte återställa filer till en tidigare eller framtida version av operativ systemet. Du kan till exempel inte återställa en fil från en virtuell Windows Server 2016-dator till Windows Server 2012 eller en dator med Windows 8. Du kan återställa filer från en virtuell dator till samma serveroperativ system eller till kompatibelt klient operativ system.

|Server-OS | Kompatibelt klient operativ system  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>För Linux OS

I Linux måste operativ systemet på datorn som används för att återställa filer ha stöd för fil systemet på den skyddade virtuella datorn. När du väljer en dator för att köra skriptet, se till att datorn har ett kompatibelt operativ system och använder en av de versioner som anges i följande tabell:

|Linux OS | Versioner  |
| --------------- | ---- |
| Ubuntu | 12,04 och uppåt |
| CentOS | 6,5 och uppåt  |
| RHEL | 6,7 och uppåt |
| Debian | 7 och senare |
| Oracle Linux | 6,4 och uppåt |
| SLES | 12 och över |
| openSUSE | 42,2 och uppåt |

> [!Note]
> Vi har hittat några problem med att köra fil återställnings skriptet på datorer med SLES 12 SP4 OS. Undersöka med SLES-teamet.
> För närvarande fungerar det att köra fil återställnings skriptet på datorer med SLES 12 SP2 och SP3 OS-versioner.
>

Skriptet kräver också python-och bash-komponenter för att kunna köra och ansluta säkert till återställnings punkten.

|Komponent | Version  |
| --------------- | ---- |
| bash | 4 och senare |
| python | 2.6.6 och senare  |
| TLS | 1,2 bör stödjas  |

## <a name="troubleshooting"></a>Felsökning

Om du har problem när du återställer filer från de virtuella datorerna kan du läsa följande tabell för ytterligare information.

| Fel meddelande/scenario | Trolig orsak | Rekommenderad åtgärd |
| ------------------------ | -------------- | ------------------ |
| Exe-utdata: *Undantag vid anslutning till målet* |Skriptet kan inte komma åt återställnings punkten    | Kontrol lera om datorn uppfyller de tidigare åtkomst kraven. |  
| Exe-utdata: *Målet har redan loggats in via en iSCSI-session.* | Skriptet har redan körts på samma dator och enheterna har anslutits | Volymerna för återställnings punkten har redan bifogats. De kan inte monteras med samma enhets beteckning på den ursprungliga virtuella datorn. Bläddra igenom alla tillgängliga volymer i Utforskaren för filen |
| Exe-utdata: *Skriptet är ogiltigt eftersom diskarna har demonterats via portalen/överskridit gränsen på 12 timmar. Ladda ned ett nytt skript från portalen.* |    Diskarna har demonterats från portalen eller så har gränsen på 12 timmar överskridits | Denna specifika exe är nu ogiltig och kan inte köras. Om du vill komma åt filerna för återställnings punkten-in-Time går du till portalen för en ny exe|
| På den dator där exe körs: De nya volymerna demonteras inte när du klickar på knappen demontera | ISCSI-initieraren på datorn svarar inte/uppdaterar sin anslutning till målet och upprätthåller cachen. |  När du har klickat på **demontera**väntar du några minuter. Om de nya volymerna inte demonteras kan du bläddra igenom alla volymer. Om du bläddrar bland alla volymer så tvingas initieraren att uppdatera anslutningen och volymen demonteras med ett fel meddelande om att disken inte är tillgänglig.|
| Exe-utdata: Skriptet körs korrekt men "nya volymer anslutna" visas inte i utdata för skriptet |    Detta är ett tillfälligt fel    | Volymerna hade redan kopplats. Öppna Utforskaren för att bläddra. Om du använder samma dator för att köra skript varje gång, bör du överväga att starta om datorn och listan bör visas i efterföljande exe-körningar. |
| Linux-information: Det går inte att Visa önskade volymer | Operativ systemet på datorn där skriptet körs kanske inte känner igen det underliggande fil systemet för den skyddade virtuella datorn | Kontrol lera om återställnings punkten är kompatibel med konsekvens eller fil konsekvent. Om filen är konsekvent kör du skriptet på en annan dator vars operativ system känner igen den skyddade virtuella datorns fil system |
| Windows-information: Det går inte att Visa önskade volymer | Diskarna kan ha anslutits, men volymerna har inte kon figurer ATS | På skärmen disk hantering identifierar du ytterligare diskar som är relaterade till återställnings punkten. Om någon av dessa diskar är i offline-läge kan du prova att göra dem online genom att högerklicka på disken och klicka på "online"|

## <a name="security"></a>Säkerhet

I det här avsnittet beskrivs de olika säkerhets åtgärder som vidtagits för att genomföra fil återställning från virtuella Azure-säkerhetskopieringer, så att användarna är medvetna om säkerhets aspekterna i funktionen.

### <a name="feature-flow"></a>Funktions flöde

Den här funktionen har skapats för att komma åt VM-data utan att behöva återställa hela VM-eller VM-diskar och i minst steg. Åtkomst till VM-data tillhandahålls av ett skript (som monterar återställnings volymen när den körs enligt nedan) och därför utgör den hörnen av alla säkerhets implementeringar

  ![Säkerhets funktions flöde](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Säkerhets implementeringar

#### <a name="select-recovery-point-who-can-generate-script"></a>Välj återställnings punkt (som kan generera skript)

Skriptet ger åtkomst till VM-data, det är viktigt att reglera vem som kan generera den på den första platsen. En måste logga in på Azure Portal och bör vara [RBAC](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) -auktoriserad för att kunna generera skriptet.

Fil återställning måste ha samma behörighets nivå som krävs för återställning av virtuella datorer och diskar. Med andra ord kan endast behöriga användare se de virtuella dator data som kan generera skriptet.

Det genererade skriptet är signerat med officiella Microsoft-certifikat för Azure Backup-tjänsten. Manipulering av skriptet innebär att signaturen är bruten och ett försök att köra skriptet är markerat som en potentiell risk av operativ systemet.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Montera återställnings volym (som kan köra skript)

Endast administratörer kan köra skriptet och köra det med förhöjt läge. Skriptet kör bara en i förväg genererad uppsättning steg och accepterar inte inmatade från externa källor.

För att köra skriptet, kräver ett lösen ord som bara visas för den auktoriserade användaren vid tidpunkten för generering av skript i Azure Portal eller PowerShell/CLI. Detta görs för att säkerställa att den auktoriserade användare som laddar ned skriptet också är ansvarig för att köra skriptet.

#### <a name="browse-files-and-folders"></a>Bläddra bland filer och mappar

För att bläddra bland filer och mappar använder skriptet iSCSI-initieraren på datorn och ansluter till den återställnings punkt som är konfigurerad som ett iSCSI-mål. Här kan du anta scenarier där en försöker imitera/manipulera antingen/alla komponenter.

Vi använder mekanismen för ömsesidig CHAP-autentisering så att varje komponent autentiserar den andra. Det innebär att det är mycket svårt för en falsk initierare att ansluta till iSCSI-målet och att ett beskrivande mål måste vara anslutet till den dator där skriptet körs.

Data flödet mellan återställnings tjänsten och datorn skyddas genom att skapa en säker SSL-tunnel över TCP ([TLS 1,2 bör stödjas](#system-requirements) på den dator där skriptet körs)

Eventuella fil Access Control List (ACL) som finns i den överordnade/säkerhetskopierade virtuella datorn bevaras även i det monterade fil systemet.

Skriptet ger skrivskyddad åtkomst till en återställnings punkt och är bara giltig i 12 timmar. Om användaren vill ta bort åtkomsten tidigare loggar du in på Azure Portal/PowerShell/CLI och utför demonterings **diskarna** för den aktuella återställnings punkten. Skriptet kommer att ogiltig förklaras.
