---
title: Säkerhetskopiera filer och mappar - vanliga frågor
description: Behandlar vanliga frågor om säkerhetskopiering av filer och mappar med Azure Backup.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 6e9f265672ff15e40444a46a3e440e73a0051a5b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254758"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Vanliga frågor om säkerhetskopiering av filer och mappar

Den här artikeln besvarar vanliga frågor som finns i överflöd säkerhetskopiera filer och mappar med Microsoft Azure Recovery Services (MARS) Agent i [Azure Backup-tjänsten.](backup-overview.md)

## <a name="configure-backups"></a>Konfigurera säkerhetskopior

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Var kan jag ladda ner den senaste versionen av MARS-agenten?

Den senaste MARS-agenten som används vid säkerhetskopiering av Windows Server-datorer, System Center DPM och Microsoft Azure Backup-server kan [hämtas](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Hur länge är valvautentiseringsuppgifter giltiga?

Valvautentiseringsuppgifter upphör att gälla efter 10 dagar. Om autentiseringsfilen upphör att gälla hämtar du filen igen från Azure-portalen.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Från vilka enheter kan jag säkerhetskopiera filer och mappar?

Du kan inte säkerhetskopiera följande typer av enheter och volymer:

* Flyttbara media: Alla källor för säkerhetskopieringsobjekt måste rapportera som åtgärdat.
* Skrivskyddade volymer: Volymen måste vara skrivbar för att volymen skuggkopieringstjänst (VSS) ska fungera.
* Offlinevolymer: Volymen måste vara online för att VSS ska fungera.
* Nätverksresurser: Volymen måste vara lokal för servern för att säkerhetskopieras med säkerhetskopiering online.
* BitLocker-skyddade volymer: Volymen måste låsas upp innan säkerhetskopieringen kan ske.
* Identifiering av filsystem: NTFS är det enda filsystem som stöds.

### <a name="what-file-and-folder-types-are-supported"></a>Vilka fil- och mapptyper stöds?

[Läs mer](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) om vilka typer av filer och mappar som stöds för säkerhetskopiering.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Kan jag använda MARS-agenten för att säkerhetskopiera filer och mappar på en virtuell Azure-dator?  

Ja. Azure Backup tillhandahåller säkerhetskopiering på VM-nivå för Virtuella Azure-datorer med vm-tillägget för Azure VM-agenten. Om du vill säkerhetskopiera filer och mappar på gästoperativsystemet Windows på den virtuella datorn kan du installera MARS-agenten för att göra det.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Kan jag använda MARS-agenten för att säkerhetskopiera filer och mappar på tillfällig lagring för Den virtuella Azure-datorn?

Ja. Installera MARS-agenten och säkerhetskopiera filer och mappar på gästoperativsystemet Windows till tillfällig lagring.

* Säkerhetskopieringsjobb misslyckas när tillfälliga lagringsdata raderas.
* Om temporära lagringsdata tas bort kan du bara återställa till icke-flyktig lagring.

### <a name="how-do-i-register-a-server-to-another-region"></a>Hur registrerar jag en server till en annan region?

Säkerhetskopieringsdata skickas till datacentret i valvet där servern är registrerad. Det enklaste sättet att ändra datacentret är att avinstallera och installera om agenten och sedan registrera datorn till ett nytt valv i den region du behöver.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Stöder MARS-agenten Windows Server 2012-deduplicering?

Ja. MARS-agenten konverterar deduplicerade data till normala data när den förbereder säkerhetskopieringen. Den optimerar sedan data för säkerhetskopiering, krypterar data och skickar sedan krypterade data till valvet.

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>Behöver jag administratörsbehörighet för att installera och konfigurera MARS-agenten?

Ja, installationen av MARS-agenten och konfigurationen av säkerhetskopior med MARS-konsolen behöver användaren som lokal administratör på den skyddade servern.

## <a name="manage-backups"></a>Hantera säkerhetskopior

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Vad händer om jag byter namn på en Windows-dator som konfigurerats för säkerhetskopiering?

När du byter namn på en Windows-dator stoppas alla för närvarande konfigurerade säkerhetskopior.

* Du måste registrera det nya datornamnet med säkerhetskopieringsvalvet.
* När du registrerar det nya namnet med valvet är den första åtgärden en *fullständig* säkerhetskopia.
* Om du behöver återställa data som säkerhetskopieras till valvet med det gamla servernamnet använder du alternativet för att återställa till en annan plats i guiden Återställ data. [Läs mer](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Vilken är den maximala filsökvägslängden för säkerhetskopiering?

MARS-agenten förlitar sig på NTFS och använder filsökvägslängdsspecifikationen som begränsas av [Windows API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Om de filer du vill skydda är längre än det tillåtna värdet säkerhetskopierar du den överordnade mappen eller diskenheten.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Vilka tecken är tillåtna i filsökvägar?

MARS-agenten förlitar sig på NTFS och tillåter [tecken som stöds](/windows/desktop/FileIO/naming-a-file#naming-conventions) i filnamn/sökvägar.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Varningen "Azure Backups have not been configured for this server" visas

Den här varningen kan visas även om du har konfigurerat en princip för säkerhetskopiering, när inställningarna för säkerhetskopieringsschema som lagras på den lokala servern inte är desamma som de inställningar som lagras i säkerhetskopieringsvalvet.

* När servern eller inställningarna har återställts till ett fungerande tillstånd kan säkerhetskopieringsscheman bli osynkroniserade.
* Om du får den här varningen [konfigurerar du](backup-azure-manage-windows-server.md) säkerhetskopieringsprincipen igen och kör sedan en säkerhetskopiering på begäran för att synkronisera om den lokala servern med Azure.

## <a name="manage-the-backup-cache-folder"></a>Hantera cachemappen för säkerhetskopiering

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Vilken är den minsta nödvändiga storleken på cachelagringsmappen?

Storleken på cachelagringsmappen avgör mängden data som säkerhetskopieras.

* Cachemappvolymerna bör ha ledigt utrymme som motsvarar minst 5-10 % av den totala storleken på säkerhetskopierade data.
* Om volymen har mindre ledigt utrymme på 5 % ökar du volymen eller flyttar cachemappen till en volym med tillräckligt med utrymme genom att följa [dessa steg](#how-do-i-change-the-cache-location-for-the-mars-agent).
* Om du säkerhetskopierar Windows System State behöver du ytterligare 30–35 GB ledigt utrymme i volymen som innehåller cachemappen.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Hur man kontrollerar om scratch mappen är giltig och tillgänglig?

1. Som standard finns scratch-mappen på`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Kontrollera att sökvägen till platsen för scratch-mappen stämmer överens med värdena för registernyckelposterna nedan:

    | Sökväg i registret | Registernyckel | Värde |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Ny plats för cachemappen* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Ny plats för cachemappen* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Hur ändrar jag cacheplatsen för MARS-agenten?

1. Kör det här kommandot i en upphöjd kommandotolk för att stoppa säkerhetskopieringsmotorn:

    ```Net stop obengine```
2. Om du har konfigurerat säkerhetskopiering av systemtillstånd öppnar du Diskhantering och `"CBSSBVol_<ID>"`avmonterar diskarna med namn i formatet .
3. Som standard finns scratch-mappen på`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. Kopiera hela `\Scratch` mappen till en annan enhet som har tillräckligt med utrymme. Kontrollera att innehållet kopieras, inte flyttas.
5. Uppdatera följande registerposter med sökvägen till den nyligen flyttade scratch-mappen.

    | Sökväg i registret | Registernyckel | Värde |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Ny plats för scratch-mappen* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Ny plats för scratch-mappen* |

6. Starta om säkerhetskopieringsmotorn vid en upphöjd kommandotolk:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Kör en säkerhetskopiering på begäran. När säkerhetskopian har slutförts med den nya platsen kan du ta bort den ursprungliga cachemappen.

### <a name="where-should-the-cache-folder-be-located"></a>Var ska cachemappen finnas?

Följande platser för cachemappen rekommenderas inte:

* Nätverksdelning/flyttbara media: Cachemappen måste vara lokal för den server som behöver säkerhetskopieras med säkerhetskopiering online. Nätverksplatser eller flyttbara media som USB-enheter stöds inte.
* Offlinevolymer: Cachemappen måste vara online för förväntad säkerhetskopiering med Azure Backup Agent

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Finns det några attribut i cachemappen som inte stöds?

Följande attribut eller deras kombinationer stöds inte för cachelagringsmappen:

* Krypterade
* Deduplicerade
* Komprimerade
* Utspridda
* Referenspunkt

Cachemappen och metadata-VIRTUELLAD har inte de nödvändiga attributen för Azure Backup-agenten.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Finns det något sätt att justera mängden bandbredd som används för säkerhetskopiering?

Ja, du kan använda alternativet **Ändra egenskaper** i MARS-agenten för att justera bandbredd och tid. [Läs mer](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>Återställ

### <a name="manage"></a>Hantera

**Kan jag återhämta mig om jag har glömt min lösenfras?**
Azure Backup-agenten kräver en lösenfras (som du angav under registreringen) för att dekryptera säkerhetskopierade data under återställningen. Granska scenarierna nedan för att förstå dina alternativ för att hantera en förlorad lösenfras:

| Original maskin <br> *(källmaskin där säkerhetskopior har tagits)* | Lösenfras | Tillgängliga alternativ |
| --- | --- | --- |
| Tillgängligt |Förlorade |Om den ursprungliga datorn (där säkerhetskopieringar har gjorts) är tillgänglig och fortfarande är registrerad med samma Recovery Services-valv kan du återskapa lösenfrasen genom att följa dessa [steg](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase).  |
| Förlorade |Förlorade |Det går inte att återställa data eller data som inte är tillgängliga |

Tänk på följande:

* Om du avinstallerar och registrerar om agenten på samma ursprungliga dator med
  * *Samma lösenfras*, då kommer du att kunna återställa dina säkerhetskopierade data.
  * *Olika lösenfras*, då kommer du inte att kunna återställa dina säkerhetskopierade data.
* Om du installerar agenten på en *annan maskin* med
  * *Samma lösenfras* (används i den ursprungliga maskinen), då du kommer att kunna återställa dina säkerhetskopierade data.
  * *Olika lösenfras*kan du inte återställa säkerhetskopierade data.
* Om den ursprungliga datorn är skadad (vilket hindrar dig från att återskapa lösenfrasen via MARS-konsolen), men du kan återställa eller komma åt den ursprungliga scratch-mappen som används av MARS-agenten, kanske du kan återställa (om du har glömt lösenordet). Kontakta kundtjänst om du vill ha mer hjälp.

**Hur återställer jag om jag förlorade min ursprungliga dator (där säkerhetskopieringar togs)?**

Om du har samma lösenfras (som du angav under registreringen) på den ursprungliga datorn kan du återställa säkerhetskopierade data till en alternativ dator. Granska scenarierna nedan för att förstå dina återställningsalternativ.

| Original maskin | Lösenfras | Tillgängliga alternativ |
| --- | --- | --- |
| Förlorade |Tillgängligt |Du kan installera och registrera MARS-agenten på en annan dator med samma lösenfras som du angav vid registreringen av den ursprungliga datorn. Välj **Återställningsalternativ** > **En annan plats** för återställningen. Mer information finns i den här [artikeln](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine).
| Förlorade |Förlorade |Det går inte att återställa data eller data som inte är tillgängliga |

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Vad händer om jag avbryter ett pågående återställningsjobb?

Om ett pågående återställningsjobb avbryts stoppas återställningsprocessen. Alla filer som återställts före annulleringen stannar i konfigurerad destination (original eller alternativ plats), utan återställningar.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>Har MARS-agenten säkerhetskopierat och återställer ACL:er som angetts för filer, mappar och volymer?

* MARS-agenten säkerhetskopierar ACL:er som är inställda på filer, mappar och volymer
* För återställningsalternativet För återställning av volym ger MARS-agenten ett alternativ för att hoppa över återställning av ACL-behörigheter till filen eller mappen som återställs
* För det enskilda återställningsalternativet för filer och mappar återställs MARS-agenten med behörigheter för ACL (det finns inget alternativ för att hoppa över ACL-återställning).

## <a name="next-steps"></a>Nästa steg

[Läs om](tutorial-backup-windows-server-to-azure.md) hur du säkerhetskopierar en Windows-dator.
