---
title: "Alternativ för Azure Storage-konton | Microsoft Docs"
description: "Information om alternativ för Azure Storage."
services: storage
documentationcenter: 
author: jirwin
manager: jwillis
editor: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/11/2017
ms.author: jirwin
ms.openlocfilehash: 1b1770e25b4b423466120cb74c08edacf2de3977
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="azure-storage-account-options"></a>Alternativ för Azure Storage-konton

## <a name="overview"></a>Översikt

Azure Storage tillhandahåller tre olika kontoalternativ, med olika priser och olika funktioner som stöds. Det är viktigt att alla användare utvärderar skillnaderna för att komma fram till det alternativ som passar dem bäst.  Följande tre alternativ erbjuds:

* **GPv2-konton (General Purpose v2)** innehåller alla de senaste funktionerna och har stöd för blobbar, filer, köer och tabeller. De senaste funktionerna innefattar i dag blobnivåindelning, arkivlagring, större tillåtna kontoutrymmen och lagringshändelser. Prisstrukturen har utformats med låga GB-priser och konkurrenskraftiga transaktionspriser.

* **Blob Storage**-konton innefattar alla de senaste funktionerna för blockblobbar, men har endast stöd för blockblobbar.  Prisstrukturen liknar den som används för General Purpose v2. Vi rekommenderar de flesta användare att välja GPv2-konton i stället för Blob Storage-konton.

* **GPv1-konton (General Purpose v1)** gör det möjligt att använda alla Azure Storage-tjänster, men vissa av de senaste funktionerna kan saknas och GB-priserna är högre. Lågfrekvent lagring och arkivlagring är två exempel på funktioner som inte stöds av GPv1.  Transaktionspriserna är lägre, så arbetsbelastningar med hög omsättning eller många läsåtgärder kan ha nytta av den här kontotypen.

### <a name="changing-account-kind"></a>Ändra kontotyp
Användare kan när som helst uppgradera ett GPv1-konto till ett GPv2-konto via Portal, CLI eller PowerShell. Den här ändringen kan inte ångras och inga andra ändringar tillåts. Möjlighet att uppgradera Blob Storage-konton till GPv2 kommer snart.

## <a name="general-purpose-v2"></a>General Purpose v2
**GPv2-konton (General Purpose v2)** är lagringskonton som stöder alla funktioner för alla lagringstjänster, inklusive blobbar, filer, köer och tabeller. För blockblobbar kan du ange frekvent och lågfrekvent lagringsnivå på kontonivå, eller ange frekvent eller lågfrekvent nivå eller arkivnivå på blobnivå, baserat på åtkomstmönster. Lagra data som sällan, mer sällan eller ofta används på lagringsnivåerna för frekvent eller lågfrekvent åtkomst eller arkivering för att optimera kostnaderna. Ett GPv1-konto kan alltid uppgraderas till ett GPv2-konto i portalen, CLI eller PowerShell. GPv2-konton stöder alla API:er och funktioner som stöds i Blob Storage- och GPv1-konton, och har samma goda egenskaper vad gäller hållbarhet, tillgänglighet, skalbarhet och prestanda som dessa kontotyper.

I Blob Storage-konton visas attributet **Åtkomstnivå** på kontonivå, vilket innebär att standardlagringsnivån för kontot kan anges till **Frekvent** eller **Lågfrekvent**. Lagringskontonivån som är standard tillämpas på alla blobar som inte har en explicit nivå angiven på blobnivån. Du kan när som helst byta mellan de olika lagringsnivåerna om användningsmönstret förändras. **Arkivnivån** kan endast användas på blobnivån.

