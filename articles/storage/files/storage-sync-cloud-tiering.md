---
title: Förstå Azure File Sync moln nivåer | Microsoft Docs
description: Läs mer om hur du Azure File Sync funktions moln nivåer
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 23e98c40420a5f1ed9b048d5530eacfe5eedfb32
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85413985"
---
# <a name="cloud-tiering-overview"></a>Översikt över moln nivåer
Moln nivåer är en valfri funktion i Azure File Sync där ofta använda filer cachelagras lokalt på servern medan alla andra filer är i nivå av Azure Files baserat på princip inställningar. När en fil skiktas, ersätter Azure File Sync fil system filtret (StorageSync.sys) filen lokalt med en pekare eller referens punkt. Referens punkten representerar en URL till filen i Azure Files. En fil med flera nivåer har både attributet "offline" och attributet FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS som har angetts i NTFS så att tredjepartsprogram kan identifiera nivåbaserade filer på ett säkert sätt.
 
När en användare öppnar en skiktad fil, återkallar Azure File Sync sömlöst fildata från Azure Files utan att användaren behöver veta att filen är lagrad i Azure. 
 
 > [!Important]  
 > Det finns inte stöd för moln nivåer på Windows-systemvolymen.
    
 > [!Important]  
 > Nätverks bandbredden bör vara minst 1 Mbit/s för att återställa filer som har skiktts. Om nätverks bandbredden är mindre än 1 Mbit/s går det inte att återkalla filer med ett tids gräns fel.

## <a name="cloud-tiering-faq"></a>Vanliga frågor och svar om moln nivåer

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Hur fungerar moln nivån?
Azure File Sync system filter skapar ett "termisk karta" för ditt namn område på varje server slut punkt. Den övervakar åtkomst (Läs-och skriv åtgärder) över tid och sedan, baserat på både frekvensen och recency, tilldelar en värme poäng till varje fil. En fil med ofta åtkomst som nyligen har öppnats kommer att anses vara frekvent, medan en fil som är knappt touchd och inte har använts under en viss tid anses vara sval. När fil volymen på en server överskrider tröskelvärdet för ledigt utrymme på volymen, kommer den att ange de häftigaste filerna som ska Azure Files tills din procent andel av det lediga utrymmet är uppfyllt.

Dessutom kan du ange en datum princip för varje server slut punkt som kommer att lagra alla filer som inte används inom ett angivet antal dagar, oavsett tillgänglig lokal lagrings kapacitet. Det här är ett bra alternativ för att proaktivt frigöra lokalt disk utrymme om du vet att filer på den server slut punkten inte behöver behållas lokalt efter en viss ålder. Det frigör värdefull lokal disk kapacitet för andra slut punkter på samma volym, för att cachelagra fler filer.

Termisk karta i molnet är i stort sett en sorterad lista över alla filer som synkroniseras och finns på en plats där moln skiktning är aktiverat. För att fastställa den relativa placeringen av en enskild fil i den termisk karta använder systemet det maximala värdet för någon av följande tidsstämplar i den ordningen: MAX (senaste åtkomst tid, senast ändrad tid, skapande tid). Normalt spåras senaste åtkomst tid och är tillgänglig. Men när en ny server slut punkt skapas, med moln nivå aktive rad, är det inte tillräckligt med tid för att observera fil åtkomsten. Om det inte finns någon senaste åtkomst tid används den senaste ändrings tiden för att utvärdera den relativa positionen i termisk karta. Samma återställning gäller för datum policyn. Utan senaste åtkomst tid kommer datum policyn att vidtas på den senaste ändrings tiden. Om det inte är tillgängligt kommer det att återgå till skapande tiden för en fil. Med tiden kommer systemet att observera fler och fler fil åtkomst begär Anden och pivotera till att huvudsakligen använda den senast spårade senaste åtkomst tiden.

Moln nivåer är inte beroende av NTFS-funktionen för att spåra senaste åtkomst tid. Den här NTFS-funktionen är inaktive rad som standard och på grund av prestanda överväganden rekommenderar vi inte att du aktiverar den här funktionen manuellt. Moln nivåer spårar senaste åtkomst tid separat och mycket effektivt.

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>Vilken är den minsta fil storleken för en fil till-nivån?

För agent versioner 9 och senare baseras den minsta fil storleken för en fil på nivån på fil systemets kluster storlek. I följande tabell visas de minsta fil storlekarna som kan vara i nivå, baserat på volym kluster storleken:

