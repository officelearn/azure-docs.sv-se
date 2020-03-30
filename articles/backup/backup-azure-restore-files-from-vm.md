---
title: Återställa filer och mappar från azure vm-säkerhetskopiering
description: I den här artikeln kan du läsa om hur du återställer filer och mappar från en Azure-återställningspunkt för virtuella datorer.
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 0e3061ea8fc26adcf39fe415cd9a662de739543a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273311"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Återställa filer från säkerhetskopiering av virtuella azure-datorer

Azure Backup innehåller möjlighet att återställa [virtuella Azure-datorer (VIRTUELLA datorer) och diskar](./backup-azure-arm-restore-vms.md) från Azure VM-säkerhetskopior, även kallade återställningspunkter. I den här artikeln beskrivs hur du återställer filer och mappar från en säkerhetskopia av Azure VM. Återställning av filer och mappar är endast tillgängligt för virtuella Azure-datorer som distribueras med hjälp av Resource Manager-modellen och skyddas till ett recovery-tjänstvalv.

> [!NOTE]
> Den här funktionen är tillgänglig för virtuella Azure-datorer som distribueras med hjälp av Resource Manager-modellen och skyddas till ett Recovery Services-valv.
> Filåterställning från en krypterad vm-säkerhetskopia stöds inte.
>

## <a name="mount-the-volume-and-copy-files"></a>Montera volymen och kopiera filer

Om du vill återställa filer eller mappar från återställningspunkten går du till den virtuella datorn och väljer önskad återställningspunkt.

