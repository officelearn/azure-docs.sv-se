<properties
    pageTitle="Azure Cool Storage for Blobs | Microsoft Azure"
    description="Lagringsnivåer för bloblagring erbjuder en kostnadseffektiv lagring av objektdata baserat på åtkomstmönster. Azures lågfrekventa lagring är optimerad för data som inte används så ofta."
    services="storage"
    documentationCenter=""
    authors="sribhat-msft"
    manager=""
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="sribhat"/>


# Azure Blob Storage: Frekvent och lågfrekvent lagringsnivå

## Översikt

Azure Storage erbjuder två lagringsnivåer för bloblagring (objektlagring), så att du kan lagra data så kostnadseffektivt som möjligt, beroende på din användning. Azures **frekventa lagringsnivå** är optimerad för att lagra data som används ofta. Azures **lågfrekventa lagringsnivå** är optimerad för att lagra data som inte används ofta och som är långlivade. Data i den lågfrekventa lagringsnivån klarar en lite lägre tillgänglighet, men kräver fortfarande hög hållbarhet och liknande åtkomsttid och dataflödesegenskaper som data i frekvent lagringsnivå. För data i den lågfrekventa lagringsnivån är serviceavtal med lägre tillgänglighet och högre åtkomstkostnader godtagbara med tanke på de mycket lägre lagringskostnaderna.

Idag växer mängden data som lagras i molnet i exponentiell takt. För att hålla kontroll på kostnaderna för dina växande lagringsbehov är det en god idé att ordna data baserat på attribut som åtkomstfrekvens och planerad kvarhållningsperiod. Data som lagras i molnet kan vara ganska olika beroende på hur de genereras, bearbetas och används under livslängden. Vissa data används aktivt och ändras under livslängden. Vissa data används ofta i början av livslängden och sedan minskar användning drastiskt när dessa data blir äldre. Vissa data förblir inaktiva i molnet och används sällan, eller kanske aldrig, när de har lagrats.

För varje scenario som beskrivs ovan finns en lagringsnivå som är optimerad för motsvarande åtkomstmönster. I Azure Blob Storage finns en frekvent och lågfrekvent åtkomstnivå för att uppfylla behovet av lagringsnivåer med olika prissättningsmodeller.

## Blob Storage-konton

**Blob Storage-konton** är särskilda lagringskonton för att lagra ostrukturerade data som blobar (objekt) i Azure Storage. Med Blob Storage-konton kan du nu välja mellan frekvent och lågfrekvent åtkomstnivå. Du kan lagra data med lågfrekvent åtkomstnivå till en lägre lagringskostnad och lagra data med frekvent åtkomstnivå till en lägre åtkomstkostnad. Blob Storage-konton liknar de allmänna lagringskonton du redan har. De har samma funktioner för hållbarhet, tillgänglighet, skalbarhet och prestanda, och dessutom har de 100 procent konsekvent API för blockblobar och tilläggsblobar.

> [AZURE.NOTE] Blob Storage-konton stöder endast block- och tilläggsblobar, inte sidblobar.

Blob Storage-konton exponerar attributet **Åtkomstnivå** som gör att du kan välja **Frekvent** eller **Lågfrekvent** lagringsnivå beroende på vilken typ av data som lagras på kontot. Du kan när som helst växla mellan de olika nivåerna om användningsmönstret förändras.

