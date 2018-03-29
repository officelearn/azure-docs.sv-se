---
title: Översikt över dela ögonblicksbilder för Azure-filer (förhandsversion) | Microsoft Docs
description: En ögonblicksbild av en resurs är en skrivskyddad version av en resurs i Azure-filer som hämtas vid en tidpunkt som ett sätt att säkerhetskopiera resursen.
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: renash
ms.openlocfilehash: 671e3737a620d85c732a091d5a62f35f35c1d515
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Översikt över dela ögonblicksbilder för Azure-filer 
Azure Files ger möjlighet att ta resursen ögonblicksbilder av filresurser. Dela ögonblicksbilder avbilda tillstånd för filresurs då i tid. Den här artikeln beskrivs vilka funktioner som ger resursen ögonblicksbilder och hur du kan dra nytta av dem i din anpassade användningsfall.


## <a name="when-to-use-share-snapshots"></a>När du ska använda ögonblicksbilder för resursen

### <a name="protection-against-application-error-and-data-corruption"></a>Skydd mot fel och data skadas i programmet

Program som använder filresurser filhantering skrivning, läsning, lagring, överföring och bearbetning. Om ett program är felaktigt konfigurerad eller en oavsiktlig bugg introduceras kan oavsiktliga överskrivning eller skada inträffa några block. För att skydda mot dessa scenarier, kan det ta en ögonblicksbild av en resurs innan du distribuerar nya programkod. Om ett programfel eller program fel introducerades i den nya distributionen, går du tillbaka till en tidigare version av dina data på den filresursen. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Skydd mot oavsiktliga borttagningar eller oavsiktliga ändringar

Anta att du arbetar med en textfil i en filresurs. När filen har stängts förlorar du möjligheten att ångra ändringarna. I dessa fall kan behöver du sedan återställa en tidigare version av filen. Du kan använda resursen ögonblicksbilder för att återställa tidigare versioner av filen om den har ändrats eller tagits bort av misstag.

### <a name="general-backup-purposes"></a>Allmän säkerhetskopiering

När du skapar en filresurs, skapar du regelbundet en resurs ögonblicksbild av filresursen som ska användas för säkerhetskopiering av data. En resurs som en ögonblicksbild, när de tas med jämna mellanrum, hjälper till att underhålla tidigare versioner av data som kan användas för framtida granskningskrav eller katastrofåterställning.

## <a name="capabilities"></a>Funktioner

En ögonblicksbild av en resurs är en tidpunkt i, skrivskyddad kopia av dina data. Du kan skapa, ta bort och hantera ögonblicksbilder med hjälp av REST API. Samma funktioner är också tillgängliga i klientbiblioteket, Azure CLI och Azure-portalen. 

Du kan visa ögonblicksbilder av en resurs med hjälp av REST API- och SMB. Du kan hämta listan över versioner av katalogen eller filen och du kan montera en viss version direkt som en enhet. 

När en ögonblicksbild av en resurs har skapats kan kan den läsa, kopieras, eller tas bort, men inte har ändrats. Du kan inte kopiera en ögonblicksbild av hela resursen till ett annat lagringskonto. Du behöver göra att varje fil, med hjälp av AzCopy eller andra kopiering mekanismer.

Dela ögonblicksbild kapaciteten tillhandahålls på filnivå för resursen. Hämtning av tillhandahålls på enskilda filer nivå, så att återställa enskilda filer. Du kan återställa en fullständig filresurs med hjälp av SMB, REST-API, portalen, klientbiblioteket eller PowerShell/CLI verktygsuppsättning.

En resurs ögonblicksbild av en filresurs är identisk med dess grundläggande filresurs. Den enda skillnaden är att en **DateTime** värdet läggs till resurs-URI för att visa den tid då resursen ögonblicksbilden togs. Till exempel om en filresurs URI är http://storagesample.core.file.windows.net/myshare, dela ögonblicksbilden URI liknar:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Dela ögonblicksbilder sparas tills de uttryckligen tas bort. En ögonblicksbild av en resurs kan inte sträcker sig längre än dess grundläggande filresurs. Du kan räkna upp ögonblicksbilder kopplade till grundläggande filresursen att spåra din aktuella ögonblicksbilder. 