1. Logga in på [Azure-portalen](https://portal.Azure.com) och klicka på **Virtuella datorer**i den vänstra rutan . Välj den virtuella datorn för att öppna instrumentpanelen för den virtuella datorn i listan över virtuella datorer.

2. Öppna **instrumentpanelen Säkerhetskopiering** på den virtuella datorns meny.

    ![Öppna säkerhetsobjekt för Återställningstjänster i valvet](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. Klicka på Återställning av säkerhetskopiering på instrumentpanelsmenyn För **säkerhetskopiering.**

    ![Knappen Återställning av filer](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Menyn **Filåterställning** öppnas.

    ![Menyn Återställning av filer](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. På den nedrullningsbara menyn **Välj återställningspunkt** väljer du den återställningspunkt som innehåller de filer du vill använda. Som standard är den senaste återställningspunkten redan markerad.

5. Om du vill hämta programvaran som används för att kopiera filer från återställningspunkten klickar du på **Hämta körbar** (för Virtuella datorer med Windows Azure) eller **Hämta skript** (för virtuella Linux Azure-datorer genereras ett pythonskript).

    ![Genererat lösenord](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure hämtar det körbara eller skriptet till den lokala datorn.

    ![hämta meddelande för den körbara filen eller skriptet](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Om du vill köra den körbara filen eller skriptet som administratör föreslås att du ska spara den nedladdade filen på datorn.

6. Den körbara filen eller skriptet är lösenordsskyddad och kräver ett lösenord. Klicka på kopieringsknappen på menyn **Filåterställning** för att läsa in lösenordet i minnet.

    ![Genererat lösenord](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Från hämtningsplatsen (vanligtvis mappen Nedladdningar) högerklickar du på det körbara eller skriptet och kör det med administratörsautentiseringsuppgifter. När du uppmanas till det skriver du lösenordet eller klistrar in lösenordet från minnet och trycker på **Retur**. När det giltiga lösenordet har angetts ansluter skriptet till återställningspunkten.

    ![Menyn Återställning av filer](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. För Linux-datorer genereras ett pythonskript. Man måste ladda ner skriptet och kopiera den till relevant / kompatibel Linux-server. Du kan behöva ändra behörigheterna för ```chmod +x <python file name>```att köra den med . Kör sedan python-filen med ```./<python file name>```.

I avsnittet [Åtkomstkrav](#access-requirements) för att se till att skriptet körs.

### <a name="identifying-volumes"></a>Identifiera volymer

#### <a name="for-windows"></a>För Windows

När du kör den körbara filen monteras de nya volymerna och enhetsbeteckningar tilldelas. Du kan använda Utforskaren eller Utforskaren för att bläddra bland dessa enheter. De enhetsbeteckningar som tilldelats volymerna kanske inte är samma bokstäver som den ursprungliga virtuella datorn. Volymnamnet bevaras dock. Om volymen på den ursprungliga virtuella datorn till exempel`\`var "Data Disk (E: )", kan den volymen kopplas`\`på den lokala datorn som "Datadisk ("Valfri bokstav": ). Bläddra igenom alla volymer som nämns i skriptutdata tills du hittar dina filer eller mappar.  

   ![Menyn Återställning av filer](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>För Linux

I Linux monteras volymerna för återställningspunkten i mappen där skriptet körs. De anslutna diskarna, volymerna och motsvarande monteringsbanor visas i enlighet med detta. Dessa monteringsbanor är synliga för användare som har åtkomst till rotnivå. Bläddra igenom de volymer som nämns i skriptutdata.

  ![Återställningsmeny för Linux-filer](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Stänga anslutningen

När du har identifierat filerna och kopierat dem till en lokal lagringsplats tar du bort (eller avmonterar) de ytterligare enheterna. Om du vill avmontera enheterna klickar du på **Avmontera diskar**på menyn **Filåterställning** i Azure-portalen .

![Avmontera diskar](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

När diskarna har avmonterats får du ett meddelande. Det kan ta några minuter innan anslutningen uppdateras så att du kan ta bort diskarna.

I Linux, efter anslutningen till återställningspunkten är avskuren, tar operativsystemet inte bort motsvarande monteringsvägar automatiskt. Monteringsbanorna finns som "överblivna" volymer och är synliga, men genererar ett fel när du öppnar/skriver filerna. De kan tas bort manuellt. Skriptet, när det körs, identifierar sådana volymer som finns från tidigare återställningspunkter och rensar upp dem efter samtycke.

## <a name="special-configurations"></a>Särskilda konfigurationer

### <a name="dynamic-disks"></a>Dynamiska diskar

Om den skyddade Azure VM har volymer med en eller båda av följande egenskaper, kan du inte köra det körbara skriptet på samma virtuella dator.

- Volymer som sträcker sig över flera diskar (spänns över och stripe-volymer)
- Feltoleranta volymer (speglade och RAID-5 volymer) på dynamiska diskar

Kör i stället det körbara skriptet på en annan dator med ett kompatibelt operativsystem.

### <a name="windows-storage-spaces"></a>Windows Storage Spaces

Windows Storage Spaces är en Windows-teknik som gör att du kan virtualisera lagring. Med Windows Storage Spaces kan du gruppera diskar av branschstandard i lagringspooler. Sedan använder du det tillgängliga utrymmet i dessa lagringspooler för att skapa virtuella diskar, så kallade lagringsutrymmen.

Om den skyddade virtuella Azure-datorn använder Windows Storage Spaces kan du inte köra det körbara skriptet på samma virtuella dator. Kör i stället det körbara skriptet på alla andra datorer med ett kompatibelt operativsystem.

### <a name="lvmraid-arrays"></a>LVM/RAID-matriser

I Linux används LVM (Logical Volume Manager) och/eller RAID-matriser för logiska volymer för att hantera logiska volymer över flera diskar. Om den skyddade Virtuella Linux-datorn använder LVM- och/eller RAID-matriser kan du inte köra skriptet på samma virtuella dator. Kör i stället skriptet på en annan dator med ett kompatibelt operativsystem och som stöder filsystemet för den skyddade virtuella datorn.

Följande skriptutdata visar LVM- och/eller RAID-matrisdiskarna och volymerna med partitionstypen.

   ![Menyn Linux LVM-utdata](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Om du vill ansluta dessa partitioner kör du kommandona i följande avsnitt.

#### <a name="for-lvm-partitions"></a>För LVM-partitioner

Så här listar du volymgruppsnamnen under en fysisk volym:

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

Så här listar du alla logiska volymer, namn och deras sökvägar i en volymgrupp:

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command's results>
```

Så här monterar du de logiska volymerna till valfri bana:

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>För RAID-matriser

Följande kommando visar information om alla raid-diskar:

```bash
#!/bin/bash
mdadm –detail –scan
```

 Den relevanta RAID-disken visas som`/dev/mdm/<RAID array name in the protected VM>`

Använd kommandot montera om RAID-disken har fysiska volymer:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Om RAID-disken har en annan LVM konfigurerad i den, använd sedan föregående procedur för LVM-partitioner men använd volymnamnet i stället för RAID-diskens namn.

## <a name="system-requirements"></a>Systemkrav

### <a name="for-windows-os"></a>För Windows OS

I följande tabell visas kompatibiliteten mellan server- och datoroperativsystem. När du återställer filer kan du inte återställa filer till en tidigare eller framtida operativsystemversion. Du kan till exempel inte återställa en fil från en virtuell Dator för Windows Server 2016 till Windows Server 2012 eller en Windows 8-dator. Du kan återställa filer från en virtuell dator till samma serveroperativsystem eller till det kompatibla klientoperativsystemet.

|Server-OS | Kompatibelt klientoperativt operativsystem  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>För Linux OS

I Linux måste operativsystemet för den dator som används för att återställa filer stödja filsystemet för den skyddade virtuella datorn. När du väljer en dator för att köra skriptet kontrollerar du att datorn har ett kompatibelt operativsystem och använder någon av de versioner som identifieras i följande tabell:

|Linux OS | Versioner  |
| --------------- | ---- |
| Ubuntu | 12.04 och uppåt |
| CentOS | 6.5 och högre  |
| RHEL | 6.7 och högre |
| Debian | 7 och uppåt |
| Oracle Linux | 6.4 och högre |
| SLES | 12 och uppåt |
| openSUSE | 42.2 och högre |

> [!NOTE]
> Vi har hittat några problem med att köra filåterställningsskriptet på datorer med SLES 12 SP4 OS och vi undersöker med SLES-teamet.
> För närvarande körs filåterställning skriptet arbetar på maskiner med SLES 12 SP2 och SP3 OS versioner.
>

Skriptet kräver också Python och bash komponenter för att köra och ansluta säkert till återställningspunkten.

|Komponent | Version  |
| --------------- | ---- |
| bash | 4 och uppåt |
| python | 2.6.6 och högre  |
| TLS | 1.2 Bör stödjas  |

## <a name="access-requirements"></a>Åtkomstkrav

Om du kör skriptet på en dator med begränsad åtkomst kontrollerar du att det finns åtkomst till:

- `download.microsoft.com`
- Url:er för återställningstjänsten (geonamn refererar till den region där återställningstjänstvalvet finns)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com`(För offentliga Azure-geos)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn`(För Azure China 21Vianet)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us`(För Azure US Government)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de`(För Azure Tyskland)
- Utgående portar 53 (DNS), 443, 3260

> [!NOTE]
>
> - Det hämtade skriptfilnamnet har **det geonamn som** ska fyllas i url:en. För exampple: Det nedladdade \'skriptnamnet\'\_\'börjar\'med\'VMname geoname _ GUID\', som *ContosoVM_wcus_12345678*
> - Webbadressen skulle <https://pod01-rec2.wcus.backup.windowsazure.com>vara "
>

För Linux kräver skriptet "open-iscsi" och "lshw" komponenter för att ansluta till återställningspunkten. Om komponenterna inte finns på datorn där skriptet körs ber skriptet om tillstånd att installera komponenterna. Ge ditt samtycke till att installera nödvändiga komponenter.

Åtkomsten `download.microsoft.com` till krävs för att hämta komponenter som används för att skapa en säker kanal mellan datorn där skriptet körs och data i återställningspunkten.

Du kan köra skriptet på alla datorer som har samma (eller kompatibla) operativsystem som den säkerhetskopierade virtuella datorn. Se [tabellen Kompatibelt operativsystem](backup-azure-restore-files-from-vm.md#system-requirements) för kompatibla operativsystem. Om den skyddade virtuella Azure-datorn använder Windows Storage Spaces (för Virtuella Windows Azure-datorer) eller LVM/RAID-matriser (för virtuella Linux-datorer) kan du inte köra den körbara eller skriptet på samma virtuella dator. Kör i stället det körbara eller skriptet på en annan dator med ett kompatibelt operativsystem.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Återställning av filer från säkerhetskopiering av virtuella datorer med stora diskar

I det här avsnittet beskrivs hur du utför filåterställning från säkerhetskopior av Virtuella Azure-datorer med mer än 16 diskar och varje diskstorlek är större än 32 TB.

Eftersom filåterställningsförfarandeet kopplar alla diskar från säkerhetskopian, när ett stort antal diskar (>16) eller stora diskar (> 32 TB vardera) används, rekommenderas följande åtgärdspunkter:

- Behåll en separat återställningsserver (Virtuella azure VM D2v3-datorer) för filåterställning. Du kan bara använda det för filåterställning och sedan stänga av den när det inte behövs. Återställning på den ursprungliga datorn rekommenderas inte eftersom det kommer att ha betydande inverkan på själva den virtuella datorn.
- Kör sedan skriptet en gång för att kontrollera om filåterställningsåtgärden lyckas.
- Om filåterställningsprocessen låser sig (diskarna är aldrig monterade eller om de är monterade men volymerna inte visas) utför du följande steg.
  - Om återställningsservern är en Virtuell Windows-dator:
    - Kontrollera att operativsystemet är WS 2012 eller högre.
    - Kontrollera att registernycklarna är inställda enligt vad som föreslås nedan på återställningsservern och se till att starta om servern. Numret bredvid GUID kan variera från 0001-0005. I följande exempel är det 0004. Navigera genom sökvägen till registernyckeln tills parameteravsnittet.

    ![iscsi-reg-key-changes.png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Om återställningsservern är en virtuell Linux-dator:
  - I filen /etc/iscsi/iscsid.conf ändrar du inställningen från:
    - node.conn[0].timeo.noop_out_timeout = 5 till node.conn[0].timeo.noop_out_timeout = 30
- När du har gjort ändringen ovan kör du skriptet igen. Med dessa ändringar är det mycket troligt att filåterställning kommer att lyckas.
- Varje gång användaren hämtar ett skript initierar Azure Backup processen att förbereda återställningspunkten för nedladdning. Med stora diskar kommer den här processen att ta lång tid. Om det finns successiva skurar av förfrågningar, kommer målet förberedelse gå in i en nedladdning spiral. Därför rekommenderas att ladda ner ett skript från Portal / Powershell / CLI, vänta i 20-30 minuter (en heuristisk) och sedan köra den. Vid det här laget förväntas målet vara klart för anslutning från skriptet.
- När du har hämtat filen kontrollerar du att du går tillbaka till portalen och klickar på **Avmontera diskar för återställningspunkter** där du inte kunde montera volymer. I huvudsak kommer detta steg att rensa alla befintliga processer / sessioner och öka risken för återhämtning.

## <a name="troubleshooting"></a>Felsökning

Om du har problem när du återställer filer från de virtuella datorerna kontrollerar du i följande tabell för ytterligare information.

| Felmeddelande / Scenario | Trolig orsak | Rekommenderad åtgärd |
| ------------------------ | -------------- | ------------------ |
| Exe-utdata: *Undantag som fångats vid anslutning till mål* | Skriptet kan inte komma åt återställningspunkten    | Kontrollera om maskinen uppfyller [de tidigare åtkomstkraven](#access-requirements). |  
| Exe-utdata: *Målet har redan loggats in via en iSCSI-session.* | Skriptet har redan körts på samma dator och enheterna har anslutits | Volymerna för återställningspunkten har redan bifogats. De får INTE monteras med samma enhetsbeteckningar för den ursprungliga virtuella datorn. Bläddra igenom alla tillgängliga volymer i utforskaren för filen. |
| Exe-utdata: *Det här skriptet är ogiltigt eftersom diskarna har demonterats via portal/överskridit 12-timmarsgränsen. Ladda ner ett nytt skript från portalen.* |    Diskarna har demonterats från portalen eller så har 12-timmarsgränsen överskridits | Den här exe är nu ogiltig och kan inte köras. Om du vill komma åt filerna för återställningspunkten besöker du portalen för ett nytt ex.|
| På datorn där exe körs: De nya volymerna demonteras inte när demonteringsknappen har klickats på | ISCSI-initieraren på datorn svarar/uppdaterar inte anslutningen till målet och underhåller cachen. |  När du har klickat på **Demontera**väntar du några minuter. Om de nya volymerna inte demonteras bläddrar du igenom alla volymer. Genom att bläddra i alla volymer tvingas initieraren att uppdatera anslutningen och volymen demonteras med ett felmeddelande om att disken inte är tillgänglig.|
| Exe-utdata: Skriptet körs men "Nya volymer bifogas" visas inte på skriptutdata |    Detta är ett tillfälligt fel    | Volymerna har redan bifogats. Öppna Utforskaren för att bläddra. Om du använder samma dator för att köra skript varje gång bör du överväga att starta om datorn och listan ska visas i de efterföljande exe-körningarna. |
| Linux-specifika: Kan inte visa önskade volymer | Operativsystemet för den dator där skriptet körs kanske inte känner igen det underliggande filsystemet för den skyddade virtuella datorn | Kontrollera om återställningspunkten är kraschkonsekvent eller filkonsekvent. Om filen är konsekvent kör du skriptet på en annan dator vars operativsystem känner igen den skyddade virtuella datorns filsystem. |
| Windows-specifik: Kan inte visa önskade volymer | Diskarna kan ha anslutits men volymerna har inte konfigurerats | Identifiera ytterligare diskar som är relaterade till återställningspunkten på diskhanteringsskärmen. Om någon av dessa diskar är i ett offline-tillstånd, prova att föra dem online genom att högerklicka på disken och klicka på **Online**.|

## <a name="security"></a>Säkerhet

I det här avsnittet beskrivs de olika säkerhetsåtgärder som vidtagits för implementering av filåterställning från Azure VM-säkerhetskopior.

### <a name="feature-flow"></a>Funktionsflöde

Den här funktionen har skapats för att komma åt VM-data utan att behöva återställa hela VM- eller VM-diskarna och med det minsta antalet steg. Åtkomst till VM-data tillhandahålls av ett skript (som monterar återställningsvolymen när den körs enligt nedan) och utgör hörnstenen i alla säkerhetsimplementeringar:

  ![Flöde av säkerhetsfunktion](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Implementeringar av säkerhet

#### <a name="select-recovery-point-who-can-generate-script"></a>Välj Återställningspunkt (vem kan generera skript)

Skriptet ger åtkomst till VM-data, så det är viktigt att reglera vem som kan generera den i första hand. Du måste logga in på Azure-portalen och vara [RBAC auktoriserad](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) för att generera skriptet.

Återställning av filer kräver samma auktoriseringsnivå som krävs för återställning av virtuella datorer och diskåterställning. Med andra ord kan endast behöriga användare visa VM-data generera skriptet.

Det genererade skriptet är signerat med det officiella Microsoft-certifikatet för Azure Backup-tjänsten. All manipulering av skriptet innebär att signaturen är bruten och alla försök att köra skriptet markeras som en potentiell risk av operativsystemet.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Mount Recovery-volym (som kan köra skript)

Endast en administratör kan köra skriptet och det ska köras i förhöjdt läge. Skriptet kör bara en förgenererad uppsättning steg och accepterar inte indata från någon extern källa.

För att köra skriptet krävs ett lösenord som bara visas för den behöriga användaren vid tidpunkten för generering av skript i Azure-portalen eller PowerShell/CLI. Detta för att säkerställa att den behöriga användaren som hämtar skriptet också är ansvarig för att köra skriptet.

#### <a name="browse-files-and-folders"></a>Bläddra bland filer och mappar

För att bläddra bland filer och mappar använder skriptet iSCSI-initieraren i datorn och ansluter till återställningspunkten som har konfigurerats som ett iSCSI-mål. Här kan du föreställa dig scenarier där man försöker imitera / parodi antingen / alla komponenter.

Vi använder en ömsesidig CHAP-autentiseringsmekanism så att varje komponent autentiserar den andra. Detta innebär att det är extremt svårt för en falsk initiativtagare att ansluta till iSCSI målet och för en falsk mål som ska anslutas till maskinen där skriptet körs.

Dataflödet mellan återställningstjänsten och datorn skyddas genom att bygga en säker TLS-tunnel över TCP[(TLS 1.2 bör stödjas](#system-requirements) i datorn där skriptet körs).

Alla ACL (File Access Control List) som finns i den överordnade/säkerhetskopierade virtuella datorn bevaras också i det monterade filsystemet.

Skriptet ger skrivskyddad åtkomst till en återställningspunkt och är giltigt i endast 12 timmar. Om du vill ta bort åtkomsten tidigare loggar du in på Azure Portal/PowerShell/CLI och utför **avmonterade diskar** för just återställningspunkten. Skriptet ogiltigförklaras omedelbart.

## <a name="next-steps"></a>Nästa steg

- Om du har problem när du återställer filer finns i avsnittet [Felsökning](#troubleshooting)
- Lär dig hur du [återställer filer via Powershell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup)
- Lär dig hur du [återställer filer via Azure CLI](https://docs.microsoft.com/azure/backup/tutorial-restore-files)
- När den virtuella datorn har återställts kan du läsa om hur du [hanterar säkerhetskopior](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms)
