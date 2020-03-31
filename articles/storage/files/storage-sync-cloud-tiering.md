---
title: Så här fungerar Molndelning för Azure-filsynkronisering | Microsoft-dokument
description: Lär dig mer om Azure File Sync funktion Cloud Tiering
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 11f9097fc4875f0a4300ac56dafe7af9a0b00c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454626"
---
# <a name="cloud-tiering-overview"></a>Översikt över molnnivådelning
Molnnivådelning är en valfri funktion i Azure File Sync där filer som används ofta cachelagras lokalt på servern medan alla andra filer är nivåindelade till Azure-filer baserat på principinställningar. När en fil är nivåindelning ersätter filsystemet För Azure File Sync (StorageSync.sys) filen lokalt med en pekare eller en ã¤ljtig punkt. Reparspunkten representerar en URL till filen i Azure Files. En nivåindelad fil har både attributet "offline" och FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS attributuppsättningen i NTFS så att tredjepartsprogram kan identifiera nivåindelade filer på ett säkert sätt.
 
När en användare öppnar en nivåindelad fil återkallar Azure File Sync fildata från Azure-filer sömlöst utan att användaren behöver veta att filen lagras i Azure. 
 
 > [!Important]  
 > Molnnivådelning stöds inte på Windows-systemvolymen.
    
 > [!Important]  
 > Om du vill återkalla filer som har nivåindelats bör nätverksbandbredden vara minst 1 Mbit/s. Om nätverksbandbredden är mindre än 1 Mbit/s kan det hända att filer inte kommer ihåg med ett timeout-fel.

## <a name="cloud-tiering-faq"></a>Vanliga frågor om molnnivådelning

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Hur fungerar molndelning?
Systemfiltret Azure File Sync skapar en "heatmap" av ditt namnområde på varje serverslutpunkt. Den övervakar åtkomster (läs- och skrivåtgärder) över tid och sedan, baserat på både frekvens och recency av tillgång, tilldelar en värme poäng till varje fil. En ofta använda filen som nyligen öppnades kommer att betraktas som varm, medan en fil som knappt berörs och inte har använts under en tid kommer att betraktas som cool. När filvolymen på en server överskrider tröskelvärdet för ledigt utrymme som du anger, kommer den att nivåa de coolaste filerna till Azure-filer tills din lediga utrymmesprocent har uppnåtts.

I version 4.0 och högre av Azure File Sync-agenten kan du dessutom ange en datumprincip på varje serverslutpunkt som ska nivåa alla filer som inte nås eller ändras inom ett visst antal dagar.

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>Vilken är den minsta filstorleken för en fil till nivå?
För agentversioner 9.x och nyare baseras den minsta filstorleken för en fil till nivå på filsystemets klusterstorlek (dubbla filsystemets klusterstorlek). Om ntfs-filsystemets klusterstorlek till exempel är 4KB är den resulterande minsta filstorleken för en fil till nivå 8KB. För agentversioner 8.x och äldre är den minsta filstorleken för en fil till nivå 64KB.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Hur fungerar nivåprincipen för ledigt utrymme på volymen?
Volymfritt utrymme är den mängd ledigt utrymme som du vill reservera på den volym där en serverslutpunkt finns. Om volymfritt utrymme till exempel är inställt på 20 % på en volym som har en serverslutpunkt, kommer upp till 80 % av volymutrymmet att upptas av de senast använda filerna, med eventuella återstående filer som inte passar in i det här utrymmet som nivåindelad upp till Azure. Volymfritt utrymme gäller på volymnivå i stället för på nivån för enskilda kataloger eller synkroniseringsgrupper. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Hur fungerar nivåprincipen för ledigt utrymme på volymen för nya serverslutpunkter?
När en serverslutpunkt är nyligen etablerad och ansluten till en Azure-filresurs, kommer servern först att dra ner namnområdet och sedan dra ner de faktiska filerna tills den når sin volymfri utrymme tröskel. Den här processen kallas även snabb haveriberedskap eller snabb återställning av namnområde.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Hur beräknas ledigt volymutrymme när det finns flera serverslutpunkter på en volym?
När det finns mer än en serverslutpunkt på en volym är tröskelvärdet för effektivt volymfritt utrymme det största volymfria utrymme som anges över en serverslutpunkt på den volymen. Filer kommer att nivåindelad enligt deras användningsmönster oavsett vilken serverslutpunkt som de tillhör. Om du till exempel har två serverslutpunkter på en volym, Slutpunkt1 och Slutpunkt2, där Slutpunkt1 har ett tröskelvärde för ledigt utrymme på 25 % och slutpunkten2 har ett tröskelvärde för ledigt utrymme på 50 %, är tröskelvärdet för ledigt utrymme för båda serverslutpunkterna 50 %. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Hur fungerar nivåprincipen för datum tillsammans med nivåprincipen för ledigt utrymme på volymen? 
När du aktiverar molnnivådelning på en serverslutpunkt anger du en princip för ledigt utrymme. Den har alltid företräde framför alla andra principer, inklusive datumprincipen. Du kan också aktivera en datumprincip för varje serverslutpunkt på den volymen, vilket innebär att endast filer som används (det vill säga läsa eller skrivas till) inom det intervall av dagar som den här principen beskriver kommer att hållas lokal, med alla föraktuella filer som är nivåindelad. Tänk på att principen om ledigt utrymme för volym alltid har företräde, och när det inte finns tillräckligt med ledigt utrymme på volymen för att behålla så många dagar som filerna är värda enligt datumprincipen fortsätter Azure File Sync att nivåinna de kallaste filerna tills volymen är fri utrymmesprocenten uppfylls.