|Volym kluster storlek (byte) |Filer av den här storleken eller större kan skiktas  |
|----------------------------|---------|
|4 KB (4096)                 | 8 kB    |
|8 KB (8192)                 | 16 kB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768) och större    | 64 kB   |

Alla fil system som används i Windows ordnar din hård disk baserat på kluster storlek (även kallat storlek på allokeringsenhet). Kluster storleken representerar den minsta mängd disk utrymme som kan användas för att lagra en fil. Om fil storlekar inte kommer ut till en till följd av kluster storleken, måste ytterligare utrymme användas för att lagra filen (upp till nästa multipel av kluster storleken).

Azure File Sync stöds på NTFS-volymer med Windows Server 2012 R2 och senare. I följande tabell beskrivs standard storlekarna för klustret när du skapar en ny NTFS-volym. 

|Volym storlek    |Windows Server 2012R2 och senare |
|---------------|---------------|
|7 MB – 16 TB   | 4 kB          |
|16TB – 32 TB   | 8 kB          |
|32 TB – 64 TB   | 16 kB         |
|64 TB – 128 TB  | 32 KB         |
|128TB – 256 TB | 64 kB         |
|> 256 TB       | Stöds inte |

När volymen har skapats har du kanske formaterat volymen manuellt med en annan storlek för klustret (allokeringsenhet). Om volymen härrör från en äldre version av Windows kan standard kluster storlekarna också vara olika. [Den här artikeln innehåller mer information om standard kluster storlekar.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat)

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Hur fungerar nivåprincipen för ledigt utrymme på volymen?
Ledigt utrymme på volymen är mängden ledigt utrymme som du vill reservera på volymen där en server slut punkt finns. Om till exempel volym ledigt utrymme har angetts till 20% på en volym som har en server slut punkt kommer upp till 80% av volym utrymmet att användas av de senast använda filerna, med eventuella återstående filer som inte passar in i det här utrymmet på Azure. Volymens lediga utrymme gäller volym nivån i stället för på nivån för enskilda kataloger eller Sync-grupper. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Hur fungerar nivåprincipen för ledigt utrymme på volymen för nya serverslutpunkter?
När en server slut punkt nyligen har allokerats och anslutits till en Azure-filresurs, hämtar servern först namn området och hämtar sedan de faktiska filerna tills den träffar tröskelvärdet för ledigt utrymme på volymen. Den här processen kallas även snabb katastrof återställning eller snabb namn områdes återställning.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Hur beräknas ledigt volymutrymme när det finns flera serverslutpunkter på en volym?
Om det finns mer än en server slut punkt på en volym, är det effektiva tröskelvärdet för ledigt disk utrymme det största lediga volym utrymmet som anges över alla Server slut punkter på volymen. Filerna sorteras enligt deras användnings mönster oavsett vilken server slut punkt de tillhör. Om du till exempel har två server slut punkter på en volym, Endpoint1 och Endpoint2, där Endpoint1 har ett tröskelvärde för ledigt utrymme på 25% och Endpoint2 har ett tröskelvärde för ledigt utrymme på 50%, kommer volymens tröskelvärde för ledigt utrymme för båda Server slut punkterna att vara 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Hur fungerar nivåprincipen för datum tillsammans med nivåprincipen för ledigt utrymme på volymen? 
När du aktiverar moln nivåer på en server slut punkt kan du ange en princip för ledigt utrymme på volymen. Den har alltid företräde framför alla andra principer, inklusive datum principen. Alternativt kan du aktivera en datum princip för varje server slut punkt på volymen. Den här principen hanterar att endast filer som har öppnats (dvs. läses eller skrivs till) inom det intervall med dagar som denna princip beskriver kommer att vara lokala. Filer som inte har öppnats med det angivna antalet dagar visas i nivå. 

