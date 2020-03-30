---
title: Översikt över ögonblicksbilder av resurs för Azure-filer | Microsoft-dokument
description: En ögonblicksbild av resurs är en skrivskyddad version av en Azure Files-resurs som tas vid en tidpunkt, som ett sätt att säkerhetskopiera resursen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c05b79d2f1da8076b507ca9ee7a06504de21d5ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72333181"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Översikt över resursögonblicksbilder för Azure Files 
Azure Files ger möjlighet att ta ögonblicksbilder av delar av filresurser. Dela ögonblicksbilder fånga resurstillstånd vid den tidpunkten. I den här artikeln beskriver vi vilka funktioner som delar ögonblicksbilder och hur du kan dra nytta av dem i ditt anpassade användningsfall.

## <a name="when-to-use-share-snapshots"></a>När ska resursögonblicksbilder användas

### <a name="protection-against-application-error-and-data-corruption"></a>Skydd mot programfel och datakorruption
Program som använder filresurser utför åtgärder som att skriva, läsa, lagra, överföra och bearbeta. Om ett program är felkonfigurerat eller ett oavsiktligt fel införs, kan oavsiktliga överskrivningar eller skador hända några kvarter. För att skydda mot dessa scenarier kan du ta en ögonblicksbild av resursen innan du distribuerar ny programkod. Om ett fel eller ett programfel införs med den nya distributionen kan du gå tillbaka till en tidigare version av dina data på filresursen. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Skydd mot oavsiktliga borttagningar eller oavsiktliga ändringar
Anta att du arbetar med en textfil i en filresurs. När textfilen har stängts förlorar du möjligheten att ångra ändringarna. I dessa fall måste du sedan återställa en tidigare version av filen. Du kan använda ögonblicksbilder av resurs för att återställa tidigare versioner av filen om den har bytt namn eller tagits bort av misstag.

### <a name="general-backup-purposes"></a>Allmänna säkerhetskopieringssyften
När du har skapat en filresurs kan du regelbundet skapa en ögonblicksbild av filresursen så att den används för säkerhetskopiering av data. En ögonblicksbild av resursen, när den tas med jämna mellanrum, hjälper till att underhålla tidigare versioner av data som kan användas för framtida granskningskrav eller haveriberedskap.

## <a name="capabilities"></a>Funktioner
En ögonblicksbild av resurs är en skrivskyddad kopia av dina data. Du kan skapa, ta bort och hantera ögonblicksbilder med hjälp av REST API. Samma funktioner är också tillgängliga i klientbiblioteket, Azure CLI och Azure-portalen. 

