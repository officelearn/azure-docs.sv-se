---
title: Förstå Azure File Sync Molnnivå | Microsoft Docs
description: Lär dig mer om Azure File Sync-funktionen Molnlagringsnivåer
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 09/21/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1851e9b2bb5ff86583228136dee977001cf0a3fd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714947"
---
# <a name="cloud-tiering-overview"></a>Molnet lagringsnivåer översikt
Molnet lagringsnivåer är en valfri funktion i Azure File Sync som ofta öppnade filer cachelagras lokalt på servern medan alla andra filer nivåindelas till Azure Files utifrån principinställningar. När en fil är nivåindelad ersätter Azure File Sync filsystemsfilter (StorageSync.sys) filen lokalt med en pekare eller en referenspunkt. Referenspunkten representerar en URL till filen i Azure Files. En nivåindelad fil har både ”offline”-attributet och FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS attributuppsättningen i NTFS så att program från tredje part kan på ett säkert sätt identifiera nivåindelade filer.
 
När en användare öppnar en nivåindelad fil, återkallar Azure File Sync sömlöst fildata från Azure-filer utan att användaren behöver du veta att filen lagras i Azure. 
 
 > [!Important]  
 > Molnet lagringsnivåer stöds inte för serverslutpunkter på volymer för Windows-system och endast filer som är större än 64 KiB i storlek kan vara nivåindelad till Azure Files.
    
Azure File Sync stöder inte lagringsnivåer filer som är mindre än 64 KiB så att prestanda försämras av lagringsnivåer och återställa sådana små filer skulle uppväger Utrymmesbesparingar.

 > [!Important]  
 > Om du vill återställa filer som har varit nivåindelade, måste nätverkets bandbredd vara minst 1 Mbit/s. Om nätverksbandbredden är mindre än 1 Mbit/s, misslyckas filer att komma ihåg med ett timeout-fel.

## <a name="cloud-tiering-faq"></a>Molnet lagringsnivåer vanliga frågor och svar

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Hur fungerar molnbaserad lagringsnivåer arbete?
Azure File Sync system filtret bygger en ”termisk karta” på ditt namnområde på varje serverslutpunkt. Det övervakar åtkomst (Läs- och skrivåtgärder) över tid och sedan, baserat på både frekvens och recency av åtkomst, tilldelar en termisk resultatet till varje fil. En ofta fil som nyligen har öppnats betraktas frekvent, medan en fil som är knappt vidröras och har inte använts under en viss tid betraktas lågfrekvent. När filen volym på en server överskrider tröskeln för ledigt utrymme som du anger, kommer den datanivå häftigaste filerna till Azure Files tills din procent ledigt utrymme är uppfyllt.

I version 4.0 och senare av Azure File Sync-agenten du kan dessutom ange en princip för datum på varje serverslutpunkt som kommer delar alla filer som inte använts eller ändrats inom ett angivet antal dagar.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Hur fungerar volym ledigt utrymme lagringsnivåer principen?
Volymens lediga utrymme är mängden ledigt utrymme som du vill reservera på volymen där en serverslutpunkt finns. Till exempel om volymens lediga utrymme är inställd på 20% på en volym som har en serverslutpunkt, upp till 80% av utrymmet på volym kommer vara upptas av nivåer nyligen öppnade filer, med eventuella återstående filer som inte passar i det här utrymmet upp till Azure. Ledigt utrymme på volym gäller på volymnivå snarare än på nivån för enskilda kataloger eller synkroniseringsgrupper. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Hur fungerar volym ledigt utrymme lagringsnivåer principen för nya serverslutpunkter?
När en serverslutpunkt är nyligen etablerade och ansluten till en Azure-filresurs, servern först hämtar namnområdet och sedan hämtar de faktiska filerna tills den når sin tröskelvärdet för återställningspunktvolym ledigt utrymme. Den här processen kallas även snabb katastrofåterställning eller snabb namnområde återställningen.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Hur tolkas ledigt utrymme på volym när jag har flera serverslutpunkter på en volym?
Om det finns fler än en serverslutpunkt på en volym, är effektiva volym ledigt utrymme tröskelvärdet den största ledigt utrymme på volym anges i valfri serverslutpunkt på den volymen. Filer nivåindelas enligt deras användningsmönster oavsett vilken serverslutpunkt som de tillhör. Till exempel om du har två server-slutpunkter på en volym, slutpunkt 1 och Endpoint2, är där slutpunkt 1 har ett tröskelvärde för ledigt utrymme för volymen med 25% och Endpoint2 har ett tröskelvärde för ledigt utrymme av volymen på 50% tröskelvärde för ledigt utrymme på volymen för både serverslutpunkter 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Hur datum lagringsnivåer principen fungerar tillsammans med det lediga utrymmet på volymen lagringsnivåer princip? 
När att aktivera molnlagringsnivåer på en serverslutpunkt kan du ställa in en princip för ledigt utrymme av volymen. Den har alltid företräde framför alla andra principer, inklusive policy för datum. Alternativt kan du aktivera ett datum som principen för varje serverslutpunkt på att volymen, vilket innebär att endast filer tillgängliga (det vill säga läsa eller skriva till) i antal dagar som beskrivs i den här principen sparas lokalt, med eventuella staler filer nivåer. Tänk på att volymen ledigt utrymme principen har alltid företräde, och när det finns inte tillräckligt med ledigt utrymme på volymen för att behålla så många dagar som filer som enligt principen datum, Azure File Sync fortsätter lagringsnivåer kallaste filerna fram till volymen som är kostnadsfri andel av utrymme är uppfyllt.

