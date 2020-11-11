---
title: Fel söknings guide för Azure Files prestanda
description: Felsök kända prestanda problem med Azure-filresurser. Identifiera möjliga orsaker och tillhör ande lösningar när dessa problem påträffas.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 4d21bfa69022cbebdcbf80c3bee4aec76bf99c53
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491128"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Felsöka Azure Files prestanda problem

Den här artikeln innehåller några vanliga problem som rör Azure-filresurser. Den ger potentiella orsaker och lösningar när dessa problem uppstår.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Hög latens, lågt data flöde och allmänna prestanda problem

### <a name="cause-1-share-was-throttled"></a>Orsak 1: resursen har begränsats

Begär Anden begränsas när IOPS, ingress eller utgående gräns för en fil resurs nås. Information om begränsningar för fil resurser för standard och Premium finns i [fil resurs-och fil skalnings mål](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#file-share-and-file-scale-targets).

Du kan använda Azures mått i portalen för att kontrol lera om din resurs är begränsad.

1. I Azure Portal går du till ditt lagrings konto.

1. Välj **mått** i den vänstra menyn under **övervakning**.

1. Välj **fil** som mått namn område för ditt lagrings konto omfång.

1. Välj **transaktioner** som mått.

1. Lägg till ett filter för **ResponseType** och kontrol lera om det finns några begär Anden som har svars koden **SUCCESSWITHTHROTTLING** (för SMB) eller **ClientThrottlingError** (för rest).

![Mått alternativ för Premium-fil resurser](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Om du vill få en avisering om en fil resurs är begränsad, se [så här skapar du en avisering om en fil resurs är begränsad](#how-to-create-an-alert-if-a-file-share-is-throttled).

### <a name="solution"></a>Lösning

- Om du använder en standard fil resurs aktiverar du [stora fil resurser](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share?tabs=azure-portal) på ditt lagrings konto. Stora fil resurser har stöd för upp till 10 000 IOPS per resurs.
- Om du använder en Premium-filresurs ökar du den etablerade fil resurs storleken för att öka IOPS-gränsen. Mer information finns i avsnittet [förstå etablering för Premium-filresurser](https://docs.microsoft.com/azure/storage/files/storage-files-planning#understanding-provisioning-for-premium-file-shares) i Azure Files Planning Guide.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Orsak 2: metadata/namnrymd, tungt arbets belastning

Om majoriteten av dina begär Anden är icke-koncentriska metadata (till exempel CreateFile/OpenFile/closefile/queryinfo/querydirectory) blir svars tiden sämre jämfört med Läs-och skriv åtgärder.

Du kan använda samma steg som ovan för att kontrol lera om de flesta av dina begär Anden är koncentriska metadata. Lägg till ett filter för **API-namn** , förutom i stället för att lägga till ett filter för **ResponseType**.

![Filtrera efter API-namn i dina mått](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Lösning

- Kontrol lera om programmet kan ändras för att minska antalet metadata-åtgärder.
- Lägg till en virtuell hård disk på fil resursen och montera VHD över SMB från klienten för att utföra fil åtgärder mot data. Den här metoden fungerar för en enskild skrivare och flera läsare scenarier och tillåter att metadata-åtgärder är lokala, vilket ger prestanda som liknar en lokal direktansluten lagring.

### <a name="cause-3-single-threaded-application"></a>Orsak 3: program med enkel tråd

Om programmet som används av kunden är en enkel tråd kan detta leda till betydligt lägre IOPS/data flöde än vad som är möjligt utifrån den etablerade resurs storleken.

### <a name="solution"></a>Lösning

- Öka programmets parallellitet genom att öka antalet trådar.
- Växla till program där parallellitet är möjligt. För kopierings åtgärder kan kunder till exempel använda AzCopy eller RoboCopy från Windows-klienter eller **parallellt** kommando på Linux-klienter.

## <a name="very-high-latency-for-requests"></a>Mycket hög svars tid för begär Anden

### <a name="cause"></a>Orsak

Den virtuella klient datorn kan finnas i en annan region än fil resursen.

### <a name="solution"></a>Lösning

- Kör programmet från en virtuell dator som finns i samma region som fil resursen.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Klienten kunde inte uppnå maximalt data flöde som stöds av nätverket

En möjlig orsak till detta är att det inte finns stöd för SMB-stöd för flera kanaler. Azure-filresurser stöder för närvarande bara en kanal, så det finns bara en anslutning från den virtuella klient datorn till servern. Den här enskilda anslutningen peggas till en enda kärna på den virtuella klient datorn, så det maximala data flödet som kan nås från en virtuell dator binds till en enda kärna.

### <a name="workaround"></a>Lösning

- Att hämta en virtuell dator med en större kärna kan hjälpa till att förbättra data flödet.
- Genom att köra klient programmet från flera virtuella datorer ökar du data flödet.

- Använd REST-API: er där det är möjligt.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Data flödet på Linux-klienter är betydligt lägre jämfört med Windows-klienter.

### <a name="cause"></a>Orsak

Detta är ett känt problem med implementeringen av SMB-klienten på Linux.

### <a name="workaround"></a>Lösning

- Sprida belastningen över flera virtuella datorer.
- Använd flera monterings punkter med alternativet **nosharesock** på samma virtuella dator och sprid belastningen över dessa monterings punkter.
- På Linux kan du prova att montera med alternativet **nostrictsync** för att undvika att framtvinga SMB-tömning på varje **fsync** -anrop. För Azure Files stör inte det här alternativet data konsekvens, men kan resultera i inaktuella fil-metadata på katalog listan ( **ls-l-** kommando). Om du direkt frågar efter metadata för filen ( **stat** -kommandot) returneras de senaste metadata som är aktuella för filen.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Hög latens för Metadatas tungt arbets belastningar som involverar omfattande öppna/stäng-åtgärder.

### <a name="cause"></a>Orsak

Saknar stöd för katalog lån.

### <a name="workaround"></a>Lösning

- Undvik att öppna och stänga av samma katalog inom en kort tids period om det är möjligt.
- För virtuella Linux-datorer ökar du timeout-värdet för katalog post genom att ange **actimeo = \<sec>** som ett monterings alternativ. Som standard är det en sekund, så ett större värde som tre eller fem kan hjälpa dig.
- För virtuella datorer med RHEL/CentOS uppgraderar du systemet till RHEL/CentOS 8,2. För andra virtuella Linux-datorer uppgraderar du kernel till 5,0 eller högre.

## <a name="low-iops-on-centosrhel"></a>Låga IOPS på CentOS/RHEL

### <a name="cause"></a>Orsak

I/o-djupet är större än ett stöds inte på CentOS/RHEL.

### <a name="workaround"></a>Lösning

- Uppgradera till CentOS 8/RHEL 8.
- Ändra till Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Långsam fil kopiering till och från Azure Files i Linux

Om du upplever långsam fil kopiering till och från Azure Files kan du ta en titt på [filen för långsam fil kopiering till och från Azure Files i Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) i fel söknings guiden för Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Darr/såg-Tooth-mönster för IOPS

### <a name="cause"></a>Orsak

Klient programmet överskrider konsekventa bas linje IOPS. För närvarande finns det ingen utjämning av belastningen på tjänst sidan, så om klienten överskrider en bas linje för IOPS, kommer den att få en begränsning av tjänsten. Den begränsningen kan resultera i att klienten har ett Darr/såg-Tooth IOPS-mönster. I det här fallet kan den genomsnittliga IOPS som uppnås av klienten vara lägre än bas linjens IOPS.

### <a name="workaround"></a>Lösning

- Minska belastningen på begäran från klient programmet, så att resursen inte får någon begränsning.
- Öka kvoten för resursen så att resursen inte får någon begränsning.

## <a name="excessive-directoryopendirectoryclose-calls"></a>För många DirectoryOpen/DirectoryClose-anrop

### <a name="cause"></a>Orsak

Om antalet DirectoryOpen/DirectoryClose-anrop är bland de främsta API-anropen och du inte förväntar dig att klienten ska göra många anrop, kan det vara ett problem med antivirus programmet som är installerat på den virtuella Azure-klientdatorn.

### <a name="workaround"></a>Lösning

- En korrigering för det här problemet finns i [april Platform Update för Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Filen har skapats långsammare än förväntat

### <a name="cause"></a>Orsak

Arbets belastningar som förlitar sig på att skapa ett stort antal filer kommer inte att se en stor skillnad mellan prestanda i Premium fil resurser och standard fil resurser.

### <a name="workaround"></a>Lösning

- Inga.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Långsamma prestanda från Windows 8,1 eller Server 2012 R2

### <a name="cause"></a>Orsak

Högre än förväntad fördröjning vid åtkomst till Azure Files för i/o-intensiva arbets belastningar.

### <a name="workaround"></a>Lösning

- Installera den tillgängliga [snabb korrigeringen](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Så här skapar du en avisering om en fil resurs är begränsad

1. Gå till ditt **lagrings konto** i **Azure Portal**.
2. I avsnittet övervakning klickar du på **aviseringar** och klickar sedan på **+ ny varnings regel**.
3. Klicka på **Redigera resurs** , Välj **fil resurs typ** för lagrings kontot och klicka sedan på **färdig**. Om lagrings konto namnet till exempel är contoso väljer du Contoso/File-resursen.
4. Klicka på **Välj villkor** för att lägga till ett villkor.
5. Du kommer att se en lista över signaler som stöds för lagrings kontot. Välj måttet **transaktioner** .
6. På bladet **Konfigurera signal logik** klickar du på list rutan **Dimensions namn** och väljer **svarstyp**.
7. Klicka på list rutan **Dimensions värden** och välj **SUCCESSWITHTHROTTLING** (för SMB) eller **ClientThrottlingError** (för rest).

   > [!NOTE]
   > Om dimension svärdet SuccessWithThrottling eller ClientThrottlingError inte visas innebär det att resursen inte har begränsats. Lägg till dimension svärdet genom att klicka på **Lägg till anpassat värde** bredvid List rutan **Dimensions värden** , Skriv **SuccessWithThrottling** eller **ClientThrottlingError** , klicka på **OK** och upprepa steg #7.

8. Klicka på list rutan **Dimensions namn** och välj **fil resurs**.
9. Klicka på list rutan **Dimensions värden** och välj den eller de fil resurser som du vill Avisera om.

   > [!NOTE]
   > Om fil resursen är en standard fil resurs väljer du **alla aktuella och framtida värden**. List rutan med dimensions värden visar inte fil resurserna eftersom det inte finns några tillgängliga fil resurser per resurs. Begränsnings varningar för standard fil resurser utlöses om någon fil resurs på lagrings kontot är begränsad och aviseringen inte kommer att identifiera vilken fil resurs som har begränsats. Eftersom per resurs-mått inte är tillgängliga för standard fil resurser, är rekommendationen att ha en fil resurs per lagrings konto.

10. Definiera **aviserings parametrarna** (tröskelvärde, Operator, agg regerings precision och frekvens för utvärderingen) och klicka på **Slutför**.

    > [!TIP]
    > Om du använder ett statiskt tröskelvärde kan mått diagrammet hjälpa till att fastställa ett rimligt tröskelvärde om fil resursen för närvarande begränsas. Om du använder ett dynamiskt tröskelvärde visar mått diagrammet de beräknade tröskelvärdena baserat på aktuella data.

11. Klicka på **Välj åtgärds grupp** för att lägga till en **Åtgärds grupp** (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.
12. Fyll i **aviserings informationen** som **aviserings regelns namn** , **Beskrivning** och **allvarlighets grad**.
13. Klicka på **skapa aviserings regel** för att skapa aviseringen.

Mer information om hur du konfigurerar aviseringar i Azure Monitor finns i [Översikt över aviseringar i Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-towards-being-throttled"></a>Så här skapar du aviseringar om en Premium-filresurs tendenser ut mot begränsning

1. Gå till ditt **lagrings konto** i **Azure Portal**.
2. I avsnittet övervakning klickar du på **aviseringar** och klickar sedan på **+ ny varnings regel**.
3. Klicka på **Redigera resurs** , Välj **fil resurs typ** för lagrings kontot och klicka sedan på **färdig**. Om lagrings konto namnet till exempel är contoso väljer du Contoso/File-resursen.
4. Klicka på **Välj villkor** för att lägga till ett villkor.
5. Du kommer att se en lista över signaler som stöds för lagrings kontot och välja **utgående** mått.

   > [!NOTE]
   > Du måste skapa tre separata aviseringar för att få en avisering när ingångs-, utgångs-eller transaktioner överskrider den tröskel mängd som du har angett. Detta beror på att en avisering endast utlöses när alla villkor är uppfyllda. Så om du placerar alla villkor i en avisering är du bara aviserad om ingångs-, utgångs-och transaktions belopp överskred tröskelvärdena.

6. Rulla nedåt. Klicka på list rutan **Dimensions namn** och välj **fil resurs**.
7. Klicka på list rutan **Dimensions värden** och välj den eller de fil resurser som du vill Avisera om.
8. Definiera **aviserings parametrarna** (tröskelvärde, Operator, agg regerings precision och frekvens för utvärderingen) och klicka på **Slutför**.

   > [!NOTE]
   > Utgångs-, ingångs-och transaktions måtten är per minut, även om du har tilldelat ut, ingress och IOPS per sekund. (prata om agg regerings kornig het – > per minut = mer brus, så välj diff en) Om du till exempel har en allokerad utgång är 90 MiB/sekund och du vill att ditt tröskelvärde ska vara 80% av etableringen, bör du välja följande aviserings parametrar: 75497472 för **tröskelvärdet** , större än eller lika med för **operator** och genomsnitt för **agg regerings typ**. Beroende på hur brus du vill att din avisering ska vara, kan du välja vilka värden du vill välja för agg regerings precision och utvärderings frekvens. Om jag till exempel vill att min avisering ska titta på genomsnitts tiden under en timmes tids period och jag vill att min varnings regel ska köras varje timme väljer jag 1 timme för **agg regerings precision** och 1 timme för **utvärderings frekvens**.

9. Klicka på **Välj åtgärds grupp** för att lägga till en **Åtgärds grupp** (e-post, SMS osv.) till aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.
10. Fyll i **aviserings informationen** som **aviserings regelns namn** , **Beskrivning** och **allvarlighets grad**.
11. Klicka på **skapa aviserings regel** för att skapa aviseringen.

    > [!NOTE]
    > Om du vill få ett meddelande om Premium-filresursen är nära begränsad till följd av insamlade ingångar följer du samma steg, förutom i steg 5, väljer du ingångs **mått i** stället.

    > [!NOTE]
    > Om du vill få ett meddelande om Premium-filresursen är nära begränsad till begränsning på grund av etablerade IOPS måste du göra några ändringar. I steg 5 väljer du ett **transaktions** mått i stället. För steg 10 är det enda alternativet för **agg regerings typ** totalt. Därför skulle tröskelvärdet vara beroende av den valda agg regerings precisionen. Om du till exempel vill att tröskelvärdet ska vara 80% av etablerad bas linje IOPS och du har valt 1 timme för **agg regerings precision** , skulle ditt **tröskelvärde** vara din bas linje för IOPS (i byte) x 0,8 x 3600. Förutom dessa ändringar följer du samma steg som ovan. 

Mer information om hur du konfigurerar aviseringar i Azure Monitor finns i [Översikt över aviseringar i Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="see-also"></a>Se även
* [Felsöka Azure Files i Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Felsöka Azure Files i Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Vanliga frågor och svar om Azure Files](storage-files-faq.md)
