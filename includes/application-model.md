# <a name="compute"></a>Compute
Azure kan du distribuera och övervaka din programkod som körs i ett Microsoft-datacenter. När du skapar ett program och kör det på Azure, koden och konfigurationen tillsammans kallas en Azure-värdtjänsten. Värdtjänster är lätta att hantera, skala upp eller ned, konfigurera och uppdatera med nya versioner av din programkod. Den här artikeln fokuserar på det Azure-värdtjänsten tjänstmodell för programmet.<a id="compare" name="compare"></a>

## Innehållsförteckning<a id="_GoBack" name="_GoBack"></a>
* [Fördelar med Azure-program modellen][Azure Application Model Benefits]
* [Grundläggande begrepp som värdtjänsten][Hosted Service Core Concepts]
* [Överväganden vid utformning av värdtjänsten][Hosted Service Design Considerations]
* [Skapar ditt program för att skala][Designing your Application for Scale]
* [Värdtjänsten Definition och konfiguration][Hosted Service Definition and Configuration]
* [Tjänstdefinitionsfilen][The Service Definition File]
* [Tjänstkonfigurationsfilen][The Service Configuration File]
* [Skapa och distribuera en värdbaserad tjänst][Creating and Deploying a Hosted Service]
* [Referenser][References]

## <a id="benefits"></a>Modellen fördelar med azure-program
När du distribuerar ditt program som en värdbaserad tjänst Azure skapar en eller flera virtuella datorer (VM som innehåller din programkod) och startar om de virtuella datorerna på fysiska datorer som finns i en av de Azure-datacenter. När klienten begär att tillämpningsprogrammet värdbaserade anger datacentret, distribuerar en belastningsutjämnare dessa begäranden jämnt till de virtuella datorerna. Medan Azure är värd för programmet, hämtar tre viktiga fördelar:

* **Hög tillgänglighet.** Hög tillgänglighet innebär Azure säkerställer att programmet körs så mycket som möjligt och kan svara på klientbegäranden. Om tillämpningsprogrammet avslutar (på grund av ett ohanterat undantag, till exempel), kommer Azure att identifiera detta och sedan automatiskt startar om programmet. Om datorn tillämpningsprogrammet körs på upplevelser någon typ av maskinvarufel, kommer sedan Azure också identifiera detta och automatiskt skapa en ny virtuell dator på en annan fysisk dator och köra din kod därifrån. Obs: För ditt program att hämta Microsofts serviceavtal på 99,95% tillgänglighet, du måste ha minst två virtuella datorer som kör programkoden. Detta gör att bearbeta klientbegäranden medan Azure flyttar din kod från en misslyckad virtuell dator till en ny, bra virtuell dator på en virtuell dator.
* **Skalbarhet.** Azure kan du enkelt och ändra dynamiskt antalet virtuella datorer som kör din programkod för att hantera den faktiska belastningen på ditt program. På så sätt kan du ändra ditt program till den arbetsbelastning som kunderna monterar när betalar bara för de virtuella datorerna som du behöver när du behöver dem på den. När du vill ändra antalet VMs svarar Azure i minuter som gör det möjligt att dynamiskt ändra antalet virtuella datorer som körs som ofta som önskas.
* **Hanterbarhet.** Eftersom Azure är en plattform som en tjänst (PaaS) erbjuder, hanterar infrastrukturen (maskinvaran, elektricitet och nätverk) som krävs för att hålla dessa datorer som körs. Azure hanterar också plattform, säkerställer en uppdaterad operativsystem med alla de korrekta korrigeringsfiler och säkerhetsuppdateringar, samt Komponentuppdateringar, till exempel .NET Framework och Internet Information Server. Eftersom de virtuella datorerna kör Windows Server 2008, tillhandahåller Azure ytterligare funktioner, till exempel diagnostiska övervakning, remote desktop support, brandväggar och konfiguration för lagring. Alla dessa funktioner finns utan extra kostnad. Faktum är när du kör programmet i Azure ingår licens för Windows Server 2008-operativsystem (OS). Eftersom alla de virtuella datorerna som kör Windows Server 2008 fungerar all kod som körs på Windows Server 2008 bara när den körs i Azure.