Anta exempelvis att du har ett datumbaserat principer för lagringsnivåer på 60 dagar och en princip för ledigt utrymme av volym av 20%. Om det finns mindre än 20% ledigt utrymme på volymen efter att ha tillämpat principen datum, startar principen volym ledigt utrymme och åsidosätta principen datum. Detta resulterar i fler filer som nivåer, så att mängden data som sparas på servern kan minskas från 60 dagars data till 45 dagar. Den här principen tvingar däremot lagringsnivåer av filer som faller utanför tidsintervallet, även om du inte har nått tröskeln för ditt utrymme – så att en fil som är 61 dagar gammal nivåindelas även om volymen är tom.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Hur vet jag lämplig mängd ledigt utrymme på volym?
Mängden data som du bör ha lokala bestäms av några faktorer: bandbredden, din datauppsättning åtkomstmönster och din budget. Om du har en anslutning med låg bandbredd kan du behålla flera av dina data lokalt för att säkerställa att det är minimal fördröjning för dina användare. Annars kan basera du den på omsättningsfrekvensen under en viss period. Till exempel om du vet att cirka 10% av ändringarna 1 TB datauppsättning eller används aktivt varje månad och sedan kan du hålla 100 GB lokal så du inte ofta återställa filer. Om volymen är 2TB, så du kan hålla 5% (eller 100 GB) local, vilket innebär att de återstående 95 din volym ledigt utrymme i procent är. Vi rekommenderar dock att du lägger till en buffert för perioder med högre omsättning – d.v.s. börjar med en lägre andel av volymen ledigt utrymme och anpassa den om det behövs senare. 

