---
title: Förstå Azure File Sync moln nivåer | Microsoft Docs
description: Läs mer om hur du Azure File Sync funktions moln nivåer
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e8a8502b40410df221886cde2fa5f3db15bf3eed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80549162"
---
# <a name="cloud-tiering-overview"></a>Översikt över moln nivåer
Moln nivåer är en valfri funktion i Azure File Sync där ofta använda filer cachelagras lokalt på servern medan alla andra filer är i nivå av Azure Files baserat på princip inställningar. När en fil skiktas, ersätter Azure File Sync fil system filtret (StorageSync. sys) filen lokalt med en pekare eller referens punkt. Referens punkten representerar en URL till filen i Azure Files. En fil med flera nivåer har både attributet "offline" och attributet FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS som har angetts i NTFS så att tredjepartsprogram kan identifiera nivåbaserade filer på ett säkert sätt.
 
När en användare öppnar en skiktad fil, återkallar Azure File Sync sömlöst fildata från Azure Files utan att användaren behöver veta att filen är lagrad i Azure. 
 
 > [!Important]  
 > Det finns inte stöd för moln nivåer på Windows-systemvolymen.
    
 > [!Important]  
 > Nätverks bandbredden bör vara minst 1 Mbit/s för att återställa filer som har skiktts. Om nätverks bandbredden är mindre än 1 Mbit/s går det inte att återkalla filer med ett tids gräns fel.

## <a name="cloud-tiering-faq"></a>Vanliga frågor och svar om moln nivåer

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Hur fungerar moln nivån?
Azure File Sync system filter skapar ett "termisk karta" för ditt namn område på varje server slut punkt. Den övervakar åtkomst (Läs-och skriv åtgärder) över tid och sedan, baserat på både frekvensen och recency, tilldelar en värme poäng till varje fil. En fil med ofta åtkomst som nyligen har öppnats kommer att anses vara frekvent, medan en fil som är knappt touchd och inte har använts under en viss tid anses vara sval. När fil volymen på en server överskrider tröskelvärdet för ledigt utrymme på volymen, kommer den att ange de häftigaste filerna som ska Azure Files tills din procent andel av det lediga utrymmet är uppfyllt.

I version 4,0 och senare av Azure File Sync agenten kan du lägga till en datum princip på varje server slut punkt som kommer att ange alla filer som inte har öppnats eller ändrats inom ett angivet antal dagar.

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>Vilken är den minsta fil storleken för en fil till-nivån?
För agent versioner 9. x och senare baseras den minsta fil storleken för en fil på nivån på fil systemets kluster storlek (dubblerat fil systemets kluster storlek). Om till exempel fil systemets NTFS-storlek är 4KB är den resulterande minsta fil storleken för en fil till nivån 8 KB. För agent versioner 8. x och äldre är den minsta fil storleken för en fil till nivån 64 kB.

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