Du kan visa ögonblicksbilder av en resurs med hjälp av både REST API och SMB. Du kan hämta listan över versioner av katalogen eller filen, och du kan montera en viss version direkt som en enhet (endast tillgänglig i Windows - se [Gränser](#limits)). 

När en ögonblicksbild av resursen har skapats kan den läsas, kopieras eller tas bort, men inte ändras. Du kan inte kopiera en hel ögonblicksbild av resursen till ett annat lagringskonto. Du måste göra den filen per fil, med hjälp av AzCopy eller andra kopieringsmekanismer.

Funktionen För ögonblicksbild av resurs finns på filresursnivå. Hämtning tillhandahålls på individuell filnivå, för att möjliggöra återställning av enskilda filer. Du kan återställa en fullständig filresurs med hjälp av SMB, REST API, portalen, klientbiblioteket eller PowerShell/CLI-verktygen.

En ögonblicksbild av en filresurs är identisk med basfilresursen. Den enda skillnaden är att ett **DateTime-värde** läggs till i resurs-URI för att ange vid vilken tidpunkt ögonblicksbilden av resursen togs. Om till exempel en filresurs-URI är, http://storagesample.core.file.windows.net/myshareliknar uri för resursögonblicksbild:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Ögonblicksbilder av resurs kvarstår tills de uttryckligen tas bort. En ögonblicksbild av resursen kan inte överleva sin basfilresurs. Du kan räkna upp ögonblicksbilder som är associerade med basfilresursen för att spåra dina aktuella ögonblicksbilder. 

När du skapar en ögonblicksbild av en filresurs kopieras filerna i resursens systemegenskaper till ögonblicksbilden av resursen med samma värden. Basfilerna och filresursens metadata kopieras också till ögonblicksbilden av resursen, såvida du inte anger separata metadata för ögonblicksbilden av resursen när du skapar den.

Du kan inte ta bort en resurs som har ögonblicksbilder av resursen om du inte tar bort alla ögonblicksbilder av resursen först.

## <a name="space-usage"></a>Användning av utrymme 
Ögonblicksbilder av resurs är inkrementella till sin natur. Endast de data som har ändrats efter att den senaste ögonblicksbilden av resursen har sparats. Detta minimerar den tid som krävs för att skapa ögonblicksbilden av resursen och sparar på lagringskostnader. Alla skrivningar till objektet eller egenskapen eller metadatauppdateringsåtgärden räknas in i "ändrat innehåll" och lagras i ögonblicksbilden av resursen. 

Om du vill spara utrymme kan du ta bort ögonblicksbilden av resursen för den period då omsättningen var som högst.

Även om ögonblicksbilder av resurs sparas stegvis måste du bara behålla den senaste ögonblicksbilden av resursen för att återställa resursen. När du tar bort en ögonblicksbild av resursen tas endast de data som är unika för ögonblicksbilden av resursen bort. Aktiva ögonblicksbilder innehåller all information som du behöver för att bläddra bland och återställa dina data (från den tidpunkt då ögonblicksbilden av resursen togs) till den ursprungliga platsen eller en alternativ plats. Du kan återställa på objektnivå.

Ögonblicksbilder räknas inte mot din 5 TB-delningsgräns. Det finns ingen gräns för hur mycket ögonblicksbilder av utrymmesresurs som upptar totalt. Begränsningar för lagringskonto gäller fortfarande.

## <a name="limits"></a>Begränsningar
Det maximala antalet ögonblicksbilder av resurs som Azure Files tillåter idag är 200. Efter 200 ögonblicksbilder av resurs måste du ta bort äldre ögonblicksbilder av resurs för att kunna skapa nya. 

Det finns ingen gräns för samtidiga anrop för att skapa ögonblicksbilder av resurs. Det finns ingen gräns för hur mycket utrymme som delar ögonblicksbilder av en viss filresurs kan förbruka. 

Idag är det inte möjligt att montera ögonblicksbilder av resurs på Linux. Detta beror på att Linux SMB-klienten inte stöder montering ögonblicksbilder som Windows gör.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Kopiera data tillbaka till en resurs från ögonblicksbild av resurs
Kopiera åtgärder som involverar filer och dela ögonblicksbilder följer dessa regler:

Du kan kopiera enskilda filer i en ögonblicksbild av filresursen till basresursen eller någon annan plats. Du kan återställa en tidigare version av en fil eller återställa hela filresursen genom att kopiera fil efter fil från ögonblicksbilden av resursen. Ögonblicksbilden av resursen befordras inte till basresurs. 

Ögonblicksbilden av resursen förblir intakt efter kopiering, men basfilresursen skrivs över med en kopia av de data som var tillgängliga i ögonblicksbilden av resursen. Alla återställda filer räknas in i "ändrat innehåll".

Du kan kopiera en fil i en ögonblicksbild av resursen till ett annat mål med ett annat namn. Den resulterande målfilen är en skrivbar fil och inte en ögonblicksbild av resursen. I det här fallet förblir basfilresursen intakt.

När en målfil skrivs över med en kopia förblir alla ögonblicksbilder av delar som är associerade med den ursprungliga målfilen intakta.

## <a name="general-best-practices"></a>Allmän bästa praxis 
När du kör infrastruktur på Azure automatiserar du säkerhetskopior för dataåterställning när det är möjligt. Automatiserade åtgärder är mer tillförlitliga än manuella processer, vilket bidrar till att förbättra dataskyddet och återställningsbarheten. Du kan använda REST API, Klient-SDK eller skript för automatisering.

Innan du distribuerar schemaläggaren för ögonblicksbilder av resursen bör du noga överväga hur du använder ögonblicksbilder och bevarandeinställningar för att undvika onödiga avgifter.

Ögonblicksbilder av resurs ger endast filnivåskydd. Ögonblicksbilder av resurser förhindrar inte borttagningar av fettfinger på en filresurs eller ett lagringskonto. Om du vill skydda ett lagringskonto från oavsiktliga borttagningar kan du låsa lagringskontot eller resursgruppen.

## <a name="next-steps"></a>Nästa steg
- Arbeta med ögonblicksbilder av resurs i:
    - [Powershell](storage-how-to-use-files-powershell.md)
    - [CLI](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Vanliga frågor och svar om ögonblicksbilder för delning](storage-files-faq.md#share-snapshots)
