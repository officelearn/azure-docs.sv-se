---
title: Översikt över resurs ögonblicks bilder för Azure Files | Microsoft Docs
description: En resurs ögonblicks bild är en skrivskyddad version av en Azure Files resurs som tas vid en tidpunkt, som ett sätt att säkerhetskopiera resursen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c05b79d2f1da8076b507ca9ee7a06504de21d5ea
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333181"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Översikt över resurs ögonblicks bilder för Azure Files 
Azure Files ger möjlighet att ta bort ögonblicks bilder av fil resurser. Dela ögonblicks bilder fångar resurs statusen vid den tidpunkten. I den här artikeln beskriver vi vilka funktioner som delar ögonblicks bilder och hur du kan dra nytta av dem i ditt anpassade användnings fall.

## <a name="when-to-use-share-snapshots"></a>När du ska använda resurs ögonblicks bilder

### <a name="protection-against-application-error-and-data-corruption"></a>Skydd mot program fel och skadade data
Program som använder fil resurser utför åtgärder som att skriva, läsa, lagra, överföra och bearbeta. Om ett program är felkonfigurerat eller om ett oavsiktligt fel har införts, kan oavsiktlig överskrivning eller skada ske i ett fåtal block. För att skydda dig mot dessa scenarier kan du ta en ögonblicks bild av en resurs innan du distribuerar ny program kod. Om ett fel eller ett program fel introduceras med den nya distributionen kan du gå tillbaka till en tidigare version av dina data på fil resursen. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Skydd mot oavsiktliga borttagningar eller oavsiktliga ändringar
Tänk dig att du arbetar med en textfil i en fil resurs. När text filen har stängts förlorar du möjligheten att ångra ändringarna. I dessa fall måste du återställa en tidigare version av filen. Du kan använda resurs ögonblicks bilder för att återställa tidigare versioner av filen om den av misstag har bytt namn eller tagits bort.

### <a name="general-backup-purposes"></a>Allmänna säkerhets kopierings syfte
När du har skapat en fil resurs kan du regelbundet skapa en resurs ögonblicks bild av fil resursen för att använda den för säkerhets kopiering av data. En resurs ögonblicks bild, när den tas med jämna mellanrum, hjälper till att underhålla tidigare versioner av data som kan användas för framtida gransknings krav eller katastrof återställning.

## <a name="capabilities"></a>Kapacitet
En ögonblicks bild av en resurs är en skrivskyddad kopia av dina data vid en viss tidpunkt. Du kan skapa, ta bort och hantera ögonblicks bilder med hjälp av REST API. Samma funktioner är också tillgängliga i klient biblioteket, Azure CLI och Azure Portal. 