Moln nivåer använder senaste åtkomst tid för att avgöra vilka filer som ska nivåas. Filter driv rutinen för moln skikts filtret (storagesync. sys) spårar senaste åtkomst tid och loggar informationen i värme lagret för moln nivåer. Du kan se värme lagret med en lokal PowerShell-cmdlet.

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
När filerna har laddats upp till Azure-filresursen i version 4,0 och senare av Azure File Sync-agenten, kommer de att sorteras enligt dina principer så snart nästa nivå av sessioner körs, vilket sker en gång i timmen. På äldre agenter kan det ta upp till 24 timmar innan skiktning sker.

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
        | L | Referenspunkt | Anger att filen har en referens punkt. En referens punkt är en särskild pekare som används av ett fil system filter. Azure File Sync använder referens punkter för att definiera till Azure File Sync fil system filter (StorageSync. sys) den moln plats där filen lagras. Detta stöder sömlös åtkomst. Användarna behöver inte känna till att Azure File Sync används eller hur de får åtkomst till filen i Azure-filresursen. När en fil har återkallats fullständigt, Azure File Sync tar bort referens punkten från filen. |
        | O | Offline | Anger att en del av eller hela filens innehåll inte lagras på disken. När en fil har återkallats fullständigt, Azure File Sync tar bort det här attributet. |

        ![Dialog rutan Egenskaper för en fil med fliken information markerad](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Du kan se attributen för alla filer i en mapp genom att lägga till fältet **attribut** i tabell visningen för Utforskaren. Om du vill göra detta högerklickar du på en befintlig kolumn (till exempel **storlek**), väljer **mer**och väljer sedan **attribut** i list rutan.
        
   * **Används `fsutil` för att kontrol lera referens punkter för en fil.**
       Som det beskrivs i föregående alternativ har en nivå fil alltid en referens punkts uppsättning. En referens pekare är en särskild pekare för Azure File Sync fil system filter (StorageSync. sys). Du kan kontrol lera om en fil har en referens punkt i en upphöjd kommando tolk eller PowerShell-fönster genom `fsutil` att köra verktyget:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Om filen har en referens punkt kan du se till att **reparse tag-värdet: 0x8000001E**. Det här hexadecimala värdet är det referens punkts värde som ägs av Azure File Sync. Utdata innehåller också de referens data som representerar sökvägen till filen på Azure-filresursen.

        > [!WARNING]  
        > `fsutil reparsepoint` Verktygs kommandot har också möjlighet att ta bort en referens punkt. Kör inte det här kommandot om inte Azure File Sync teknik teamet ber dig. Att köra det här kommandot kan leda till data förlust. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>En fil som jag vill använda har flera nivåer. Hur kan jag återkalla filen till disk för att använda den lokalt?
Det enklaste sättet att återkalla en fil till disken är att öppna filen. Filtret för Azure File Sync-filsystem (StorageSync. sys) laddar ned filen från Azure-filresursen utan något arbete på din sida. För filtyper som kan läsas delvis från, t. ex. multimedia eller zip-filer, så laddas inte hela filen om du öppnar en fil.

Du kan också använda PowerShell för att tvinga en fil att återkallas. Det här alternativet kan vara användbart om du vill återkalla flera filer samtidigt, t. ex. alla filer i en mapp. Öppna en PowerShell-session på den servernod där Azure File Sync är installerat och kör sedan följande PowerShell-kommandon:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Valfria parametrar:
* `-Order CloudTieringPolicy`kommer att återkalla de senast ändrade filerna först.  
* `-ThreadCount`anger hur många filer som kan återkallas parallellt.
* `-PerFileRetryCount`anger hur ofta ett återställnings försök ska göras för en fil som för närvarande är blockerad.
* `-PerFileRetryDelaySeconds`fastställer tiden i sekunder mellan försök att återkalla försök och bör alltid användas i kombination med föregående parameter.

> [!Note]  
> Om den lokala volym som är värd för servern inte har tillräckligt med ledigt utrymme för att återkalla alla data på `Invoke-StorageSyncFileRecall` nivån, Miss lyckas cmdleten.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Varför stämmer inte *storleken på disk* egenskapen för en fil med egenskapen *size* efter att du använt Azure File Sync? 
Utforskaren i Windows visar två egenskaper som representerar storleken på en fil: **storlek** och **storlek på disk**. De här egenskaperna skiljer sig i betydelse. **Storlek** representerar filens fullständiga storlek. **Storleken på disken** representerar storleken på fil strömmen som lagras på disken. Värdena för dessa egenskaper kan skilja sig åt av olika orsaker, till exempel komprimering, användning av datadeduplicering eller moln nivåer med Azure File Sync. Om en fil är i nivå av en Azure-filresurs är storleken på disken noll, eftersom fil data strömmen lagras i Azure-filresursen och inte på disken. Det är också möjligt för en fil att delvis skiktas (eller delvis återkallas). I en delvis skiktad fil finns en del av filen på disk. Detta kan inträffa när filer delvis läses av program som multimedie spelare eller zip-verktyg. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Hur gör jag för att tvinga en fil eller katalog att vara i nivå av?
När funktionen för moln nivåer är aktive rad, nivårar automatiskt filer baserat på senaste åtkomst och ändrings tider för att uppnå volymens lediga utrymme i procent som anges i moln slut punkten. Ibland kanske du vill tvinga fram en fil manuellt till nivån. Detta kan vara användbart om du sparar en stor fil som du inte tänker använda igen under en längre tid, och du vill att det lediga utrymmet på volymen nu ska användas för andra filer och mappar. Du kan framtvinga en nivå genom att använda följande PowerShell-kommandon:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>Varför visas inte de filer som är i nivå med miniatyrer eller för hands versioner i Utforskaren?
För nivåbaserade filer visas inte miniatyrer och för hands versioner på din server slut punkt. Det här beteendet förväntas eftersom funktionen för miniatyr-cache i Windows avsiktligt hoppar över läsning av filer med attributet offline. När moln nivån är aktive rad kan läsning genom nivåer av filer leda till att de hämtas (återkallas).

Det här beteendet är inte bara för Azure File Sync, Windows Explorer visar ett "grått X" för alla filer som har offline-attributet inställt. X-ikonen visas vid åtkomst till filer över SMB. En detaljerad förklaring av det här problemet finns i[https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)


## <a name="next-steps"></a>Nästa steg
* [Planera för en Azure File Sync distribution](storage-sync-files-planning.md)