Moln nivåer använder senaste åtkomst tid för att avgöra vilka filer som ska nivåas. Filtret för filter driv rutinen för moln skikt (storagesync.sys) spårar senaste åtkomst tid och loggar informationen i värme lagret för moln lagring. Du kan se värme lagret med en lokal PowerShell-cmdlet.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Get-StorageSyncHeatStoreInformation '<LocalServerEndpointPath>'
```

> [!IMPORTANT]
> Den senaste gången-timestamp är inte en egenskap som spåras av NTFS och visas därför inte som standard i Utforskaren. Använd inte den senast ändrade tidstämpeln på en fil för att kontrol lera om datum principen fungerar som förväntat. Den här tidsstämpeln spårar bara skrivningar, inte läsningar. Använd den cmdlet som visas för att få den senaste tillgängliga-tidstämpeln för den här utvärderingen.

> [!WARNING]
> Aktivera inte NTFS-funktionen för att spåra senast använda tidsstämpel för filer och mappar. Den här funktionen är inaktive rad som standard eftersom den har stor påverkan på prestanda. Azure File Sync spårar senast använda gånger och är mycket effektivt och använder inte denna NTFS-funktion.

Tänk på att principen för ledigt utrymme på volymen alltid har företräde, och när det inte finns tillräckligt med ledigt utrymme på volymen för att bevara så många dagar filer som beskrivs i datum principen, Azure File Sync fortsätter att placera coldest-filerna i nivå tills volymens lediga utrymme är uppfyllt.

Anta till exempel att du har en datum-baserad nivå princip på 60 dagar och en princip för ledigt utrymme på 20%. Om det finns mindre än 20% ledigt utrymme på volymen efter att ha tillämpat datum policyn, så kommer principen för ledigt utrymme i volymen att tillämpas och åsidosätta datum principen. Detta leder till att fler filer på nivå, så att mängden data som lagras på servern kan minskas från 60 dagars data till 45 dagar. Det innebär att den här principen tvingar fram en nivå av filer som ligger utanför tidsintervallet, även om du inte har nått tröskelvärdet för ledigt utrymme – så att en fil som är 61 dagar gammal kommer att skiktas även om volymen är tom.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Hur vet jag vad som är lämplig mängd ledigt utrymme på volymen?
Mängden data som du bör behålla lokalt bestäms av några faktorer: bandbredden, din data uppsättnings åtkomst mönster och din budget. Om du har en anslutning med låg bandbredd kanske du vill behålla mer av dina data lokalt för att se till att det finns en minimal fördröjning för dina användare. Annars kan du basera den på omsättnings skatten under en viss period. Om du till exempel vet att 10% av dina 1 TB data uppsättnings ändringar eller används aktivt varje månad, kan du behålla 100 GB lokalt så att du inte ofta anropar filer. Om volymen är 2 TB vill du behålla 5% (eller 100 GB) lokalt, vilket innebär att återstående 95% är volymens lediga utrymme i procent. Vi rekommenderar dock att du lägger till en buffert i konto för perioder med högre omsättning – med andra ord, med en lägre volym på ledigt utrymme i procent och sedan justerar den om det behövs senare. 

Att behålla mer data lokalt innebär lägre utgående kostnader eftersom färre filer kommer att återkallas från Azure, men kräver också att du underhåller en större mängd lokal lagring, som kommer till en egen kostnad. När du har en instans av Azure File Sync distribuerad kan du titta på ditt lagrings kontos utgångs tecken för att se om dina volym inställningar för ledigt utrymme är lämpliga för din användning. Förutsatt att lagrings kontot bara innehåller Azure File Sync moln slut punkten (det vill säga din synkroniseringsresurs) innebär det att många filer återkallas från molnet, och du bör överväga att öka det lokala cacheminnet.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Jag har lagt till en ny server slut punkt. Hur länge till mina filer på den här server nivån?

Huruvida filer måste vara nivåbaserade per uppsättnings principer utvärderas en gång i timmen. Du kan stöta på två situationer när en ny server slut punkt skapas:

1. När du lägger till en ny server slut punkt finns ofta filer på den server platsen. De måste laddas upp först innan moln nivån kan börja. Principen för ledigt utrymme på volymen börjar inte att fungera förrän den inledande överföringen av alla filer har avslut ATS. Den valfria datum principen kommer dock att börja arbeta med en enskild fil, så snart en fil har överförts. Intervallet med en timme gäller även här. 
2. När du lägger till en ny server slut punkt är det möjligt att du ansluter en tom Server plats till en Azure-filresurs med dina data. Om det gäller en andra server eller under en katastrof återställning. Om du väljer att ladda ned namn området och återkalla innehållet under den inledande nedladdningen till servern, kommer filerna att återkallas, baserat på den senast ändrade tidsstämpeln. Endast så många filer kommer att återkallas så att de passar in i principen ledigt utrymme för volymen och den valfria datum principen.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Hur kan jag se om en fil har flyttats på nivå?
Det finns flera sätt att kontrol lera om en fil har flyttats till din Azure-fil resurs:
    
   *  **Kontrol lera filattributen på filen.**
     Högerklicka på en fil, gå till **information**och rulla ned till egenskapen **attributes** . En nivå fil har följande attribut inställda:     
        
        | Bokstav för attribut | Attribut | Definition |
        |:----------------:|-----------|------------|
        | A | Arkiv | Anger att filen ska säkerhets kopie ras av säkerhets kopierings program. Det här attributet är alltid angivet, oavsett om filen har en nivå eller lagras helt på disken. |
        | P | Sparse-fil | Anger att filen är en sparse-fil. En sparse-fil är en specialiserad typ av fil som NTFS erbjuder för effektiv användning när filen i disk strömmen huvudsakligen är tom. Azure File Sync använder sparse-filer eftersom en fil antingen är helt på nivå eller delvis återkallas. I en fil med fullständigt skikt lagras fil data strömmen i molnet. I en delvis återkallad fil är den delen av filen redan på disk. Om en fil återställs fullständigt till disken, Azure File Sync konverterar den från en sparse-fil till en vanlig fil. Det här attributet anges bara för Windows Server 2016 och äldre.|
        | M | Återkalla data åtkomst | Anger att filens data inte är fullständigt tillgängliga på den lokala lagrings platsen. Om du läser filen kommer minst en del av fil innehållet att hämtas från en Azure-filresurs som server slut punkten är ansluten till. Det här attributet anges bara för Windows Server 2019. |
        | L | Referenspunkt | Anger att filen har en referens punkt. En referens punkt är en särskild pekare som används av ett fil system filter. Azure File Sync använder referens punkter för att definiera Azure File Sync fil system filtret (StorageSync.sys) moln platsen där filen lagras. Detta stöder sömlös åtkomst. Användarna behöver inte känna till att Azure File Sync används eller hur de får åtkomst till filen i Azure-filresursen. När en fil har återkallats fullständigt, Azure File Sync tar bort referens punkten från filen. |
        | O | Offline | Anger att en del av eller hela filens innehåll inte lagras på disken. När en fil har återkallats fullständigt, Azure File Sync tar bort det här attributet. |

        ![Dialog rutan Egenskaper för en fil med fliken information markerad](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Du kan se attributen för alla filer i en mapp genom att lägga till fältet **attribut** i tabell visningen för Utforskaren. Om du vill göra detta högerklickar du på en befintlig kolumn (till exempel **storlek**), väljer **mer**och väljer sedan **attribut** i list rutan.
        
   * **Används `fsutil` för att kontrol lera referens punkter för en fil.**
       Som det beskrivs i föregående alternativ har en nivå fil alltid en referens punkts uppsättning. En referens pekare är en särskild pekare för Azure File Sync fil system filter (StorageSync.sys). Du kan kontrol lera om en fil har en referens punkt i en upphöjd kommando tolk eller PowerShell-fönster genom att köra `fsutil` verktyget:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Om filen har en referens punkt kan du se till att **reparse tag-värdet: 0x8000001E**. Det här hexadecimala värdet är det referens punkts värde som ägs av Azure File Sync. Utdata innehåller också de referens data som representerar sökvägen till filen på Azure-filresursen.

        > [!WARNING]  
        > `fsutil reparsepoint`Verktygs kommandot har också möjlighet att ta bort en referens punkt. Kör inte det här kommandot om inte Azure File Sync teknik teamet ber dig. Att köra det här kommandot kan leda till data förlust. 

<a id="afs-recall-file"></a>
### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>En fil som jag vill använda har flera nivåer. Hur kan jag återkalla filen till disk för att använda den lokalt?
Det enklaste sättet att återkalla en fil till disken är att öppna filen. Azure File Sync fil system filter (StorageSync.sys) laddar ned filen från Azure-filresursen utan något arbete på din sida. För filtyper som kan läsas delvis från, t. ex. multimedia eller zip-filer, så laddas inte hela filen om du öppnar en fil.

Du kan också använda PowerShell för att tvinga en fil att återkallas. Det här alternativet kan vara användbart om du vill återkalla flera filer samtidigt, t. ex. alla filer i en mapp. Öppna en PowerShell-session på den servernod där Azure File Sync är installerat och kör sedan följande PowerShell-kommandon:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Valfria parametrar:
* `-Order CloudTieringPolicy`kommer att återkalla de senast ändrade eller öppnade filerna först och tillåts av den aktuella nivå principen. 
    * Om principen för ledigt utrymme på volymen har kon figurer ATS, kommer filerna att återkallas tills det finns en princip inställning för ledigt utrymme på volymen. Om den kostnads fria princip inställningen till exempel är 20% stoppas återställningen när volymens lediga utrymme når 20%.  
    * Om mängden ledigt utrymme och en datum princip har kon figurer ATS kommer filerna att återkallas tills volymens lediga utrymme eller datum princip inställning nås. Om den kostnads fria princip inställningen till exempel är 20% och datum policyn är 7 dagar, stoppas återställningen när volymens lediga utrymme når 20% eller om alla filer som har öppnats eller ändrats inom 7 dagar är lokala.
* `-ThreadCount`anger hur många filer som kan återkallas parallellt.
* `-PerFileRetryCount`anger hur ofta ett återställnings försök ska göras för en fil som för närvarande är blockerad.
* `-PerFileRetryDelaySeconds`fastställer tiden i sekunder mellan försök att återkalla försök och bör alltid användas i kombination med föregående parameter.

Exempel:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
> - Cmdleten Invoke-StorageSyncFileRecall kan också användas för att förbättra fil nedladdnings prestanda när du lägger till en ny server slut punkt i en befintlig synkroniseringsresurs.  
>- Om den lokala volym som är värd för servern inte har tillräckligt med ledigt utrymme för att återkalla alla data på nivån, `Invoke-StorageSyncFileRecall` Miss lyckas cmdleten.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Varför stämmer inte *storleken på disk* egenskapen för en fil med egenskapen *size* efter att du använt Azure File Sync? 
Utforskaren i Windows visar två egenskaper som representerar storleken på en fil: **storlek** och **storlek på disk**. De här egenskaperna skiljer sig i betydelse. **Storlek** representerar filens fullständiga storlek. **Storleken på disken** representerar storleken på fil strömmen som lagras på disken. Värdena för dessa egenskaper kan skilja sig åt av olika orsaker, till exempel komprimering, användning av datadeduplicering eller moln nivåer med Azure File Sync. Om en fil är i nivå av en Azure-filresurs är storleken på disken noll, eftersom fil data strömmen lagras i Azure-filresursen och inte på disken. Det är också möjligt för en fil att delvis skiktas (eller delvis återkallas). I en delvis skiktad fil finns en del av filen på disk. Detta kan inträffa när filer delvis läses av program som multimedie spelare eller zip-verktyg. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Hur gör jag för att tvinga en fil eller katalog att vara i nivå av?

> [!NOTE]
> När du väljer en katalog som ska sorteras på nivå, så är det bara filer som finns i katalogen. Filer som skapas efter den tiden sker inte automatiskt i nivå.

När funktionen för moln nivåer är aktive rad, nivårar automatiskt filer baserat på senaste åtkomst och ändrings tider för att uppnå volymens lediga utrymme i procent som anges i moln slut punkten. Ibland kanske du vill tvinga fram en fil manuellt till nivån. Detta kan vara användbart om du sparar en stor fil som du inte tänker använda igen under en längre tid, och du vill att det lediga utrymmet på volymen nu ska användas för andra filer och mappar. Du kan framtvinga en nivå genom att använda följande PowerShell-kommandon:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>Varför visas inte de filer som är i nivå med miniatyrer eller för hands versioner i Utforskaren?
För nivåbaserade filer visas inte miniatyrer och för hands versioner på din server slut punkt. Det här beteendet förväntas eftersom funktionen för miniatyr-cache i Windows avsiktligt hoppar över läsning av filer med attributet offline. När moln nivån är aktive rad kan läsning genom nivåer av filer leda till att de hämtas (återkallas).

Det här beteendet är inte bara för Azure File Sync, Windows Explorer visar ett "grått X" för alla filer som har offline-attributet inställt. X-ikonen visas vid åtkomst till filer över SMB. En detaljerad förklaring av det här problemet finns i[https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

<a id="afs-tiering-disabled"></a>
### <a name="i-have-cloud-tiering-disabled-why-are-there-tiered-files-in-the-server-endpoint-location"></a>Jag har inaktiverat moln nivå, varför finns det filer på Server slut punkten?

Det finns två orsaker till varför skiktade filer kan finnas på Server slut punkts platsen:

- När du lägger till en ny server slut punkt i en befintlig synkroniseringskoppling synkroniseras metadata först till servern och filerna laddas sedan ned till servern i bakgrunden. Filerna visas som i nivå innan de hämtas lokalt. Använd cmdleten [Invoke-StorageSyncFileRecall](storage-sync-cloud-tiering.md#afs-recall-file) för att förbättra fil hämtnings prestanda när du lägger till en ny server i en Sync-grupp.

- Om moln skiktning har Aktiver ATS på Server slut punkten och sedan inaktiverats, kommer filer att fortsätta att vara i nivå tills de nås.


## <a name="next-steps"></a>Nästa steg
* [Planera för en Azure File Sync distribution](storage-sync-files-planning.md)