## <a id="concepts"></a>Värdbaserade tjänsten grundbegrepp
När programmet distribueras som en värdbaserad tjänst i Azure, körs som en eller flera *roller.* En *rollen* refererar bara till programfiler och konfiguration. Du kan definiera en eller flera roller för programmet, var och en med sin egen uppsättning programfiler och konfiguration. För varje roll i ditt program, kan du ange hur många virtuella datorer, eller *rollinstanser*, för att köra. Bilden nedan visar två enkla exempel på ett program som modellerats som en värdbaserad tjänst med hjälp av roller och rollinstanser.

##### <a name="figure-1-a-single-role-with-three-instances-vms-running-in-an-azure-data-center"></a>Bild 1: En roll med tre instanser (VM) som körs i ett Azure-Datacenter
![Bild][0]

##### <a name="figure-2-two-roles-each-with-two-instances-vms-running-in-an-azure-data-center"></a>Bild 2: Två roller, var och en med två instanser (VM) körs i ett Azure-Datacenter
![Bild][1]

Rollinstanser bearbeta normalt Internet-klientbegäranden anger datacentret genom något som kallas en *indataslutpunkten*. En roll kan ha 0 eller fler inkommande slutpunkter. Varje slutpunkt anger ett protokoll (HTTP, HTTPS eller TCP) och en port. Det är vanligt att konfigurera en roll om du vill ha två inkommande slutpunkter: HTTP lyssnar på port 80 och HTTPS som lyssnar på port 443. Bilden nedan visar ett exempel på två olika roller med olika inkommande slutpunkter som dirigerar klientbegäranden till dem.

![Bild][2]

När du skapar en värdbaserad tjänst i Azure kan tilldelas en offentligt adresserbara IP-adress som klienter kan använda för att komma åt den. Du måste välja ett URL-prefix som är mappad till att IP-adress på att skapa den värdbaserade tjänsten. Det här prefixet måste vara unikt eftersom du huvudsakligen reserverar den *prefix*. cloudapp.net URL så att ingen annan kan ha den. Klienter kan kommunicera med dina rollinstanser med hjälp av URL: en. Du kommer normalt inte distribuera eller publicera Azure *prefix*. cloudapp.net URL. I stället kan du köpa ett DNS-namn från DNS-registratorns väljer och konfigurerar DNS-namnet för att omdirigera klientförfrågningar till Azure-Webbadressen. Mer information finns i [konfigurera ett anpassat domännamn i Azure][Configuring a Custom Domain Name in Azure].

## <a id="considerations"></a>Värdbaserade tjänsten designöverväganden
När du skapar ett program att köras i en molnmiljö finns det flera saker att tänka på, till exempel svarstid, hög tillgänglighet och skalbarhet.

Bestämmer var du vill leta upp din programkod är viktigt när du kör en värdbaserad tjänst i Azure. Det är vanligt att distribuera programmet till datacenter som är närmast dina klienter för att minska svarstiden och få bästa möjliga prestanda möjligt.
Du kan välja ett datacenter närmare för ditt företag eller närmast dina data om du har några jurisdictional eller juridiska frågor om dina data och var den finns. Det finns sex Datacenter över hela världen kan vara värd för din programkod. Tabellen nedan visar tillgängliga platser:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<tbody>

<tr>

<td style="width: 100px;">
**Land/Region**

</td>

<td style="width: 200px;">
**Underordnade regioner**

</td>
</tr>

<tr>

<td>
USA

</td>

<td>
Central Syd & Central Nord

</td>
</tr>

<tr>

<td>
Europa

</td>

<td>
Nord & Väst

</td>
</tr>

<tr>

<td>
Asien

</td>

<td>
Sydost & Öst

</td>
</tr>
</tbody>
</table>
När du skapar en värdbaserad tjänst kan välja du en underregion som anger den plats där du vill att koden för att köra.

