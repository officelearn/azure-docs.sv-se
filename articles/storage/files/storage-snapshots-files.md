---
title: Översikt över resursögonblicksbilder för Azure Files | Microsoft Docs
description: En ögonblicksbild är en skrivskyddad version av en Azure Files-resurs som händer vid en tidpunkt i tid, som ett sätt att säkerhetskopiera filresursen.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: article
ms.date: 01/17/2018
ms.author: renash
ms.component: files
ms.openlocfilehash: b261ec5fb0ad437202df1a8fd8683a095cb1bb96
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2018
ms.locfileid: "42055771"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Översikt över resursögonblicksbilder för Azure Files 
Azure Files ger möjlighet att ta ögonblicksbilder av filresurser. Dela ögonblicksbilder in tillstånd för filresurs i det här läget i tid. I den här artikeln beskrivs vilka funktioner som ger resursögonblicksbilder och hur du kan dra nytta av dem i dina anpassade användningsfall.

## <a name="when-to-use-share-snapshots"></a>När du ska använda resursögonblicksbilder

### <a name="protection-against-application-error-and-data-corruption"></a>Skydd mot fel och data skadas i programmet
Program som använder filresurser utföra åtgärder som att skriva, läsning, lagring, överföring och bearbetning. Om ett program är felkonfigurerad eller en oavsiktlig bugg introduceras, kan oavsiktlig överskrivning eller skada inträffa för ett par block. För att skydda mot dessa scenarier, kan du ta en ögonblicksbild innan du distribuerar nya programkod. Om en bugg eller ett program fel introduceras med den nya distributionen kan kan du gå tillbaka till en tidigare version av dina data på filresursen. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Skydd mot oavsiktliga borttagningar eller ändringar av oönskade
Anta att du arbetar med en textfil i en filresurs. När filen har stängts kan du förlora möjligheten att ångra ändringarna. I dessa fall kan behöver du sedan återställa en tidigare version av filen. Du kan använda ögonblicksbilder av filresurser för att återställa tidigare versioner av filen om den har ändrats eller tagits bort av misstag.

### <a name="general-backup-purposes"></a>Allmän säkerhetskopiering
När du har skapat en filresurs kan du skapa en ögonblicksbild för filresursen för att använda den för säkerhetskopiering av data med jämna mellanrum. En resurs som ögonblicksbild, när de tas med jämna mellanrum, hjälper till att upprätthålla tidigare versioner av data som kan användas för framtida granskningskrav eller katastrofåterställning.

## <a name="capabilities"></a>Funktioner
En ögonblicksbild är en point-in-time, skrivskyddad kopia av dina data. Du kan skapa, ta bort och hantera ögonblicksbilder med hjälp av REST-API. Samma funktioner är också tillgängliga i-klientbiblioteket, Azure CLI och Azure-portalen. 

Du kan visa ögonblicksbilder av en resurs med hjälp av REST API och ett SMB. Du kan hämta listan över versioner av katalogen eller filen och du kan montera en specifik version direkt som en enhet. 

När en ögonblicksbild har skapats kan kan den läsas, kopieras, eller ta bort, men inte har ändrats. Du kan inte kopiera en resursögonblicksbild av hela till ett annat lagringskonto. Du måste göra det varje fil, med hjälp av AzCopy och andra mekanismer som kopiering.

Kapaciteten för ögonblicksbild av filresurs finns i filresursen. Hämtning tillhandahålls enskilda filnivå, så att återställa enskilda filer. Du kan återställa en fullständig filresurs med hjälp av SMB, REST-API, portalen, klientbibliotek eller PowerShell/CLI-verktyg.