> [AZURE.NOTE] Observera att ytterligare kostnader kan tillkomma om du ändrar åtkomstnivå. Mer information finns i avsnittet [Priser och fakturering](storage-blob-storage-tiers.md#pricing-and-billing).

Exempelscenarier för lagringsnivån för frekvent åtkomst:

- Data används aktivt eller förväntas användas ofta (både för att läsa och skriva).
- Data som mellanlagras för bearbetning och eventuell migrering till lågfrekvent åtkomstnivå.

Exempelscenarier för lågfrekvent åtkomstnivå:

- Datauppsättningar för säkerhetskopiering, arkivering och haveriberedskap.
- Äldre medieinnehåll som inte visas så ofta längre, men som förväntas vara tillgängligt direkt vid behov.
- Stora datamängder som behöver en kostnadseffektiv lagring under tiden mer data samlas in för framtida bearbetning. (*T.ex.*, långsiktig lagring av vetenskapliga data eller telemetridata (rådata) från en tillverkningsanläggning.)
- Ursprungliga rådata som måste bevaras, även efter att de har bearbetats till ett slutligt användbart format. (*T.ex.* mediefiler i RAW-format som har omkodats till andra format.)
- Efterlevnads- och arkiveringsdata som behöver lagras under en längre tid och som nästan aldrig används. (*T.ex.* film från säkerhetskameror, gamla röntgenbilder/magnetröntgenbilder för vårdorganisationer, ljudinspelningar och transkript av kundsamtal för ekonomiska tjänster.)

Mer information om lagringskonton finns i [Om Azure-lagringskonton](storage-create-storage-account.md).

För program som bara behöver block- eller tilläggsbloblagring rekommenderar vi att du använder Blob Storage-konton så att du kan utnyttja de olika prissättningsmodellerna för olika lagringsnivåer. Men i vissa fall kan det vara bättre att använda allmänna lagringskonton, t.ex. om:

- Du behöver använda tabeller, köer eller filer och vill att dina blobar ska lagras i samma lagringskonto. Observera att det inte finns några tekniska fördelar med att lagra dessa på samma konto förutom att du får samma delade nycklar.
- Du ändå måste använda den klassiska distributionsmodellen. Blob Storage-konton är bara tillgängliga via Azure Resource Manager-distributionsmodellen.
- Du behöver använda sidblobar. Blob Storage-konton stöder inte sidblobar. Generellt rekommenderar vi att du använder blockblobar om du inte har ett särskilt behov av sidblobar.
- Du använder en version av [Storage Services REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) som är äldre än 2014-02-14 eller ett klientbiblioteket med en tidigare version än 4.x och det inte går att uppgradera ditt program.

> [AZURE.NOTE] Blob Storage-konton stöds för närvarande i en majoritet av Azure-regionerna, och kommer att utökas till fler regioner. Den senaste listan med tillgängliga regioner hittar du på sidan [Tjänster per region](https://azure.microsoft.com/regions/#services).

## Jämförelse mellan åtkomstnivåer

Följande tabell visar en jämförelse mellan åtkomstnivåerna:

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250">
<col width="250">
<col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Frekvent åtkomstnivå</center></strong></td>
    <td><strong><center>Lågfrekvent åtkomstnivå</center></strong></td
</tr>
<tr>
    <td><strong><center>Tillgänglighet</center></strong></td>
    <td><center>99,9 %</center></td>
    <td><center>99 %</center></td>
</tr>
<tr>
    <td><strong><center>Tillgänglighet<br>(RA-GRS-läsningar)</center></strong></td>
    <td><center>99,99 %</center></td>
    <td><center>99,9 %</center></td>
</tr>
<tr>
    <td><strong><center>Avgifter för användning</center></strong></td>
    <td><center>Högre kostnader för lagring<br>Lägre kostnader för åtkomst och transaktioner</center></td>
    <td><center>Lägre kostnader för lagring<br>Högre kostnader för åtkomst och transaktioner</center></td>
</tr>
<tr>
    <td><strong><center>Minsta objektstorlek<center></strong></td>
    <td colspan="2"><center>Saknas</center></td>
</tr>
<tr>
    <td><strong><center>Minsta lagringstid<center></strong></td>
    <td colspan="2"><center>Saknas</center></td>
</tr>
<tr>
    <td><strong><center>Svarstid<br>(Tid till första byte)<center></strong></td>
    <td colspan="2"><center>millisekunder</center></td>
</tr>
<tr>
    <td><strong><center>Mål för skalbarhet och prestanda<center></strong></td>
    <td colspan="2"><center>Samma som allmänna lagringskonton</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Blob Storage-konton har samma mål för prestanda och skalbarhet som allmänna lagringskonton. Mer information finns i [Skalbarhets- och prestandamål i Azure Storage](storage-scalability-targets.md).

## Priser och fakturering

För Blob Storage-konton används en ny prissättningsmodell för bloblagring baserad på åtkomstnivå. När du använder ett Blob Storage-konto gäller följande för debitering:

- **Lagringskostnader**: Utöver mängden data som lagras varierar lagringskostnaden beroende på åtkomstnivå. Kostnaden per gigabyte är lägre för lågfrekvent åtkomstnivå än för frekvent åtkomstnivå.
- **Kostnader för dataåtkomst**: För data i lågfrekvent åtkomstnivå debiteras du en avgift per gigabyte för läsningar och skrivningar.
- **Transaktionskostnader**: Det finns en kostnad per transaktion för båda nivåerna. Kostnaden per transaktion är dock högre för lågfrekvent åtkomstnivå än för frekvent åtkomstnivå.
- **Dataöverföringskostnader för geo-replikering**: Detta gäller endast konton med konfigurerad geo-replikering, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.
- **Kostnader för utgående dataöverföring**: Utgående dataöverföringar (data som överförs utanför en Azure-region) debiteras för bandbreddsanvändning per gigabyte, på samma sätt som för allmänna lagringskonton.
- **Ändring av åtkomstnivå**: Om du byter åtkomstnivå från lågfrekvent till frekvent påförs en avgift som motsvarar läsning av alla data på lagringskontot för varje övergång. Å andra sidan kostar det inget att byta från frekvent till lågfrekvent åtkomstnivå.

> [AZURE.NOTE] För att användarna ska få prova de nya lagringsnivåerna och utvärdera funktionerna debiteras inget för övergång från lågfrekvent till frekvent åtkomstnivå t.o.m. 30 juni 2016. Från 1 juli 2016 börjar kostnaden gälla för alla övergångar från lågfrekvent till frekvent åtkomstnivå. Mer information om priserna för Blob Storage-konton finns på sidan [Pris för Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Mer information om kostnaderna för utgående dataöverföring finns på sidan [Prisinformation om Dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

## Snabbstart

I det här avsnittet visar vi hur du utför följande åtgärder i Azure Portal:

- Skapar ett Blob Storage-konto.
- Hanterar ett Blob Storage-konto.

### Använda Azure Portal

#### Skapa ett Blob Storage-konto i Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. På navmenyn väljer du **Nytt** -> **Data + Storage** -> **Storage-konto**.

3. Ange ett namn för lagringskontot.

4. Välj **Resource Manager** som distributionsmodell.

5. Välj **Blob Storage** som typ av lagringskonto.

6. Välj åtkomstnivå: **Frekvent** eller **Lågfrekvent**. Standardinställningen är **Frekvent**.

7. Välj replikeringsalternativ för lagringskontot: **LRS**, **GRS** eller **RA-GRS**. Standardinställningen är **RA-GRS**. Mer information om replikeringsalternativen för Azure Storage finns i [Azure Storage-replikering](storage-redundancy.md).

8. Välj den prenumeration som du vill skapa det nya lagringskontot i.

9. Ange en ny resursgrupp eller välj en befintlig resursgrupp. Mer information om resursgrupper finns i [Hantera Azure-resurser med hjälp av Azure Portal](../azure-portal/resource-group-portal.md).

10. Välj den geografiska platsen för ditt lagringskonto.

11. Skapa lagringskontot genom att klicka på **Skapa**.

#### Ändra åtkomstnivå för ett Blob Storage-konto med Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till ditt lagringskonto.

2. Klicka på **Alla inställningar** och sedan på **Konfiguration** för att visa och/eller ändra kontokonfigurationen.

3. Ange önskad åtkomstnivå: **Frekvent** eller **Lågfrekvent**.

    > [AZURE.NOTE] Observera att ytterligare kostnader kan tillkomma om du ändrar åtkomstnivå. Mer information finns i avsnittet [Priser och fakturering](storage-blob-storage-tiers.md#pricing-and-billing).

## Migrera till Blob Storage-konton

Syftet med det här avsnittet är att hjälpa användarna att få en smidig övergång till Blob Storage-konton. Ett Blob Storage-konto är specialanpassat för lagring av endast block- och tilläggsblobar. Allmänna lagringskonton, där du kan lagra tabeller, köer, filer och diskar, och även blobar, kan inte omvandlas till Blob Storage-konton. Om du vill använda lagringsnivåer måste du skapa Blob Storage-konton och migrera dina befintliga data till de nya kontona.

### Planera migrering av befintliga data

Om du flyttar dina data till ett Blob Storage-konto vill du förmodligen dra nytta av fördelarna med lågfrekvent åtkomstnivå för att minska kostnaderna för data som inte används så ofta. Det första steget när du planerar att flytta data till ett Blob Storage-konto med lågfrekvent åtkomstnivå är att utvärdera det befintliga användningsmönstret och ta reda på om du kommer att tjäna på att migrera till Blob Storage-kontot. Vanligtvis vill du veta:

- Ditt användningsmönster – hur mycket data lagras och hur ändras lagringen månadsvis?
- Ditt åtkomstmönster – hur mycket data läses in och skrivs till kontot (inklusive nya data)? Hur många och vilka transaktioner används för dataåtkomst?

Information om hur du övervakar dina befintliga lagringskonton och samlar in dessa data finns i [Enabling Azure Storage metrics and viewing metrics data](storage-enable-and-view-metrics.md). Med hjälp av dessa data kan du använda [Priskalkylatorn för Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) för att beräkna dina kostnader.

### Migrera befintliga data

Med följande metoder kan du migrera befintliga data till Blob Storage-konton från lokala lagringsenheter, från externa molnlagringsleverantörer eller från befintliga allmänna lagringskonton i Azure:

#### AzCopy

AzCopy är ett Windows-kommandoradsverktyg för högpresterande kopiering av data till och från Azure Storage. Med AzCopy kan du kopiera data till ditt Blob Storage-konto från dina befintliga allmänna lagringskonton eller överföra data från dina lokala lagringsenheter till ditt Blob Storage-konto.

Mer information finns i [Transfer data with the AzCopy Command-Line Utility](storage-use-azcopy.md).

#### Bibliotek för flytt av data

Azure Storage-biblioteket för flytt av data för .NET är baserat på det ramverk för flytt av data som används för AzCopy. Biblioteket är utformat för högpresterande, tillförlitliga och enkla åtgärder för dataöverföring och liknar de som används i AzCopy. Du kan utnyttja fördelarna med funktionerna som tillhandahålls av AzCopy direkt i din app utan att du behöver köra och övervaka externa instanser av AzCopy.

Mer information finns i [Azure Storage Data Movement Library for .Net](https://github.com/Azure/azure-storage-net-data-movement)

#### REST-API eller klientbibliotek

Du kan skapa en anpassad app för att migrera dina data till ett Blob Storage-konto med Azures klientbibliotek eller REST-API:t för Azure-lagringstjänster. Azure Storage innehåller omfattande klientbibliotek för flera språk och plattformar som .NET, Java, C++, Node.JS, PHP, Ruby och Python. Klientbiblioteken har avancerade funktioner, t.ex. logik för omprövning, loggning och parallell överföring. Du kan också utveckla direkt mot REST-API:t, som kan anropas med valfritt språk som kan skicka HTTP/HTTPS-begäranden.

Mer information finns i [Get Started with Azure Blob storage](storage-dotnet-how-to-use-blobs.md) (Kom igång med Azure Blob Storage).

> [AZURE.NOTE] Blobar som krypteras med kryptering på klientsidan lagrar krypteringsrelaterade metadata tillsammans med bloben. Det är absolut nödvändigt att kopieringsmekanismen ser till att blobmetadata, och särskilt krypteringsrelaterade metadata, bevaras. Om du kopierar blobar utan metadata är blobinnehållet inte längre hämtningsbart. Mer information om krypteringsrelaterade metadata finns i [Azure Storage client side encryption](storage-client-side-encryption.md).

## Vanliga frågor och svar

1. **Är de befintliga lagringskontona fortfarande tillgängliga?**

    Ja, de befintliga lagringskontona är fortfarande tillgängliga. Varken priserna eller funktionerna har ändrats.  Du kan inte välja åtkomstnivå med dessa konton och du kommer inte heller att kunna göra det i framtiden.

2. **När och varför ska jag börja använda Blob Storage-konton?**

    Blob Storage-konton är särskilt utformade för att lagra blobar och ger oss möjlighet att lägga till nya blobanpassade funktioner. Vi rekommenderar att du använder Blob Storage-konton i fortsättningen för att lagra blobar eftersom framtida funktioner, t.ex. hierarkisk lagring och lagringsnivåer, kommer att läggas till för den här kontotypen. Det är naturligtvis upp till dig när du vill migrera beroende på dina affärsbehov.

3. **Kan jag omvandla mitt befintliga lagringskonto till ett Blob Storage-konto?**

    Nej. Blob Storage-kontot är en annan typ av lagringskonto. Du måste skapa ett nytt konto och sedan migrera dina data enligt beskrivningen ovan.

4. **Kan jag lagra objekt i båda åtkomstnivåerna i samma konto?**

    Attributet för åtkomstnivå är inställt på en kontonivå och gäller för alla objekt på det kontot. Du kan inte ange åtkomstnivå på objektnivå.

5. **Kan jag ändra åtkomstnivå för mitt Blob Storage-konto?**

    Ja. Du kan ändra åtkomstnivå för lagringskontot. Om du ändrar åtkomstnivå för ett konto gäller den nivån för alla objekt som lagras på kontot. Om du ändrar åtkomstnivå från frekvent till lågfrekvent åtkomstnivå behöver du inte betala något, men om du byter från lågfrekvent till frekvent åtkomstnivå debiteras du per GB för läsning av alla data på kontot.

6. **Hur ofta kan jag ändra åtkomstnivå för mitt Blob Storage-konto?**

    Vi har ingen begränsning för hur ofta du kan ändra åtkomstnivån, men observera att det medför kostnader att ändra åtkomstnivå från lågfrekvent till frekvent. Vi rekommenderar att du inte ändrar åtkomstnivå ofta.

7. **Beter sig blobar i lågfrekvent åtkomstnivå annorlunda än de som lagras i åtkomstnivån för frekvent åtkomst?**

    Blobar i frekvent åtkomstnivå har samma svarstid som blobar i allmänna lagringskonton. Blobar i lågfrekvent åtkomstnivå har liknande svarstid (i millisekunder) som blobar i allmänna lagringskonton.

    Blobar i lågfrekvent åtkomstnivå har något lägre tillgänglighetsnivå (enligt SLA) än blobar som lagras i frekvent åtkomstnivå. Mer information finns i [SLA för Storage](https://azure.microsoft.com/support/legal/sla/storage).

8. **Kan jag lagra sidblobar och virtuella datordiskar i Blob Storage-konton?**

    Blob Storage-konton stöder endast block- och tilläggsblobar, inte sidblobar. Virtuella datordiskar i Azure  backas upp av sidblobar, vilket gör att Blob Storage-konton inte kan användas för att lagra virtuella datordiskar. Däremot kan du lagra säkerhetskopior av virtuella datordiskar som blockblobar i ett Blob Storage-konto.

9. **Måste jag ändra mina befintliga appar för att använda Blob Storage-konton?**

    Blob Storage-konton är API-konsekventa till 100 % med allmänna lagringskonton för block- och tilläggsblobar. Så länge din app använder blockblobar eller tilläggsblobar, och du använder 2014-02-14-versionen av [Storage Services REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) eller senare, ska din app fungera korrekt. Om du använder en äldre version av protokollet måste du uppdatera appen till den nya versionen för att den ska fungera smidigt med båda typerna av lagringskonton. Normalt rekommenderar vi att du använder den senaste versionen oavsett vilken lagringskontotyp du använder.

10. **Kommer användarupplevelsen att ändras?**

    Blob Storage-konton är mycket lika allmänna lagringskonton när det gäller att lagra block- och tilläggsblobar, och de stöder alla viktiga funktioner i Azure Storage, med bland annat hög hållbarhet och tillgänglighet, skalbarhet, prestanda och säkerhet. Förutom funktionerna och begränsningarna som är specifika för Blob Storage-konton och åtkomstnivåerna som nämnts ovan är allt annat detsamma.

## Nästa steg

### Utvärdera Blob Storage-konton

[Kontrollera tillgängligheten för Blob Storage-konton efter region](https://azure.microsoft.com/regions/#services)

[Utvärdera användningen av dina aktuella lagringskonton genom att aktivera  mätvärden i Azure Storage.](storage-enable-and-view-metrics.md)

[Se priser för Blob Storage per region](https://azure.microsoft.com/pricing/details/storage/)

[Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)

### Börja använda Blob Storage-konton

[Komma igång med Azure Blob Storage](storage-dotnet-how-to-use-blobs.md)

[Flytta data till och från Azure Storage](storage-moving-data.md)

[Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md)



<!--HONumber=Jun16_HO2-->