> [!NOTE]
> Ändringar av lagringsnivån kan medför ytterligare avgifter. Mer information finns i avsnittet [Priser och fakturering](#pricing-and-billing).

## <a name="blob-storage-accounts"></a>Blob Storage-konton

**Blob Storage-konton** stöder samma funktioner för blockblobbar som GPv2, men stödet är begränsat till blockblobbar. Alla kunder bör granska prisskillnaderna mellan Blob Storage- och GPv2-konton, och överväga att uppgradera till GPv2. Observera att den här uppgraderingen inte kan ångras.

> [!NOTE]
> Blob Storage-konton stöder endast block- och tilläggsblobbar, inte sidblobbar.

## <a name="general-purpose-v1"></a>General Purpose v1
**GPv1-konton (General Purpose v1)** är den äldsta typen av lagringskonto, och det är endast denna typ som kan användas i den klassiska distributionsmodellen. Funktioner som lågfrekvent lagring och arkivlagring är inte tillgängliga i GPv1-konton. För GPv1 gäller vanligtvis högre GB-lagringskostnader men lägre transaktionskostnader än för GPv2- och Blob Storage-konton.

## <a name="recommendations"></a>Rekommendationer

Mer information om lagringskonton finns i [Om Azure Storage-konton](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Om du endast behöver block- eller tilläggsbloblagring rekommenderar vi att du använder GPv2-konton så att du kan dra nytta av de olika prissättningsmodellerna för olika lagringsnivåer. I vissa fall kan det dock ändå vara bättre att använda GPv1-konton, till exempel om:

* Du fortfarande måste använda den klassiska distributionsmodellen. Blob Storage-konton endast är tillgängliga via Azure Resource Manager-distributionsmodellen.

* Du har stora transaktionsvolymer eller behöver stor bandbredd för geo-replikering, vilket ger högre kostnader med GPv2- och Blob Storage-konton än med GPv1, samtidigt som du inte har tillräckligt med lagringsutrymme för att kunna dra nytta av de lägre kostnaderna för GB-lagring.

* Du använder en version av [Storage Services REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) som är äldre än 2014-02-14 eller ett klientbiblioteket med en tidigare version än 4.x och det inte går att uppgradera ditt program.

> [!NOTE]
> Blob Storage-konton stöds för närvarande i alla Azure-regioner.

## <a name="pricing-and-billing"></a>Priser och fakturering
För alla lagringskonton används en prissättningsmodell för bloblagring som baseras på nivån för varje blob. När du använder ett lagringskonto gäller följande för debitering:

* **Lagringskostnader**: Utöver mängden data som lagras varierar lagringskostnaden beroende på lagringsnivå. Kostnaden per gigabyte minskas när nivån blir mer lågfrekvent.

* **Kostnader för dataåtkomst**: Kostnaderna för dataåtkomst ökar när nivån blir mer lågfrekvent. För data på den lågfrekventa- och arkivlagringsnivån debiteras du en åtkomstavgift per gigabyte för läsningar.

* **Transaktionskostnader**: Du debiteras en kostnad per transaktion för alla nivåer som ökar när nivån blir mer lågfrekvent.

* **Dataöverföringskostnader för geo-replikering**: Detta gäller endast konton med konfigurerad geo-replikering, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.

* **Kostnader för utgående dataöverföring**: Utgående dataöverföringar (data som överförs utanför en Azure-region) debiteras för bandbreddsanvändning per gigabyte, på samma sätt som för allmänna lagringskonton.

* **Ändring av lagringsnivå**: Om du byter lagringskontonivå från lågfrekvent till frekvent utgår en avgift motsvarande läsningen av alla data på lagringskontot. Om du byter lagringskontonivå från frekvent till lågfrekvent utgår dock en avgift som motsvarar skrivningen av alla data till den lågfrekventa nivån (gäller endast GPv2-konton).

> [!NOTE]
> Mer information om prissättningen för Blob Storage-konton finns på sidan [Pris för Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Mer information om kostnaderna för utgående dataöverföring finns på sidan [Prisinformation om Dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Snabbstartsscenarier

I det här avsnittet visas följande scenarier på Azure Portal:

* Skapa ett GPv2-lagringskonto.
* Konvertera ett GPv1- eller Blob Storage-konto till ett GPv2-lagringskonto.
* Ange lagringsnivåer på konto- och blobnivå för ett GPv2-lagringskonto.

Du kan inte ange arkiv som åtkomstnivå i följande exempel eftersom den här inställningen gäller för hela lagringskontot. Arkiv kan bara anges för en specifik blob.

### <a name="create-a-gpv2-storage-account-using-the-azure-portal"></a>Skapa ett GPv2-lagringskonto med Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. På navmenyn väljer du **Nytt** > **Data + Storage** > **Storage-konto**.

3. Ange ett namn för lagringskontot.

    Det här namnet måste vara globalt unikt. Det används som en del av URL:en som används för att få åtkomst till objekt i lagringskontot.  

4. Välj **Resource Manager** som distributionsmodell.

    Nivåindelad lagring kan bara användas med Resource Manager-lagringskonton. Det här är den rekommenderade distributionsmodellen för nya resurser. Mer information finns i [Översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).  

5. I listrutan Typ av konto väljer du**General Purpose v2**.

    Det är här du väljer typen av lagringskonto. Nivåindelad lagring är inte tillgängligt för allmän lagring. Det är endast tillgängligt med Blob Storage-konton.     

    När du väljer detta anges prestandanivån till Standard. Nivåindelad lagring är inte tillgängligt med Premium-prestandanivån.

6. Välj replikeringsalternativ för lagringskontot: **LRS**, **GRS** eller **RA-GRS**. Standardinställningen är **RA-GRS**.

    LRS = lokalt redundant lagring. GRS = geo-redundant lagring (två regioner). RA-GRS är skrivskyddad geo-redundant lagring (två regioner med läsbehörighet till den andra).

    Mer information om replikeringsalternativen för Azure Storage finns i [Azure Storage-replikering](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

7. Välj rätt lagringsnivå för dina behov: Ange **Åtkomstnivå** till antingen **Cool** (lågfrekvent) eller **Hot** (frekvent). Standardinställningen är **Frekvent**.

8. Välj den prenumeration som du vill skapa det nya lagringskontot i.

9. Ange en ny resursgrupp eller välj en befintlig resursgrupp. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

10. Välj regionen för ditt lagringskonto.

11. Skapa lagringskontot genom att klicka på **Skapa**.

### <a name="convert-a-gpv1-account-to-a-gpv2-storage-account-using-the-azure-portal"></a>Konvertera ett GPv1-konto till ett GPv2-lagringskonto med Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Gå till ditt lagringskonto genom att välja Alla resurser och välj sedan ditt lagringskonto.

3. På bladet Inställningar klickar du på **Konfiguration**.

4. Under Typ av konto klickar du på **Uppgradera**.

5. Ett nytt blad visas till höger för att bekräfta valet. Ange namnet på ditt konto under Bekräfta uppgradering. 

5. Klicka på Uppgradera längst ned på bladet.

### <a name="change-the-storage-tier-of-a-gpv2-storage-account-using-the-azure-portal"></a>Ändra lagringsnivån för ett GPv2-konto med hjälp av Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Gå till ditt lagringskonto genom att välja Alla resurser och välj sedan ditt lagringskonto.

3. Klicka på **Konfiguration** på bladet Inställningar för att visa och/eller ändra kontokonfigurationen.

4. Välj rätt lagringsnivå för dina behov: Ange **Åtkomstnivå** till antingen **Cool** (lågfrekvent) eller **Hot** (frekvent).

5. Klicka på Spara överst på bladet.

### <a name="change-the-storage-tier-of-a-blob-using-the-azure-portal"></a>Ändra lagringsnivån för en blob med hjälp av Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Gå till din blob i lagringskontot genom att välja Alla resurser, ditt lagringskonto, din behållare och sedan välja din blob.

3. På bladet Blob-egenskaper klickar du på listrutemenyn **Åtkomstnivå** för att välja lagringsnivåerna **Frekvent**, **Lågfrekvent**, eller **Arkiv**.

5. Klicka på Spara överst på bladet.

> [!NOTE]
> Ändringar av lagringsnivån kan medför ytterligare avgifter. Mer information finns i avsnittet [Priser och fakturering](#pricing-and-billing).


## <a name="evaluating-and-migrating-to-gpv2-storage-accounts"></a>Utvärdera och migrera till GPv2-konton
Avsikten med det här avsnittet är att hjälpa användarna med övergången till GPv2-konton (detta gäller alltså inte GPv1-konton). Det finns två scenarier:

* Du har ett befintligt GPv1-konto och vill utvärdera en övergång till ett GPv2-konto med rätt lagringsnivå.
* Du har bestämt dig för att använda ett GPv2-konto eller har redan ett sådant, och vill utvärdera om du ska använda lågfrekvent eller frekvent lagringsnivå.

I båda fallen bör du börja med att beräkna kostnaden för att lagra och komma åt dina data i GPv2-kontot och jämföra det med dina nuvarande kostnader.

## <a name="evaluating-gpv2-storage-account-tiers"></a>Utvärdera GPv2-kontonivåer

För att beräkna kostnaden för att lagra och komma åt data som lagras i ett GPv2-konto måste du utvärdera ditt nuvarande användningsmönster eller göra en uppskattning av ditt förväntade användningsmönster. Vanligtvis vill du veta:

* Din lagringsanvändning – hur mycket data lagras och hur ändras lagringen månadsvis?

* Ditt åtkomstmönster – hur mycket data läses in och skrivs till kontot (inklusive nya data)? Hur många transaktioner används för dataåtkomst och vilka typer av transaktioner är de?

## <a name="monitoring-existing-storage-accounts"></a>Övervaka befintliga lagringskonton

Om du vill övervaka dina befintliga lagringskonton och samla in dessa data kan du använda Azure-lagringsanalys som utför loggning och tillhandahåller mätvärden för ett lagringskonto. Azure-lagringsanalys kan lagra mätvärden som innehåller aggregerad transaktionsstatistik och kapacitetsdata om förfrågningar till lagringstjänsten för konton av typerna GPv1, GPv2 och Blob Storage. Dessa data lagras i välkända tabeller i samma lagringskonto.

Mer information finns i [Om mätvärden i Storage Analytics](https://msdn.microsoft.com/library/azure/hh343258.aspx) och [Schema över måttabeller i Storage Analytics](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> Blob Storage-konton exponerar endast tabelltjänstens slutpunkt för att lagra och komma åt mätvärden för det aktuella kontot. GPv1 ZRS-lagringskonton har inte stöd för mätvärdesdata.

Om du vill övervaka lagringsanvändningen för Blob Storage-tjänsten måste du aktivera kapacitetsmåtten.
När du har gjort det registreras kapacitetsdata varje dag för ett lagringskontos Blob Service och registreras som en tabellpost som skrivs till tabellen *$MetricsCapacityBlob* i samma lagringskonto.

Om du vill övervaka dataåtkomstmönstret för Blob Storage-tjänsten måste du aktivera transaktionsmått för varje timme på API-nivå. När du har gjort det aggregeras transaktioner för varje API varje timme, och registreras som en tabellpost som skrivs till tabellen *$MetricsHourPrimaryTransactionsBlob* i samma lagringskonto. Tabellen *$MetricsHourSecondaryTransactionsBlob* registrerar transaktionerna till den sekundära slutpunkten när du använder RA-GRS-lagringskonton.

> [!NOTE]
> Om du har ett allmänt lagringskonto (GPvX) där du har lagrat sidblobbar och virtuella datordiskar, eller köer, filer eller tabeller, utöver block- och tilläggsblobdata så gäller inte den här uppskattningsberäkningen. Detta beror på att kapacitetsdata inte visas med åtskillnad mellan blockblobbar och andra typer, och kapacitetsdata visas inte för andra datatyper. Om du använder dessa typer är en alternativ metod att titta på kvantiteterna på din senaste faktura.

För att få en bra uppskattning av din dataförbrukning och ditt åtkomstmönster rekommenderar vi att du väljer en kvarhållningsperiod för mätvärden som är representativ för din normala användning, och att du utgår därifrån. Ett alternativ är att spara mätvärdena i sju dagar och samla in data varje vecka, för att sedan analysera dessa data i slutet av månaden. Ett annat alternativ är att spara mätvärden i 30 dagar och samla in och analysera dessa data i slutet av 30-dagarsperioden.

Mer information om hur du aktiverar, samlar in och visar mätvärden finns i [Aktivera Azure Storage-mätvärden och visa måttdata](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Lagring, åtkomst och hämtning av analysdata debiteras på samma sätt som vanliga användardata.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Beräkna kostnader med hjälp av användningsmått

### <a name="storage-costs"></a>Lagringskostnader

Den senaste posten i kapacitetsmåttstabellen *$MetricsCapacityBlob* med radnyckeln *'data'* visar lagringskapacitet som förbrukats av användardata. Den senaste posten i kapacitetsmåttstabellen *$MetricsCapacityBlob* med radnyckeln *'analytics'* visar lagringskapaciteten som förbrukats av analysloggarna.

Den här totala kapaciteten som förbrukats av både användardata och analysloggar (om detta aktiverats) kan sedan användas för att beräkna kostnaden för datalagring i lagringskontot. Samma metod kan även användas för att uppskatta lagringskostnader för GPv1-konton.

### <a name="transaction-costs"></a>Transaktionskostnader

Summan av *'TotalBillableRequests'*, för alla poster för ett API i tabellen över transaktionsmått, visar det totala antalet transaktioner för API:et i fråga. *Till exempel* kan det totala antalet *'GetBlob'*-transaktioner inom en viss period beräknas baserat på summan av totalt antal debiterbara begäranden för alla poster med radnyckeln *'user;GetBlob'*.

För att kunna beräkna transaktionskostnader för Blob Storage-konton måste du dela in transaktionerna i tre grupper eftersom de prissätts på olika sätt.

* Skrivtransaktioner som *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* och *'CopyBlob'*.
* Borttagningstransaktioner som *'DeleteBlob'* och *'DeleteContainer'*.
* Alla andra transaktioner.

För att kunna beräkna transaktionskostnaderna för GPv1-konton måste du aggregera alla transaktioner oavsett åtgärd/API.

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Dataöverföringskostnader för dataåtkomst och geo-replikering

Lagringsanalyserna visar inte mängden data som har lästs eller skrivits till ett lagringskonto, men du kan göra en ungefärlig uppskattning genom att titta i tabellen över transaktionsmått. Summan av *'TotalIngress'* för alla poster för ett API i tabellen över transaktionsmått anger den totala mängden inkommande data i antal byte för API:et i fråga. På samma sätt anger summan av *'TotalEgress'* den totala mängden utgående data i antal byte.

För att kunna beräkna kostnaderna för dataåtkomst för Blob Storage-konton måste du dela in transaktionerna i två grupper.

* Du kan beräkna mängden data som hämtas från lagringskontot genom att titta på summan av *'TotalEgress'* och särskilt för åtgärderna *'GetBlob'* och *'CopyBlob'*.

* Du kan beräkna mängden data som skrivs till lagringskontot genom att titta på summan av *'TotalIngress'* och särskilt för åtgärderna *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* och *'AppendBlock'*.

När du använder ett GRS- eller RA-GRS-lagringskonto kan kostnaden för dataöverföring med geo-replikering för Blob Storage-konton också beräknas baserat på uppskattningen av mängden data som skrivits.

> [!NOTE]
> Ett mer detaljerat exempel på hur du beräknar kostnaderna för den frekventa eller lågfrekventa lagringsnivån finns i frågeavsnittet *”Vad är lågfrekvent och frekvent lagringsnivå och hur vet jag vilken jag ska använda?”* på [Azure Storage-prissidan](https://azure.microsoft.com/pricing/details/storage/).

## <a name="migrating-existing-data"></a>Migrera befintliga data

Ett GPv1-konto kan enkelt uppgraderas till GPv2 utan driftavbrott eller API-ändringar, och utan att du behöver flytta data. Detta är en av de främsta fördelarna med GPv2 jämfört med Blob Storage-konton.

Om du behöver migrera till ett Blob Storage-konto kan du använda instruktionerna nedan.

Ett Blob Storage-konto är specialanpassat för lagring av endast block- och tilläggsblobbar. Befintliga allmänna lagringskonton, där du kan lagra tabeller, köer, filer och diskar utöver blobbar, kan inte omvandlas till Blob Storage-konton. Om du vill använda lagringsnivåer måste du skapa Blob Storage-konton och migrera dina befintliga data till de nya kontona.

Med följande metoder kan du migrera befintliga data till Blob Storage-konton från lokala lagringsenheter, från externa molnlagringsleverantörer eller från befintliga allmänna lagringskonton i Azure:

### <a name="azcopy"></a>AzCopy

AzCopy är ett Windows-kommandoradsverktyg för högpresterande kopiering av data till och från Azure Storage. Med AzCopy kan du kopiera data till ditt Blob Storage-konto från dina befintliga allmänna lagringskonton eller överföra data från dina lokala lagringsenheter till ditt Blob Storage-konto.

Mer information finns i [Transfer data with the AzCopy Command-Line Utility](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Bibliotek för flytt av data

Azure Storage-biblioteket för flytt av data för .NET är baserat på det ramverk för flytt av data som används för AzCopy. Biblioteket är utformat för högpresterande, tillförlitliga och enkla åtgärder för dataöverföring och liknar de som används i AzCopy. Du kan utnyttja fördelarna med funktionerna som tillhandahålls av AzCopy direkt i din app utan att du behöver köra och övervaka externa instanser av AzCopy.

Mer information finns i [Azure Storage Data Movement Library for .Net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST-API eller klientbibliotek

Du kan skapa en anpassad app för att migrera dina data till ett Blob Storage-konto med Azures klientbibliotek eller REST-API:t för Azure Storage-tjänster. Azure Storage innehåller omfattande klientbibliotek för flera språk och plattformar som .NET, Java, C++, Node.JS, PHP, Ruby och Python. Klientbiblioteken har avancerade funktioner, t.ex. logik för omprövning, loggning och parallell överföring. Du kan också utveckla direkt mot REST-API:t, som kan anropas med valfritt språk som kan skicka HTTP/HTTPS-begäranden.

Mer information finns i [Komma igång med Azure Blob Storage](../blobs/storage-dotnet-how-to-use-blobs.md).

> [!NOTE]
> Blobar som krypteras med kryptering på klientsidan lagrar krypteringsrelaterade metadata tillsammans med bloben. Det är absolut nödvändigt att kopieringsmekanismen ser till att blobmetadata, och särskilt krypteringsrelaterade metadata, bevaras. Om du kopierar blobar utan metadata kan blobinnehållet inte hämtas igen. Mer information om krypteringsrelaterade metadata finns i [Azure Storage Client Side Encryption](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Är de befintliga lagringskontona fortfarande tillgängliga?**

Ja, de befintliga lagringskontona (GPv1) är fortfarande tillgängliga. Varken priserna eller funktionerna har ändrats.  Du kan inte välja lagringsnivå med GPv1-konton och du kommer inte heller att kunna göra det i framtiden.

**När och varför ska jag börja använda GPv2-konton?**

GPv2-konton är särskilt utformade för att tillhandahålla låga GB-lagringskostnader och konkurrenskraftiga kostnader för transaktioner och dataåtkomst. Vi rekommenderar att du använder GPv2-konton i fortsättningen för att lagra blobbar eftersom framtida funktioner, t.ex. ändringsmeddelanden, kommer att läggas till för den här kontotypen. Det är naturligtvis upp till dig när du vill uppgradera beroende på dina affärsbehov.  Du kan till exempel välja att optimera dina transaktionsmönster innan du uppgraderar.

Eftersom du inte kan nedgradera från GPv2 bör du fundera över vad detta har för påverkan på kostnaderna innan du uppgraderar dina konton till GPv2.

**Kan jag uppgradera mitt befintliga lagringskonto till ett GPv2-lagringskonto?**

Ja. GPv1-konton kan enkelt uppgraderas till GPv2 i Portal, eller med PowerShell eller CLI. Blob Storage-konton kan uppgraderas till GPv2 med PowerShell eller CLI. Möjlighet att uppgradera ett Blob Storage-konto till GPv2 i Portal kommer snart.

Eftersom du inte kan nedgradera från GPv2 bör du fundera över vad detta har för påverkan på kostnaderna innan du uppgraderar dina konton till GPv2.

**Kan jag lagra objekt på båda lagringsnivåerna i samma konto?**

Ja. Attributet **Åtkomstnivå** anger standardlagringsnivån som används för alla objekt i kontot som inte har en angiven nivå. Med blobnivåindelningen kan du ange åtkomstnivå på objektnivå oavsett vilken åtkomstnivå som angetts för kontot. Blobar från alla tre lagringsnivåer (frekvent, lågfrekvent eller arkiv) kan finnas samtidigt i samma konto.

**Kan jag ändra lagringsnivå för mitt GPv2-lagringskonto?**

Ja, du kan ändra lagringsnivå för kontot med attributet **Åtkomstnivå** för lagringskontot. Ändringar av lagringsnivån för kontot gäller för alla objekt som lagras i kontot och som inte har en uttryckligen inställd nivå. Om du ändrar lagringsnivån från frekvent till lågfrekvent tillkommer avgifter för skrivåtgärder (per 10 000) (gäller endast GPv2-konton). Om du ändrar från lågfrekvent till frekvent tillkommer avgifter för både läsåtgärder (per 10 000) och datahämtning (per GB) för läsning av alla data på kontot.

**Hur ofta kan jag ändra lagringsnivå för mitt Blob Storage-konto?**

Vi har ingen begränsning för hur ofta du kan ändra lagringsnivån, men observera att det kan medföra kostnader att ändra lagringsnivå från lågfrekvent till frekvent. Vi rekommenderar att du inte ändrar lagringsnivån så ofta.

**Beter sig blobarna på lågfrekvent lagringsnivå annorlunda än på frekvent lagringsnivå?**

Blobbar på frekvent lagringsnivå i GPv2- och Blob Storage-konton har samma svarstid som blobbar i GPv1-konton. Blobbar på lågfrekvent lagringsnivå har liknande svarstid (i millisekunder) som blobbar på frekvent lagringsnivå. Blobar på arkivlagringsnivån har flera timmars svarstid.

Blobbar på lågfrekvent lagringsnivå har något lägre tillgänglighetsnivå (enligt SLA) än blobbar som lagras på frekvent lagringsnivå. Mer information finns i [SLA för Storage](https://azure.microsoft.com/support/legal/sla/storage).

**Kan jag lagra sidblobbar och virtuella datordiskar i Blob Storage-konton?**

Nej. Blob Storage-konton stöder endast block- och tilläggsblobbar, inte sidblobbar. Virtuella datordiskar i Azure säkerhetskopieras av sidblobbar, vilket gör att Blob Storage-konton inte kan användas för att lagra virtuella datordiskar. Däremot kan du lagra säkerhetskopior av virtuella datordiskar som blockblobbar i ett Blob Storage-konto. Detta är en av anledningarna till att du bör överväga att använda GPv2 i stället för Blob Storage.

**Måste jag ändra mina befintliga appar/program för att använda GPv2-konton?**

GPv2-konton är 100 % API-konsekventa med GPv1- och Blob Storage-konton. Så länge ditt program använder blockblobbar eller tilläggsblobbar, och du använder 2014-02-14-versionen av [Storage Services REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) eller senare ska ditt program fungera korrekt. Om du använder en äldre version av protokollet måste du uppdatera appen till den nya versionen för att den ska fungera smidigt med båda typerna av lagringskonton. Normalt rekommenderar vi att du använder den senaste versionen oavsett vilken lagringskontotyp du använder.

Priserna för GPv2 är vanligtvis högre än GPv1 när det gäller transaktioner och bandbredd. Du kan därför behöva optimera dina transaktionsmönster innan du uppgraderar för att undvika att den totala kostnaden ökar.

**Ändras användarupplevelsen?**

GPv2-konton är mycket lika GPv1-konton, och de stöder alla viktiga funktioner i Azure Storage, med bland annat hög hållbarhet och tillgänglighet, skalbarhet, prestanda och säkerhet. Förutom funktionerna och begränsningarna som är specifika för Blob Storage-konton och lagringsnivåerna som nämnts ovan är allt annat detsamma när du uppgraderat till GPv2 eller Blob Storage.

## <a name="next-steps"></a>Nästa steg

### <a name="evaluate-blob-storage-accounts"></a>Utvärdera Blob Storage-konton

[Kontrollera tillgängligheten för Blob Storage-konton per region](https://azure.microsoft.com/regions/#services)

[Utvärdera användningen av dina aktuella lagringskonton genom att aktivera mätvärden i Azure Storage.](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Se priser för Blob Storage per region](https://azure.microsoft.com/pricing/details/storage/)

[Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-gpv2-storage-accounts"></a>Börja använda GPv2-konton

[Komma igång med Azure Blob Storage](../blobs/storage-dotnet-how-to-use-blobs.md)

[Flytta data till och från Azure Storage](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Överföra data med kommandoradsverktyget AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Bläddra igenom och utforska dina lagringskonton](http://storageexplorer.com/)