Du kan visa ögonblicks bilder av en resurs med hjälp av både REST API och SMB. Du kan hämta listan över versioner av katalogen eller filen och du kan montera en speciell version direkt som en enhet (endast tillgänglig i Windows-se [gränser](#limits)). 

När en resurs ögonblicks bild har skapats kan den läsas, kopieras eller tas bort, men inte ändras. Du kan inte kopiera en fullständig resurs ögonblicks bild till ett annat lagrings konto. Du måste göra filen efter fil genom att använda AzCopy eller andra kopierings metoder.

Delning av ögonblicks bilder ges på fil resurs nivå. Hämtning ges på enskild filnivå, så att enskilda filer kan återställas. Du kan återställa en fullständig fil resurs med hjälp av SMB, REST API, portalen, klient biblioteket eller PowerShell/CLI-verktyget.

En resurs ögonblicks bild av en fil resurs är identisk med bas fil resursen. Den enda skillnaden är att ett **datetime** -värde läggs till i resurs-URI: n för att ange den tid då resurs ögonblicks bilden togs. Om till exempel en fil resurs-URI är http://storagesample.core.file.windows.net/myshare, så liknar resursens ögonblicks bilds-URI följande:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Resurs ögonblicks bilder sparas tills de tas bort explicit. En resurs ögonblicks bild kan inte leva ut från sin bas fil resurs. Du kan räkna upp ögonblicks bilderna som är associerade med bas fil resursen för att spåra dina befintliga ögonblicks bilder. 

När du skapar en resurs ögonblicks bild av en fil resurs kopieras filerna i resursens system egenskaper till resursens ögonblicks bild med samma värden. Bas filerna och fil resursens metadata kopieras också till resursens ögonblicks bild, om du inte anger separata metadata för resurs ögonblicks bilden när du skapar den.

Det går inte att ta bort en resurs som har ögonblicks bilder av resursen om du inte tar bort alla ögonblicks bilder först.

## <a name="space-usage"></a>Utrymmes användning 
Resurs ögonblicks bilder är stegvisa. Endast de data som har ändrats efter att du har sparat den senaste ögonblicks bilden av resursen. Detta minimerar den tid som krävs för att skapa ögonblicks bilden av resursen och sparar pengar på lagrings kostnaderna. Alla Skriv åtgärder till åtgärden för objekt-eller egenskaps-eller metadata-uppdatering räknas till "ändrat innehåll" och lagras i resurs ögonblicks bilden. 

För att spara utrymme kan du ta bort resurs ögonblicks bilden för den period då omsättningen var högst.

Även om resurs ögonblicks bilder sparas stegvis, behöver du bara behålla den senaste ögonblicks bilden för att återställa resursen. När du tar bort en resurs ögonblicks bild tas endast de data som är unika för den delade ögonblicks bilden bort. Aktiva ögonblicks bilder innehåller all information som du behöver för att bläddra bland och återställa dina data (från den tidpunkt då resursens ögonblicks bild togs) till den ursprungliga platsen eller en alternativ plats. Du kan återställa på objekt nivå.

Ögonblicks bilder räknas inte mot din gräns för 5 TB-resursen. Det finns ingen gräns för hur mycket utrymmes resurs ögonblicks bilder som upptas totalt. Lagrings konto gränser gäller fortfarande.

## <a name="limits"></a>Begränsningar
Det maximala antalet resurs ögonblicks bilder som Azure Files tillåter idag är 200. Efter 200 resurs ögonblicks bilder måste du ta bort gamla ögonblicks bilder av resurser för att skapa nya. 

Det finns ingen gräns för samtidiga anrop för att skapa resurs ögonblicks bilder. Det finns ingen gräns för mängden utrymme som delar ögonblicks bilder av en viss fil resurs som kan använda. 

Idag är det inte möjligt att montera ögonblicks bilder av resurser i Linux. Detta beror på att Linux SMB-klienten inte stöder montering av ögonblicks bilder som Windows gör.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Kopiera data tillbaka till en resurs från resurs ögonblicks bild
Kopierings åtgärder som involverar filer och resurs ögonblicks bilder följer dessa regler:

Du kan kopiera enskilda filer i en ögonblicks bild av en fil resurs till dess bas resurs eller någon annan plats. Du kan återställa en tidigare version av en fil eller återställa hela fil resursen genom att kopiera filen från den delade ögonblicks bilden. Resurs ögonblicks bilden höjs inte till bas resursen. 

Resurs ögonblicks bilden förblir intakt efter kopiering, men bas fil resursen skrivs över med en kopia av de data som var tillgängliga i ögonblicks bilden av resursen. Alla återställda filer räknas till "ändrat innehåll".

Du kan kopiera en fil i en resurs ögonblicks bild till ett annat mål med ett annat namn. Den resulterande mål filen är en skrivbar fil och inte en resurs ögonblicks bild. I det här fallet kommer bas fil resursen att förbli intakt.

När en målfil skrivs över med en kopia förblir alla resurs ögonblicks bilder som är associerade med den ursprungliga mål filen oförändrade.

## <a name="general-best-practices"></a>Allmän bästa praxis 
När du kör en infrastruktur på Azure kan du automatisera säkerhets kopieringar för data återställning närhelst det är möjligt. Automatiserade åtgärder är mer pålitliga än manuella processer, vilket hjälper till att förbättra data skydd och återställning. Du kan använda REST API, klient-SDK eller skript för automatisering.

Innan du distribuerar Schemaläggaren för resurs ögonblicks bilder bör du ta hänsyn till din resurs frekvens för ögonblicks bilder och inställningarna för kvarhållning för att undvika onödiga kostnader.

Resurs ögonblicks bilder tillhandahåller bara skydd på filnivå. Resurs ögonblicks bilder förhindrar inte borttagning av fat-fingrar på en fil resurs eller ett lagrings konto. För att skydda ett lagrings konto från oavsiktliga borttagningar kan du låsa lagrings kontot eller resurs gruppen.

## <a name="next-steps"></a>Nästa steg
- Arbeta med resurs ögonblicks bilder i:
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [CLI](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Vanliga frågor och svar om att dela bilder](storage-files-faq.md#share-snapshots)