En ögonblicksbild av en filresurs är identiskt med dess grundläggande filresurs. Den enda skillnaden är att en **DateTime** värde läggs till resurs-URI för att ange den tid då resursen ögonblicksbilden togs. Till exempel om en filresurs för URI: N är http://storagesample.core.file.windows.net/myshare, URI: N är liknar ögonblicksbilden för resursen:
```
http://storagesample.file.core.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Ögonblicksbilder av filresurser kvar tills de uttryckligen tas bort. En ögonblicksbild av en resurs kan inte sträcker sig längre än dess grundläggande filresurs. Du kan räkna upp ögonblicksbilder som är associerade med grundläggande filresursen för att spåra din aktuella ögonblicksbilder. 

När du skapar en ögonblicksbild av en filresurs, kopieras filerna i resursens Systemegenskaper till ögonblicksbilden för resursen med samma värden. Grundläggande filer och metadata för den delade filresursen kopieras även till ögonblicksbilden för resursen om du inte anger separat metadata för resursen ögonblicksbild när du skapar den.

Du kan inte ta bort en resurs som har ögonblicksbilder, såvida inte du först ta bort alla ögonblicksbilder.

## <a name="space-usage"></a>Användning av diskutrymme 
Resursögonblicksbilder är inkrementell sin natur. Endast de data som har ändrats när din senaste resursögonblicksbild har sparats. Detta minimerar den tid som krävs för att skapa ögonblicksbilden för resursen och sparar på lagringskostnaderna. Någon skriva åtgärden till objektet eller egenskapen eller metadata uppdateringsåtgärden räknas mot ”ändrade innehåll” och lagras i ögonblicksbilden för resursen. 

Om du vill spara utrymme kan du ta bort ögonblicksbilden för resursen under när omsättningen har högsta.

Även om ögonblicksbilder av filresurser har sparats stegvis, måste du behålla bara den senaste ögonblicksbilden för resursen om du vill återställa till resursen. När du tar bort en resursögonblicksbild tas endast data som är unika för den ögonblicksbilden av en resurs bort. Aktiva ögonblicksbilder innehåller den information som du behöver för att söka och återställa data (från den tidpunkt som ögonblicksbilden för resursen togs) till den ursprungliga platsen eller en annan plats. Du kan återställa på objektnivå.

Ögonblicksbilder räknas inte mot din gräns på 5 TB resurs. Det finns ingen gräns för hur mycket utrymme resursögonblicksbilder upptar totalt. Lagringskontogränser gäller fortfarande.

## <a name="limits"></a>Begränsningar
Det maximala antalet ögonblicksbilder som Azure Files gör i dag är 200. Du måste ta bort äldre ögonblicksbilder av filresurser för att skapa nya efter 200 ögonblicksbilder. 

Det finns ingen gräns för samtidiga anrop för att skapa resursögonblicksbilder. Det finns ingen gräns för mängden utrymme som delar ögonblicksbilder av en viss resurs kan använda. 

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Kopiera data till en resurs från en ögonblicksbild av filresurs
Kopia av åtgärder som omfattar filer och dela ögonblicksbilder Följ de här reglerna:

Du kan kopiera enskilda filer i en ögonblicksbild av filresurs via dess grundläggande resurs eller någon annan plats. Du kan återställa en tidigare version av en fil eller återställa fullständig filresursen genom att kopiera varje fil från ögonblicksbilden för resursen. Ögonblicksbilden för resursen befordras inte till grundläggande resurs. 

Ögonblicksbilden för resursen bevaras när du har kopierat, men de grundläggande filresursen skrivs över med en kopia av informationen som fanns i ögonblicksbilden för resursen. Alla de återställda filerna räkningen in i ”ändra innehåll”.

Du kan kopiera en fil i en ögonblicksbild till ett mål med ett annat namn. Resulterande målfilen är en skrivbar fil och inte en ögonblicksbild.

När en målfil skrivas över med en kopia, förblir alla ögonblicksbilder som är associerade med den ursprungliga målfilen intakta.

## <a name="general-best-practices"></a>Allmänna Metodtips 
När du kör infrastruktur på Azure, automatisera säkerhetskopieringar för återställning av data när det är möjligt. Automatiska åtgärder är mer tillförlitligt än att manuella processer som hjälper till att förbättra dataskydd och återställning. Du kan använda REST-API, klient-SDK eller skript för automatisering.

Innan du distribuerar dela ögonblicksbild scheduler måste du noga överväga dina frekvensen för ögonblicksbilder för resursen och inställningarna för datakvarhållning till att undvika onödiga kostnader.

Ögonblicksbilder av filresurser skyddar bara på filnivå. Ögonblicksbilder av filresurser förhindra inte fat-finger borttagningar på en fil resurs eller storage-konto. För att skydda ett lagringskonto från oavsiktliga borttagningar, kan du låsa storage-konto eller resursgruppen.

## <a name="next-steps"></a>Nästa steg
- Arbeta med ögonblicksbilder av filresurser i:
    - [Portal](storage-how-to-use-files-portal.md#create-and-modify-share-snapshots)
    - [PowerShell](storage-how-to-use-files-powershell.md#create-and-modify-share-snapshots)
    - [CLI](storage-how-to-use-files-cli.md#create-and-modify-share-snapshots)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
- [Dela ögonblicksbild vanliga frågor och svar](storage-files-faq.md#share-snapshots)