Anta till exempel att du har en datumbaserad nivåindelad princip på 60 dagar och en volymfri utrymmesprincip på 20 %. Om det, efter att datumprincipen har tillämpats, finns mindre än 20 % ledigt utrymme på volymen, kommer principen för ledigt utrymme att sparka in och åsidosätta datumprincipen. Detta kommer att resultera i att fler filer nivåindelad, så att mängden data som lagras på servern kan minskas från 60 dagars data till 45 dagar. Omvänt kommer den här principen att tvinga nivåindelning av filer som faller utanför ditt tidsintervall även om du inte har nått tröskelvärdet för ledigt utrymme – så en fil som är 61 dagar gammal kommer att nivåindelad även om volymen är tom.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Hur vet jag vad som är lämplig mängd ledigt utrymme på volymen?
Mängden data som du bör hålla lokal bestäms av några faktorer: din bandbredd, datauppsättningens åtkomstmönster och din budget. Om du har en anslutning med låg bandbredd kanske du vill behålla mer av dina data lokalt för att säkerställa att det finns minimal fördröjning för användarna. Annars kan du basera den på omsättningshastigheten under en viss period. Om du till exempel vet att cirka 10 % av dina 1 TB-datauppsättning ändras eller används aktivt varje månad, kanske du vill behålla 100 GB lokalt så att du inte ofta återkallar filer. Om volymen är 2 TB, då du kommer att vilja hålla 5% (eller 100 GB) lokal, vilket innebär att de återstående 95% är din volym ledigt utrymme procent. Vi rekommenderar dock att du lägger till en buffert för att ta hänsyn till perioder med högre omsättning – med andra ord, från och med en lägre volymfri utrymmesprocent, och sedan justera den om det behövs senare. 