När du skapar en ögonblicksbild av en resurs på en filresurs, kopieras filerna i den resursen Systemegenskaper till ögonblicksbilden resurs med samma värden. Grundläggande filer och metadata för den delade filresursen även kopieras till resursen ögonblicksbilden, om du inte anger separat metadata för resursen när du skapar den en ögonblicksbild.

Du kan inte ta bort en resurs som innehåller ögonblicksbilder som andel om du först ta bort alla ögonblicksbilder för resursen.


## <a name="space-usage"></a>Användning av diskutrymme 

Dela ögonblicksbilder är inkrementell till sin natur. Endast de data som har ändrats när din senaste dela ögonblicksbild har sparats. Detta minimerar den tid som krävs för att skapa ögonblicksbilden för resursen och sparar på lagringskostnader. Någon skrivåtgärd till objektet eller egenskapen eller metadata uppdateringsåtgärden räknas mot ”ändrade innehåll” och lagras i resursen ögonblicksbilden. 

Du kan ta bort resursen ögonblicksbilden för perioden när dataomsättningen var högst för att spara utrymme.

Även om resursen ögonblicksbilder sparas inkrementellt, som du vill behålla endast den senaste ögonblicksbilden för resurs för att kunna återställa resursen. När du tar bort en ögonblicksbild av en resurs tas bara data som är unika för att dela ögonblicksbild bort. Aktiva ögonblicksbilder innehåller den information som du behöver bläddra och återställa dina data (från den tid som resursen ögonblicksbilden togs) till den ursprungliga platsen eller en alternativ plats. Du kan återställa på objektnivå.

Ögonblicksbilder räknas inte in 5 TB resursen gränsen. Det finns ingen gräns för hur mycket utrymme resursen ögonblicksbilder uppta totalt. Lagringskontogränser gäller fortfarande.

## <a name="limits"></a>Begränsningar

Det maximala antalet resursen ögonblicksbilder som Azure-filer kan idag är 200. Du måste ta bort äldre resursen ögonblicksbilder för att kunna skapa nya efter 200 resursen ögonblicksbilder. 

Det finns ingen gräns för samtidiga anrop för att skapa ögonblicksbilder av resursen. Det finns ingen gräns för mängden utrymme som delar som kan använda ögonblicksbilder av en viss resurs. 

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Kopiera data till en resurs från resursen ögonblicksbild

Kopieringsåtgärder som inbegriper filer och dela ögonblicksbilder följer dessa regler:

Du kan kopiera enskilda filer i en filresursen Snabböversikt över till dess grundläggande resurs eller någon annan plats. Du kan återställa en tidigare version av en fil eller återställa fullständig filresursen genom att kopiera varje fil från dela ögonblicksbild. Ögonblicksbilden av resursen är inte befordras till grundläggande resursen. 

Dela ögonblicksbild bevaras när du har kopierat, men bas filresursen skrivs över med en kopia av de data som fanns i resursen ögonblicksbilden. Återställda filerna räknas in ”ändra innehållet”.

Du kan kopiera en fil i en ögonblicksbild av en resurs till ett mål med ett annat namn. Resulterande målfilen finns en skrivbar fil och inte en ögonblicksbild av resursen.

När en målfil skrivs över med en kopia, bevaras resursen ögonblicksbilder som är kopplade till den ursprungliga målfilen.

## <a name="general-best-practices"></a>Allmänna Metodtips 

När du kör infrastruktur på Azure, kan du automatisera säkerhetskopior för återställning av data när det är möjligt. Automatiska åtgärder är mer tillförlitligt än manuella processer, hjälper till att förbättra dataskydd och återställning. Du kan använda REST-API, klient-SDK eller skript för automatisering.

Innan du distribuerar dela ögonblicksbild Schemaläggaren måste du noga överväga din resurs ögonblicksbild frekvens och inställningarna för datakvarhållning till att undvika onödiga kostnader.

Dela ögonblicksbilder skyddar bara på filnivå. Dela ögonblicksbilder hindra inte fat-fingeravtrycksläsare borttagningar på ett resurs eller lagring konto. Om du vill skydda ett lagringskonto från oavsiktliga borttagningar, kan du låsa storage-konto eller resursgruppen.

## <a name="next-steps"></a>Nästa steg
* [Arbeta med ögonblicksbilder för resursen](storage-how-to-use-files-snapshots.md)
* [Dela ögonblicksbild vanliga frågor och svar](storage-files-faq.md#share-snapshots)