Det är ytterst viktigt att dina programdata behållas i en central databas som är tillgänglig för flera rollinstanser för att uppnå hög tillgänglighet och skalbarhet. Azure erbjuder flera alternativ för lagring som blobbar, tabeller och SQL-databas att med den här. Finns det [erbjudanden för lagring av Data i Azure] [ Data Storage Offerings in Azure] artikel för mer information om dessa lagringstekniker. Bilden nedan visar hur belastningsutjämnare i Azure-datacentret distribuerar klientbegäranden till en annan rollinstanser som har åtkomst till samma datalagring.

![Bild][3]

Vanligtvis du vill leta upp din programkod och dina data i samma datacenter som det här tillåter en låg latens (bättre prestanda) när din programkod har åtkomst till data. Dessutom kan debiteras du inte för bandbredden när data flyttas inom samma datacenter.

## <a id="scale"></a>Utformning av ditt program för att skala
Ibland kanske du vill ta ett program (till exempel en enkel webbplats) och det finns i Azure. Men ofta kan programmet kan bestå av flera roller som tillsammans. Till exempel i bilden nedan finns två instanser av rollen webbplats, tre instanser av rollen Orderbearbetning och en instans av rollen Rapportgenerator. Dessa roller alla arbetar tillsammans och koden för alla kan samlats i ett paket och distribueras som en enhet till Azure.

![Bild][4]

Det främsta skälet till att dela ett program i olika roller varje körs på en egen uppsättning rollinstanser (d.v.s. virtuella datorer) är att skala rollerna oberoende av varandra. Till exempel under semestern, kan många kunder köpa produkter från företaget, så kanske du vill öka antalet rollinstanser kör din webbplats roll, samt antalet rollinstanser kör din Orderbearbetning roll. Efter säsongen helgdagar får du mycket produkter returnerade, så du kan behöva mycket webbplats instanser men färre Orderbearbetning instanser. Under resten av året, kanske du bara behöver några webbplats och Orderbearbetning instanser. I hela allt detta, kanske du måste bara en Rapportgenerator-instans. Flexibilitet för rollbaserad distributioner i Azure kan du enkelt anpassa ditt program till dina affärsbehov.

Det är vanligt att ha rollen instanser inom din värdbaserade tjänst kommunicera med varandra. Till exempel webbplatsrollen accepteras kundens men sedan den avlastar ordningen bearbetning rollinstanser Orderbearbetning. Det bästa sättet att skicka arbete formuläret som en uppsättning rollinstanser till en annan uppsättning instanser med hjälp av MSMQ-teknik som tillhandahålls av Azure, antingen kötjänsten eller Service Bus-köer. Användning av en kö är en viktig del av den här artikeln. Kön kan den värdbaserade tjänsten att skala dess roller separat så att du kan balansera arbetsbelastningen mot kostnaden. Om antalet meddelanden i kön ökar med tiden, kan du skala upp antalet Orderbearbetning rollinstanser. Om antalet meddelanden i kön minskar med tiden, kan du skala ned antalet Orderbearbetning rollinstanser. På så sätt kan du bara betalar för de instanser som krävs för att hantera den faktiska arbetsbelastningen.

Kön innehåller också tillförlitlighet. Vid skalning ned antalet Orderbearbetning rollinstanser beslutar Azure vilka instanser som ska avslutas. Det kan besluta att avsluta en instans som håller på att bearbetning av ett meddelande i kön. Eftersom meddelandebehandling inte slutförs korrekt blir dock meddelandet visas igen för en annan Orderbearbetning rollinstans som tar upp och bearbetar dessa. På grund av kön meddelandet synlighet garanterat meddelanden slutligen bearbetas. Kön fungerar också som en belastningsutjämnare genom att effektivt distribuera meddelanden till alla rollinstanser som begär meddelanden från den.

För rollinstanser webbplats som du kan övervaka trafik som kommer till dem och bestämmer dig för att bygga ut antalet uppåt eller nedåt samt. Kön kan du skala antalet rollinstanser för webbplatsen oberoende av rollinstanser Orderbearbetning. Detta är mycket kraftfulla och ger dig stor flexibilitet. Om ditt program består av ytterligare roller, kan du självklart lägga till ytterligare köer som ledning för kommunikation mellan dem för att kunna dra nytta av samma skalning och kostnaden fördelar.