Att behålla mer data lokalt innebär lägre utgående kostnader eftersom färre filer kommer att återkallas från Azure, men kräver också att du underhåller en större mängd lokal lagring, vilket kommer på egen bekostnad. När du har distribuerat en instans av Azure File Sync kan du titta på ditt lagringskontos utgående för att ungefär mäta om dina volymfria utrymmesinställningar är lämpliga för din användning. Förutsatt att lagringskontot bara innehåller din Azure File Sync Cloud Endpoint (det vill säga din synkroniseringsresurs), innebär hög utgående utgående att många filer återkallas från molnet och du bör överväga att öka din lokala cache.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Jag har lagt till en ny serverslutpunkt. Hur lång tid tills mina filer på den här servernivån?
I version 4.0 och högre av Azure File Sync-agenten, när dina filer har överförts till Azure-filresursen, kommer de att nivåindelad enligt dina principer så snart nästa nivåinderingssession körs, vilket inträffar en gång i timmen. På äldre agenter kan det ta upp till 24 timmar att delta i nivåindelningen.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Hur vet jag om en fil har nivåats upp?
Det finns flera sätt att kontrollera om en fil har nivåats upp till din Azure-filresurs:
    
   *  **Kontrollera filens attribut i filen.**
     Högerklicka på en fil, gå till **Detaljer**och bläddra sedan ned till egenskapen **Attribut.** En nivåindelad fil har följande attribut inställd:     
        
        | Attributbokstav | Attribut | Definition |
        |:----------------:|-----------|------------|
        | A | Arkiv | Anger att filen ska säkerhetskopieras av säkerhetskopieringsprogram. Det här attributet är alltid inställt, oavsett om filen är nivåindelade eller lagrad helt på disken. |
        | P | Sparse-fil | Anger att filen är en gles fil. En gles fil är en specialiserad filtyp som NTFS erbjuder för effektiv användning när filen på diskströmmen oftast är tom. Azure File Sync använder glesa filer eftersom en fil antingen är helt nivåindelad eller delvis återkallad. I en helt nivåindelad fil lagras filströmmen i molnet. I en delvis återkallad fil finns den delen av filen redan på disken. Om en fil återkallas helt till disk konverteras den från en gles fil till en vanlig fil. Det här attributet anges endast på Windows Server 2016 och äldre.|
        | M | Återkalla dataåtkomst | Anger att filens data inte finns helt på lokal lagring. Om du läser filen kommer åtminstone en del av filinnehållet att hämtas från en Azure-filresurs som serverslutpunkten är ansluten till. Det här attributet anges endast i Windows Server 2019. |
        | L | Referenspunkt | Anger att filen har en förarplats. En reparse punkt är en speciell pekare för användning av ett filsystemfilter. Azure File Sync använder reparse punkter för att definiera för Azure File Sync filsystemfilter (StorageSync.sys) molnplatsen där filen lagras. Detta stöder sömlös åtkomst. Användarna behöver inte veta att Azure File Sync används eller hur du får åtkomst till filen i din Azure-filresurs. När en fil återkallas helt tar Azure File Sync bort reparspunkten från filen. |
        | O | Offline | Anger att en del av eller hela filens innehåll inte lagras på disken. När en fil återkallas helt tar Azure File Sync bort det här attributet. |

        ![Dialogrutan Egenskaper för en fil, med fliken Information markerad](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Du kan se attributen för alla filer i en mapp genom att lägga till fältet **Attribut** i tabellvisningen av Utforskaren. För att göra detta högerklicka på en befintlig kolumn (till exempel **Storlek**), välj **Mer**och välj sedan **Attribut** i listrutan.
        
   * **Används `fsutil` för att söka efter reparse-punkter på en fil.**
       Som beskrivs i föregående alternativ har en nivåindelad fil alltid en reparsepunktuppsättning. En reparspekare är en särskild pekare för filsystemet Azure File Sync (StorageSync.sys). Om du vill kontrollera om en fil har en reparspunkt kör `fsutil` du verktyget i ett upphöjt kommandotolks- eller PowerShell-fönster:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Om filen har en reparse punkt, kan du förvänta dig att se **Reparse Tag Value: 0x8000001e**. Det här hexadecimala värdet är det reparspunktvärde som ägs av Azure File Sync. Utdata innehåller också reparsedata som representerar sökvägen till filen på din Azure-filresurs.

        > [!WARNING]  
        > Verktyget `fsutil reparsepoint` kommandot har också möjlighet att ta bort en reparse punkt. Kör inte det här kommandot om inte Azure File Sync engineering team ber dig att. Om du kör det här kommandot kan det leda till dataförlust. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>En fil som jag vill använda har nivåindelats. Hur kan jag återkalla filen till disken för att använda den lokalt?
Det enklaste sättet att återkalla en fil till disk är att öppna filen. Filsystemet Azure File Sync (StorageSync.sys) hämtar filen sömlöst från din Azure-filresurs utan att du arbetar med din del. För filtyper som delvis kan läsas från, till exempel multimedia- eller ZIP-filer, hämtas inte hela filen när du öppnar en fil.

Du kan också använda PowerShell för att tvinga en fil att återkallas. Det här alternativet kan vara användbart om du vill återkalla flera filer samtidigt, till exempel alla filer i en mapp. Öppna en PowerShell-session till servernoden där Azure File Sync är installerat och kör sedan följande PowerShell-kommandon:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Valfria parametrar:
* `-Order CloudTieringPolicy`kommer att återkalla de senast ändrade filerna först.  
* `-ThreadCount`avgör hur många filer som kan återkallas parallellt.
* `-PerFileRetryCount`avgör hur ofta ett återkallande görs av en fil som för närvarande är blockerad.
* `-PerFileRetryDelaySeconds`bestämmer tiden i sekunder mellan återförsök för att återkalla försök och bör alltid användas i kombination med den tidigare parametern.

> [!Note]  
> Om den lokala volymen som är värd för servern inte har `Invoke-StorageSyncFileRecall` tillräckligt med ledigt utrymme för att återkalla alla nivåindelade data, misslyckas cmdleten.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Varför matchar inte egenskapen *Storlek på disk* för en fil egenskapen *Size* när du har använt Azure File Sync? 
I Utforskaren visas två egenskaper som representerar storleken på en fil: **Storlek** och **storlek på disk**. Dessa egenskaper skiljer sig subtilt i betydelse. **Storleken** representerar filens hela storlek. **Storlek på disk** representerar storleken på filströmmen som lagras på disken. Värdena för dessa egenskaper kan skilja sig åt av olika anledningar, till exempel komprimering, användning av dataduplicering eller molnnivådelning med Azure File Sync. Om en fil är nivåinställd till en Azure-filresurs är storleken på disken noll, eftersom filströmmen lagras i din Azure-filresurs och inte på disken. Det är också möjligt att en fil delvis ska nivåindelad (eller delvis återkallas). I en fil som är delvis nivåindelad finns en del av filen på disken. Detta kan inträffa när filer delvis läs av program som multimediaspelare eller zip-verktyg. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Hur tvingar jag en fil eller katalog att nivåindelad?
När molnnivådelningsfunktionen är aktiverad, nivåer molnnivåfiler automatiskt filer baserat på senaste åtkomst och ändra tider för att uppnå den volymfri utrymme procent som anges på molnet slutpunkten. Ibland kanske du vill tvinga en fil manuellt till nivå. Detta kan vara användbart om du sparar en stor fil som du inte tänker använda igen under en längre tid, och du vill att det lediga utrymmet på volymen nu ska användas för andra filer och mappar. Du kan tvinga på nivåindelning med hjälp av följande PowerShell-kommandon:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>Varför visas inte miniatyrer eller förhandsgranskningar i Utforskaren i Mina nivåindelade filer?
För nivåindelada filer visas inte miniatyrer och förhandsgranskningar på serverslutpunkten. Det här problemet förväntas eftersom miniatyrcachefunktionen i Windows avsiktligt hoppar över att läsa filer med offlineattributet. Med Cloud Tiering aktiverat, läsa igenom nivåindelade filer skulle orsaka dem att laddas ner (återkallas).

Det här problemet är inte specifikt för Azure File Sync, Utforskaren visar ett "grått X" för alla filer som har offlineattributet inställt. Du kommer att se X-ikonen när du öppnar filer via SMB. En detaljerad förklaring av detta beteende finns i[https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)


## <a name="next-steps"></a>Efterföljande moment
* [Planera för en Azure File Sync-distribution](storage-sync-files-planning.md)