Att hålla mer data lokala innebär lägre kostnader för nätverksegress som färre filer ska återställas från Azure, men kräver också att underhålla en större mängd lokal lagring, som kommer till sin egen kostnad. När du har en instans av Azure File Sync distribueras kan titta du på ditt lagringskonto utgående trafik till ungefär avgöra om volyminställningarna för ledigt utrymme är lämpliga för din användning. Förutsatt att storage-konto innehåller bara Azure filen synkronisering Molnslutpunkten (det vill säga din synkroniseringsresurs) blir hög utgående innebär att många filer hämtas från molnet, och du bör överväga att öka din lokal cache.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Jag har lagt till en ny serverslutpunkt. Hur länge tills Mina filer på den här nivån för server?
I version 4.0 och senare av Azure File Sync-agenten när filerna har överförts till Azure-filresurs, de nivåindelas enligt dina principer så snart som nästa lagringsnivåer session körningar, som sker en gång i timmen. Äldre agenterna kan det ta upp till 24 timmar att hända att lagringsnivåer.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Hur vet jag om en fil har nivåindelade?
Det finns flera sätt att kontrollera om en fil nivåer till din Azure-filresurs:
    
   *  **Kontrollera filattribut för filen.**
     Högerklicka på en fil, gå till **information**, och bläddra till den **attribut** egenskapen. En nivåindelad fil har de följande attribut:     
        
        | Attributet bokstav | Attribut | Definition |
        |:----------------:|-----------|------------|
        | A | Arkiv | Anger att filen bör säkerhetskopieras av programvara för säkerhetskopiering. Det här attributet anges alltid, oavsett om filen nivåer eller helt lagras på disk. |
        | P | Sparse-fil | Anger att filen är en sparse-fil. En sparse-fil är en särskild typ av fil som NTFS erbjuder effektiv när filen på disk stream huvudsakligen är tom. Azure File Sync använder sparse-filer eftersom en fil nivåer helt eller delvis återkallas. Fildataströmmen är en fullständigt nivåindelad fil lagras i molnet. I en delvis återställd fil, som en del av filen finns redan på disk. Om en fil är fullständigt kanske till disk, Azure File Sync konverteras från en sparse-fil till en vanlig fil. |
        | L | Referenspunkt | Anger att filen har en referenspunkt. En referenspunkt är en särskild pekare för användning av ett filsystemsfilter. Azure File Sync använder referenspunkter för att definiera till Azure File Sync filsystemsfilter (StorageSync.sys) cloud platsen där filen lagras. Det stöder sömlös åtkomst. Användarna behöver inte veta att Azure File Sync som används eller hur du får åtkomst till filen i din Azure-filresurs. När en fil återställs helt bort Azure File Sync referenspunkten från filen. |
        | O | Offline | Anger att en eller flera av dess innehåll inte lagras på disk. När en fil återställs fullständigt Azure File Sync tar du bort det här attributet. |

        ![Dialogrutan Egenskaper för en fil med fliken information om markerad](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Du kan se attributen för alla filer i en mapp genom att lägga till den **attribut** till tabellen visningen av Utforskaren. Gör detta genom att högerklicka på en befintlig kolumn (till exempel **storlek**), Välj **mer**, och välj sedan **attribut** från den nedrullningsbara listan.
        
   * **Använd `fsutil` att söka efter referenspunkter för en fil.**
       Enligt beskrivningen i föregående alternativ, har en nivåindelad fil alltid en referenspunkt set. En referenspunkt pekare är en särskild pekare för Azure File Sync filsystemsfilter (StorageSync.sys). Om du vill kontrollera om en fil har en referenspunkt, i en upphöjd kommandotolk eller PowerShell-kommandotolk, kör den `fsutil` verktyget:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Om filen har en referenspunkt, du kan förvänta dig att se **referenspunkter Taggvärde: 0x8000001e**. Den här hexadecimalt värde är punktvärdet referenspunkter som ägs av Azure File Sync. Utdata innehåller också referensdata som representerar sökvägen till din fil på din Azure-filresurs.

        > [!WARNING]  
        > Den `fsutil reparsepoint` verktyget kommandot har också möjlighet att ta bort en referenspunkt. Kör inte det här kommandot om inte det tekniska teamet för Azure File Sync ber dig. Kör det här kommandot kan resultera i dataförlust. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>En fil som jag vill använda har varit nivåindelade. Hur kan jag återställa filen till disk för att använda den lokalt?
Det enklaste sättet att återställa en fil till disk är att öppna filen. Azure File Sync filsystemsfilter (StorageSync.sys) hämtas sömlöst från din Azure-filresurs utan något arbete från din sida. För filtyper som kan vara delvis läsning från, t.ex multimediafiler eller ZIP-filer, öppna en fil inte ladda ned hela filen.

Du kan också använda PowerShell om du vill tvinga en fil som ska återställas. Det här alternativet kan vara användbart om du vill återställa flera filer samtidigt, till exempel alla filer i en mapp. Öppna en PowerShell-session till noden där Azure File Sync är installerad och kör följande PowerShell-kommandon:
    
    ```powershell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Varför inte den *storlek på disken* -egenskapen för en fil matchar den *storlek* egenskapen när du har använt Azure File Sync? 
Windows Utforskaren visar två egenskaper för att representera storleken på en fil: **Storlek** och **storlek på disken**. De här egenskaperna skriftspråk i betydelse. **Storlek** representerar fullständig storleken på filen. **Storlek på disken** representerar storleken på fildataströmmen som lagras på disken. Värden för dessa egenskaper kan variera för olika skäl, till exempel komprimering, användning av Datadeduplicering eller molnnivå med Azure File Sync. Om en fil är nivåindelad till en Azure-filresurs, är storleken på disken noll, eftersom fildataströmmen lagras i din Azure-filresurs och inte på disken. Det är också möjligt för en fil som ska innehålla delvis nivåindelade (eller delvis återkallade). I en delvis nivåindelad fil är en del av filen på disken. Detta kan inträffa när filer läses delvis av program, t.ex. multimedia spelare eller zip-verktyg. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Hur gör jag för att tvinga en fil eller katalog för att vara nivåindelad?
När funktionen cloud lagringsnivåer aktiveras molnlagringsnivåer automatiskt nivåer filer baserat på senaste åtkomst och ändra gånger för att uppnå den volymen ledigt utrymme i procent som anges på molnslutpunkten. Ibland, men kanske du vill tvinga en fil till tier manuellt. Detta kan vara användbart om du sparar en stor fil som du inte planerar att använda igen under en längre tid och du vill ha det lediga utrymmet på volymen nu ska användas för andra filer och mappar. Du kan tvinga lagringsnivåer med hjälp av följande PowerShell-kommandon:

    ```powershell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

## <a name="next-steps"></a>Nästa steg
* [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