## <a id="defandcfg"></a>Finns tjänstdefinitionen och konfiguration
Distribuera en värdbaserad tjänst i Azure måste du också ha en tjänstdefinitionsfilen och en konfigurationsfil för tjänsten. Båda dessa filer är XML-filer och du kan ange distributionsalternativ för din värdbaserade tjänst deklarativt. Tjänstdefinitionsfilen beskriver alla de roller som utgör din värdbaserade tjänst och hur de kommunicerar med varandra. Tjänstkonfigurationsfilen anger antal instanser för varje roll och inställningar som används för att konfigurera varje rollinstans.

## <a id="def"></a>Tjänstdefinitionsfilen
I tidigare nämnda tjänstdefinitionen (CSDEF) i filen är en XML-fil som beskriver de olika roller som utgör ditt fullständiga program. Fullständig schemat för XML-filen finns här: [http://msdn.microsoft.com/library/windowsazure/ee758711.aspx] [].
CSDEF-filen innehåller en WebRole eller WorkerRole element för varje roll som du vill använda i ditt program. Distribuera en rollen webbserver (med elementet WebRole) innebär att koden ska köras på en rollinstans som innehåller Windows Server 2008 och Internet Information Server (IIS).
Distribuera en roll som en arbetsroll (med elementet WorkerRole) innebär att instansen har Windows Server 2008 på den (IIS installeras inte).

Du kan skapa och distribuera en arbetsroll som använder någon annan mekanism för att lyssna efter inkommande webbegäranden absolut (till exempel din kod kan skapa och använda en .NET-HttpListener). Eftersom rollinstanserna körs Windows Server 2008, kan din kod utföra alla åtgärder som normalt är tillgängliga i ett program som körs på Windows Server
2008.

För varje roll anger du önskade VM-storlek som ska använda för instanser av rollen. Tabellen nedan visar de olika storlekarna på VM finns idag och attribut för varje:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<tbody>

<tr align="left" valign="top">

<td>
**VM-storlek**

</td>

<td>
**CPU**

</td>

<td>
**RAM-MINNE**

</td>

<td>
**Disk**

</td>

<td>
**Belastning från nätverket**

</td>
</tr>

<tr align="left" valign="top">

<td>
**Extra liten**

</td>

<td>
1 x 1,0 GHz

</td>

<td>
768 MB

</td>

<td>
20GB

</td>

<td>
\~5 Mbit/s

</td>
</tr>

<tr align="left" valign="top">

<td>
**Liten**

</td>

<td>
1 x 1,6 GHz

</td>

<td>
1,75 GB

</td>

<td>
225GB

</td>

<td>
\~100 Mbit/s

</td>
</tr>

<tr align="left" valign="top">

<td>
**Medel**

</td>

<td>
2 x 1,6 GHz

</td>

<td>
3,5 GB

</td>

<td>
490GB

</td>

<td>
\~200 Mbit/s

</td>
</tr>

<tr align="left" valign="top">

<td>
**Stora**

</td>

<td>
4 x 1,6 GHz

</td>

<td>
7 GB

</td>

<td>
1TB

</td>

<td>
\~400 Mbit/s

</td>
</tr>

<tr align="left" valign="top">

<td>
**Extra stor**

</td>

<td>
8 x 1,6 GHz

</td>

<td>
14 GB

</td>

<td>
2TB

</td>

<td>
\~800 Mbit/s

</td>
</tr>
</tbody>
</table>
Du debiteras timvis för varje virtuell dator du använder som en rollinstans och du debiteras också för alla data som dina rollinstanser skickar utanför datacentret. Du debiteras inte för data som anger datacentret. Mer information finns i [priser för Azure] [priser för Azure]. I allmänhet rekommenderas det att använda många små rollinstanser inte några stora förekomster så att programmet är mer flexibla fel. När alla färre rollinstanserna du, mer katastrofal ett fel i en av dem är att tillämpningsprogrammet övergripande. Som tidigare nämnts, måste du också distribuera minst två instanser för varje roll för att få 99,95% servicenivåavtalet Microsoft tillhandahåller.

Tjänstdefinitionsfilen (CSDEF) är också där du vill ange många attribut om varje roll i ditt program. Här följer några mer användbar objekt tillgängliga:

* **Certifikat**. Du använder certifikat för att kryptera data eller om webbtjänsten stöder SSL. Alla certifikat måste laddas upp till Azure. Mer information finns i [hantera certifikat i Azure][Managing Certificates in Azure]. Den här inställningen för XML-installerar överfört tidigare certifikat i certifikatarkivet för den rollinstans så att de kan användas av programkoden.
* **Inställningen konfigurationsnamn**. För värden som du vill att dina program att läsa när du kör på en rollinstans. Det faktiska värdet för konfigurationsinställningarna anges i (CSCFG) tjänstkonfigurationsfilen som kan uppdateras när som helst utan att behöva distribuera din kod. Du kan i själva verket koda dina program på ett sådant sätt att identifiera ändrade konfigurationsvärdena utan medför några driftavbrott.
* **Ange slutpunkter**. Här anger du eventuella HTTP, HTTPS eller TCP-slutpunkter (med portar) som du vill exponera för allmänheten via din *prefix*. cloadapp.net URL. När Azure distribuerar din roll, konfigurerar den brandväggen på rollinstans automatiskt.
* **Interna slutpunkter**. Här kan du ange alla HTTP- eller TCP-slutpunkter som du vill exponeras för andra rollinstanser som distribueras som en del av ditt program. Interna slutpunkter som tillåter alla rollinstanser i ditt program för att kommunicera med varandra men är inte tillgängliga för alla rollinstanser som ligger utanför tillämpningsprogrammet.
* **Importera moduler**. Dessa installera om du vill användbar komponenter på dina rollinstanser. Det finns komponenter för diagnostiska övervakning, fjärrskrivbord och Azure Connect (som gör att din instans av serverroll åtkomst till lokala resurser via en säker kanal).
* **Lokal lagring**. Detta allokerar en underkatalog på instansen för programmet ska använda. Det beskrivs i detalj i den [erbjudanden för lagring av Data i Azure] [ Data Storage Offerings in Azure] artikel.
* **Start uppgifter**. Start uppgifter ger dig ett sätt att installera nödvändiga komponenter på en rollinstans när den startar. Uppgifterna kan köras utökade som administratör, om det behövs.

## <a id="cfg"></a>Tjänstkonfigurationsfilen
Tjänstkonfigurationsfilen (CSCFG) är en XML-fil som beskriver inställningar som kan ändras utan att omdistribuera ditt program. Fullständig schemat för XML-filen finns här: [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][http://msdn.microsoft.com/library/windowsazure/ee758710.aspx].
CSCFG-filen innehåller en roll-element för varje roll i ditt program. Här följer några av de objekt som du kan ange i CSCFG-filen:

* **OS-Version**. Det här attributet kan du välja operativsystem (OS) version som du vill används för alla rollinstanser som kör programkoden. Det här Operativsystemet kallas den *gästen OS*, varje ny version med de senaste säkerhetskorrigeringar och uppdateringar som är tillgängliga vid tidpunkten gästoperativsystemet släpps. Om du anger värdet för attributet osVersion till ”\*”, och sedan Azure uppdateras automatiskt gästoperativsystemet på var och en av dina rollinstanser som nya gäst-OS-versioner. Dock kan du välja utanför automatiska uppdateringar genom att välja en specifik gäst-OS-version. Ange till exempel osVersion-attributet till ett värde för ”WA-GUEST-OS-2.8\_201109-01” gör att alla rollinstanser få vad som anges på den här webbsidan: [http://msdn.microsoft.com/library/hh560567.aspx] [http://msdn.microsoft.com/library/hh560567.aspx]. Mer information om gäst-OS-versioner finns [hantera uppgraderingar till Azure gäster OS].
* **Instanser**. Värdet för det här elementet anger antalet rollinstanser som du vill använda etablerade Kör koden för en viss roll. Eftersom du kan ladda upp en ny CSCFG-fil till Azure (utan att omdistribuera ditt program), är det trivially enkelt att ändra värdet för det här elementet och ladda upp en ny CSCFG-fil för att öka eller minska antalet rollinstanser kör programkoden dynamiskt . På så sätt kan du enkelt skala ditt program uppåt eller nedåt uppfyller faktiska arbetsbelastningen behoven också styra hur mycket du debiteras för rollinstanserna.
* **Värden för konfigurationsinställningen**. Det här elementet anger värden för inställningar (som definieras i filen CSDEF). Din roll kan läsa dessa värden när den körs. Dessa konfigurationsinställningar används vanligtvis för anslutningssträngar till SQL-databas eller till Azure Storage, men de kan användas för något ändamål som du önskar.

## <a id="hostedservices"></a>Att skapa och distribuera en värdbaserad tjänst
Skapa en värdbaserad tjänst kräver att du först gå till den [Azure-hanteringsportalen] och etablera en värdbaserad tjänst genom att ange ett DNS-prefix och data center du slutligen vill koden körs i. I din utvecklingsmiljö skapa din tjänstdefinitionsfilen (CSDEF), skapa din programkod och paket (zip) med alla dessa filer till en servicepaketfil (CSPKG). Dessutom måste du förbereda din tjänstekonfigurationsfil (CSCFG). För att distribuera din roll måste överföra du CSPKG- och CSCFG-filer med Azure Service Management API. När har distribuerats, Azure, kommer etablera rollinstanser i datacenter (baserat på konfigurationsdata), extrahera programkoden från paketet, kopierar den till rollinstanserna och starta instanserna. Koden är nu aktiv och körs.

Bilden nedan visar CSPKG- och CSCFG-filer som du skapar på utvecklingsdatorn. CSPKG-filen innehåller filen CSDEF och koden för två roller. Azure skapar rollinstanserna i datacentret efter överföring CSPKG- och CSCFG-filer med Azure Service Management API. I det här exemplet anges CSCFG-fil att Azure ska skapa tre instanser av rollen \#1 och två instanser av rollen \#2.

![Bild][5]

Mer information om hur du distribuerar, uppgradera och konfigurera om dina roller finns på [distribuerar och uppdaterar Azure program] [ Deploying and Updating Azure Applications] artikel.<a id="Ref" name="Ref"></a>

## <a id="references"></a>Referenser
* [Skapa en värdbaserad tjänst för Azure][Creating a Hosted Service for Azure]
* [Hantera värdbaserade tjänster i Azure][Managing Hosted Services in Azure]
* [Migrering av program till Azure][Migrating Applications to Azure]
* [Konfigurera ett Azure-program][Configuring an Azure Application]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Skrivits av Jeffrey Richter (Wintellect)</p>

</div>

[Azure Application Model Benefits]: #benefits
[Hosted Service Core Concepts]: #concepts
[Hosted Service Design Considerations]: #considerations
[Designing your Application for Scale]: #scale
[Hosted Service Definition and Configuration]: #defandcfg
[The Service Definition File]: #def
[The Service Configuration File]: #cfg
[Creating and Deploying a Hosted Service]: #hostedservices
[References]: #references
[0]: ./media/application-model/application-model-3.jpg
[1]: ./media/application-model/application-model-4.jpg
[2]: ./media/application-model/application-model-5.jpg
[Configuring a Custom Domain Name in Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
[Data Storage Offerings in Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
[3]: ./media/application-model/application-model-6.jpg
[4]: ./media/application-model/application-model-7.jpg

[Azure Pricing]: http://www.windowsazure.com/pricing/calculator/
[Managing Certificates in Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
[http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
[hantera uppgraderingar till Azure gäster OS]: http://msdn.microsoft.com/library/ee924680.aspx
[Azure-hanteringsportalen]: http://manage.windowsazure.com/
[5]: ./media/application-model/application-model-8.jpg
[Deploying and Updating Azure Applications]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
[Creating a Hosted Service for Azure]: http://msdn.microsoft.com/library/gg432967.aspx
[Managing Hosted Services in Azure]: http://msdn.microsoft.com/library/gg433038.aspx
[Migrating Applications to Azure]: http://msdn.microsoft.com/library/gg186051.aspx
[Configuring an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
