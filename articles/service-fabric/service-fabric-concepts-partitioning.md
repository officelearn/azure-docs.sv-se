---
title: Tjänster för partitionering av tjänst fabric
description: Beskriver hur du partitionerar tillståndskänsliga tjänst för Service Fabric. Partitioner möjliggör datalagring på de lokala datorerna så att data och beräkning kan skalas tillsammans.
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: 4edfaa74fe109c688cad733d16031e87fff1e46f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115153"
---
# <a name="partition-service-fabric-reliable-services"></a>Partitionera tillförlitliga Service Fabric-tjänster
Den här artikeln innehåller en introduktion till de grundläggande begreppen för partitionering av azure service fabric-tillförlitliga tjänster. Källkoden som används i artikeln finns också på [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Partitionering
Partitionering är inte unikt för Service Fabric. I själva verket är det ett centralt mönster för att bygga skalbara tjänster. I vidare bemärkelse kan vi tänka oss att partitionera som ett begrepp om att dela upp tillstånd (data) och beräkna i mindre tillgängliga enheter för att förbättra skalbarhet och prestanda. En välkänd form av partitionering är [datapartitionering][wikipartition], även känd som sharding.

### <a name="partition-service-fabric-stateless-services"></a>Tillståndslösa tjänster för Partition Service Fabric
För tillståndslösa tjänster kan du tänka på att en partition är en logisk enhet som innehåller en eller flera instanser av en tjänst. Bild 1 visar en tillståndslös tjänst med fem instanser fördelade över ett kluster med en partition.

![Tillståndslös tjänst](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Det finns egentligen två typer av statslösa servicelösningar. Den första är en tjänst som behåller sitt tillstånd externt, till exempel i en Azure SQL-databas (som en webbplats som lagrar sessionsinformation och data). Den andra är beräknings-bara tjänster (som en kalkylator eller miniatyrbild av bilder) som inte hanterar något beständigt tillstånd.

I båda fallen är partitionering av en tillståndslös tjänst ett mycket sällsynt scenario – skalbarhet och tillgänglighet uppnås normalt genom att lägga till fler instanser. Den enda gången du vill överväga flera partitioner för tillståndslösa tjänstinstanser är när du behöver uppfylla särskilda routningsbegäranden.

Som ett exempel bör du överväga ett fall där användare med ID:er i ett visst intervall endast ska betjänas av en viss tjänstinstans. Ett annat exempel på när du kan partitionera en tillståndslös tjänst är när du har en verkligt partitionerad servergrupp (t.ex. en fragmenterad SQL-databas) och du vill styra vilken tjänstinstans som ska skriva till databasen shard - eller utföra andra förberedelsearbete inom den tillståndslösa tjänsten som kräver samma partitioneringsinformation som används i serverda. Dessa typer av scenarier kan också lösas på olika sätt och behöver inte nödvändigtvis tjänstpartitionering.

Resten av den här genomgången fokuserar på tillståndskänsliga tjänster.

### <a name="partition-service-fabric-stateful-services"></a>Tillståndskänsliga tjänster för Partition Service Fabric
Service Fabric gör det enkelt att utveckla skalbara tillståndskänsliga tjänster genom att erbjuda ett förstklassigt sätt att partitionera tillstånd (data). Begreppsmässigt kan du tänka på en partition av en tillståndskänslig tjänst som en skalningsenhet som är mycket tillförlitlig genom [repliker](service-fabric-availability-services.md) som distribueras och balanseras över noderna i ett kluster.

Partitionering i samband med Service Fabric tillståndskänsliga tjänster hänvisar till processen att fastställa att en viss tjänst partition är ansvarig för en del av hela tillståndet för tjänsten. (Som tidigare nämnts är en partition en uppsättning [repliker](service-fabric-availability-services.md)). En stor sak om Service Fabric är att det placerar partitioner på olika noder. På så sätt kan de växa till en nods resursgräns. När databehoven växer växer partitioner och Service Fabric balanserar partitioner över noder. Detta säkerställer fortsatt effektiv användning av maskinvaruresurser.

Om du vill ge dig ett exempel säger du att du börjar med ett 5-nodskluster och en tjänst som är konfigurerad för att ha 10 partitioner och ett mål på tre repliker. I det här fallet skulle Service Fabric balansera och distribuera replikerna över klustret - och du skulle sluta med två primära [repliker](service-fabric-availability-services.md) per nod.
Om du nu behöver skala ut klustret till 10 noder, skulle Service Fabric balansera de primära [replikerna](service-fabric-availability-services.md) över alla 10 noder. På samma sätt, om du skalas tillbaka till 5 noder, skulle Service Fabric balansera alla repliker över 5 noder.  

Bild 2 visar fördelningen av 10 partitioner före och efter skalning av klustret.

![Tillståndskänslig tjänst](./media/service-fabric-concepts-partitioning/partitions.png)

Som ett resultat uppnås utskalningen eftersom begäranden från klienter distribueras över datorer, programmets övergripande prestanda förbättras och konkurrens om åtkomst till datasegment minskar.

## <a name="plan-for-partitioning"></a>Planera för partitionering
Innan du implementerar en tjänst bör du alltid överväga den partitioneringsstrategi som krävs för att skala ut. Det finns olika sätt, men alla fokuserar på vad programmet behöver uppnå. I samband med den här artikeln, låt oss överväga några av de viktigare aspekterna.

Ett bra tillvägagångssätt är att tänka på strukturen i den stat som måste delas, som det första steget.

Låt oss ta ett enkelt exempel. Om du skulle bygga en tjänst för en länsomfattande undersökning, kan du skapa en partition för varje stad i länet. Sedan kan du lagra rösterna för varje person i staden i partitionen som motsvarar den staden. Figur 3 illustrerar en uppsättning människor och den stad där de bor.

![Enkel partition](./media/service-fabric-concepts-partitioning/cities.png)

Eftersom befolkningen i städer varierar kraftigt, kan du sluta med vissa partitioner som innehåller en hel del data (t.ex. Seattle) och andra partitioner med mycket lite tillstånd (t.ex. Kirkland). Så vad är effekten av att ha partitioner med ojämna mängder av tillstånd?

Om du tänker på exemplet igen, kan du enkelt se att den partition som innehar rösterna för Seattle kommer att få mer trafik än Kirkland en. Som standard ser Service Fabric till att det finns ungefär samma antal primära och sekundära repliker på varje nod. Så du kan sluta med noder som håller repliker som tjänar mer trafik och andra som tjänar mindre trafik. Du skulle helst vilja undvika varma och kalla fläckar som denna i ett kluster.

För att undvika detta bör du göra två saker, från en partitionering synvinkel:

* Försök att partitionera tillståndet så att det är jämnt fördelat över alla partitioner.
* Rapportera belastning från var och en av replikerna för tjänsten. (Mer information om hur du läser den här artikeln om [mått och inläsning](service-fabric-cluster-resource-manager-metrics.md)). Service Fabric ger möjlighet att rapportera belastning som förbrukas av tjänster, till exempel mängden minne eller antal poster. Baserat på de rapporterade måtten upptäcker Service Fabric att vissa partitioner betjänar högre belastningar än andra och balanserar om klustret genom att flytta repliker till mer lämpliga noder, så att den totala noden överbelastas.

Ibland kan du inte veta hur mycket data som kommer att finnas i en viss partition. Så en allmän rekommendation är att göra både - först, genom att anta en partitionering strategi som sprider data jämnt över partitioner och andra, genom att rapportera belastning.  Den första metoden förhindrar situationer som beskrivs i röstningsexemplet, medan den andra hjälper till att jämna ut tillfälliga skillnader i åtkomst eller belastning över tid.

En annan aspekt av partitionsplanering är att välja rätt antal partitioner till att börja med.
Ur ett Service Fabric-perspektiv finns det inget som hindrar dig från att börja med ett större antal partitioner än förväntat för ditt scenario.
Faktum är att anta att det maximala antalet partitioner är en giltig metod.

I sällsynta fall kan du behöva fler partitioner än du ursprungligen har valt. Eftersom du inte kan ändra antalet partitioner i efterhand måste du använda vissa avancerade partitionsmetoder, till exempel skapa en ny tjänstinstans av samma tjänsttyp. Du måste också implementera viss logik på klientsidan som dirigerar begäranden till rätt tjänstinstans, baserat på kunskap på klientsidan som klientkoden måste underhålla.

En annan faktor för partitionering planering är de tillgängliga datorresurser. Eftersom tillståndet måste nås och lagras måste du följa:

* Begränsningar för nätverksbandbredd
* Begränsningar för systemminne
* Begränsningar för disklagring

Så vad händer om du stöter på resursbegränsningar i ett kluster som körs? Svaret är att du helt enkelt kan skala ut klustret för att tillgodose de nya kraven.

[Guiden för kapacitetsplanering](service-fabric-capacity-planning.md) ger vägledning för hur du avgör hur många noder klustret behöver.

## <a name="get-started-with-partitioning"></a>Komma igång med partitionering
I det här avsnittet beskrivs hur du kommer igång med partitionering av tjänsten.

Service Fabric erbjuder ett urval av tre partitionsscheman:

* Intervallpartitionering (kallas annars UniformInt64Partition).
* Namngiven partitionering. Program som använder den här modellen har vanligtvis data som kan bucketed, inom en begränsad uppsättning. Några vanliga exempel på datafält som används som namngivna partitionsnycklar är regioner, postnummer, kundgrupper eller andra affärsgränser.
* Singleton partitionering. Singleton-partitioner används vanligtvis när tjänsten inte kräver någon ytterligare routning. Tillståndslösa tjänster använder till exempel det här partitioneringsschemat som standard.

Namngivna och Singleton partitionering system är speciella former av varierade partitioner. Som standard använder Visual Studio-mallarna för Service Fabric varierade partitionering, eftersom det är den vanligaste och mest användbara. Resten av den här artikeln fokuserar på det varierade partitioneringsschemat.

### <a name="ranged-partitioning-scheme"></a>Intervallintervalleringsschema
Detta används för att ange ett heltalsintervall (identifieras med en låg nyckel och hög nyckel) och ett antal partitioner (n). Det skapar n partitioner, var och en ansvarig för en icke-överlappande underregister av den totala partitionen nyckelintervallet. Ett intervallpartitioneringsschema med en låg nyckel på 0, en hög nyckel på 99 och antalet 4 skulle till exempel skapa fyra partitioner, som visas nedan.

![Partitionering av område](./media/service-fabric-concepts-partitioning/range-partitioning.png)

En vanlig metod är att skapa en hash baserat på en unik nyckel i datauppsättningen. Några vanliga exempel på nycklar är ett fordonsidentifieringsnummer (VIN), ett medarbetar-ID eller en unik sträng. Genom att använda den här unika nyckeln skulle du sedan generera en hash-kod, modulus nyckelintervallet, att använda som din nyckel. Du kan ange de övre och nedre gränserna för det tillåtna nyckelintervallet.

### <a name="select-a-hash-algorithm"></a>Välj en hash-algoritm
En viktig del av hash-ning är att välja din hash-algoritm. En faktor är om målet är att gruppera liknande nycklar nära varandra (lokalkänslig hashing)--eller om aktiviteten bör fördelas brett över alla partitioner (distribution hashing), vilket är vanligare.

Egenskaperna hos en bra distribution hash algoritm är att det är lätt att beräkna, det har få kollisioner, och det distribuerar nycklarna jämnt. Ett bra exempel på en effektiv hash-algoritm är [FNV-1 hash-algoritmen.](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function)

En bra resurs för allmänna hash kod algoritm val är [Wikipedia sida på hash-funktioner](https://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Skapa en tillståndskänslig tjänst med flera partitioner
Nu ska vi skapa din första tillförlitliga tillståndskänsliga tjänst med flera partitioner. I det här exemplet ska du skapa ett mycket enkelt program där du vill lagra alla efternamn som börjar med samma bokstav i samma partition.

Innan du skriver någon kod måste du tänka på partitioner och partitionsnycklar. Du behöver 26 partitioner (en för varje bokstav i alfabetet), men hur är det med de låga och höga tangenterna?
Eftersom vi bokstavligen vill ha en partition per bokstav, kan vi använda 0 som lågmäld och 25 som hög nyckel, eftersom varje bokstav är sin egen nyckel.

> [!NOTE]
> Detta är ett förenklat scenario, eftersom fördelningen i själva verket skulle vara ojämn. Efternamn som börjar med bokstäverna "S" eller "M" är vanligare än de som börjar med "X" eller "Y".
> 
> 

1. Öppna **Visual Studio** > **File** > **New** > **Project**.
2. Välj programmet Service Fabric i dialogrutan **Nytt projekt.**
3. Kalla projektet "AlphabetPartitions".
4. I dialogrutan **Skapa en tjänst** väljer du **Tillståndskänslig** tjänst och kallar den "Alphabet.Processing".
5. Ange antalet partitioner. Öppna filen Applicationmanifest.xml i mappen ApplicationPackageRoot i AlphabetPartitions-projektet och uppdatera parametern Processing_PartitionCount till 26 enligt nedan.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Du måste också uppdatera LowKey- och HighKey-egenskaperna för elementet StatefulService i ApplicationManifest.xml enligt nedan.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. För att tjänsten ska vara tillgänglig öppnar du en slutpunkt på en port genom att lägga till slutpunktselementet i ServiceManifest.xml (som finns i PackageRoot-mappen) för tjänsten Alphabet.Processing enligt nedan:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Nu är tjänsten konfigurerad för att lyssna på en intern slutpunkt med 26 partitioner.
7. Därefter måste du åsidosätta metoden för `CreateServiceReplicaListeners()` klassen Processing.
   
   > [!NOTE]
   > För det här exemplet förutsätter vi att du använder en enkel HttpCommunicationListener. Mer information om tillförlitlig servicekommunikation finns i [Kommunikationsmodellen för tillförlitlig service](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Ett rekommenderat mönster för webbadressen som en replik lyssnar på är följande format: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    Så du vill konfigurera din kommunikation lyssnare att lyssna på rätt slutpunkter och med detta mönster.
   
    Flera repliker av den här tjänsten kan vara värd på samma dator, så den här adressen måste vara unik för repliken. Det är därför partitions-ID + replik-ID finns i URL:en. HttpListener kan lyssna på flera adresser på samma port så länge URL-prefixet är unikt.
   
    Den extra GUID är där för ett avancerat fall där sekundära repliker också lyssna efter skrivskyddade begäranden. När så är fallet vill du se till att en ny unik adress används vid övergången från primär till sekundär för att tvinga klienter att lösa adressen igen. '+' används som adress här så att repliken lyssnar på alla tillgängliga värdar (IP, FQDN, localhost, etc.) Koden nedan visar ett exempel.
   
    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
         return new[] { new ServiceReplicaListener(context => this.CreateInternalListener(context))};
    }
    private ICommunicationListener CreateInternalListener(ServiceContext context)
    {
   
         EndpointResourceDescription internalEndpoint = context.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");
         string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                context.PartitionId,
                context.ReplicaOrInstanceId,
                Guid.NewGuid());
   
         string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
   
         string uriPublished = uriPrefix.Replace("+", nodeIP);
         return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
   
    Det är också värt att notera att den publicerade webbadressen skiljer sig något från prefixet för lyssningsadress.
    Lyssningsadressen ges till HttpListener. Den publicerade URL:en är webbadressen som publiceras till tjänst fabric-namngivningstjänsten, som används för identifiering av tjänsten. Klienter kommer att be om den här adressen via identifieringstjänsten. Adressen som klienterna får måste ha den faktiska IP- eller FQDN-adressen för noden för att kunna ansluta. Så du måste ersätta "+" med nodens IP eller FQDN som visas ovan.
9. Det sista steget är att lägga till bearbetningslogiken i tjänsten enligt nedan.
   
    ```csharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        string output = null;
        string user = context.Request.QueryString["lastname"].ToString();
   
        try
        {
            output = await this.AddUserAsync(user);
        }
        catch (Exception ex)
        {
            output = ex.Message;
        }
   
        using (HttpListenerResponse response = context.Response)
        {
            if (output != null)
            {
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    private async Task<string> AddUserAsync(string user)
    {
        IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");
   
        using (ITransaction tx = this.StateManager.CreateTransaction())
        {
            bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);
   
            await tx.CommitAsync();
   
            return String.Format(
                "User {0} {1}",
                user,
                addResult ? "successfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest`läser värdena för frågesträngparametern som `AddUserAsync` används för att anropa partitionen och anrop för att lägga till det efternamn i den tillförlitliga ordlistan `dictionary`.
10. Nu ska vi lägga till en tillståndslös tjänst i projektet för att se hur du kan anropa en viss partition.
    
    Den här tjänsten fungerar som ett enkelt webbgränssnitt som accepterar det efternamn som en frågesträngparameter, bestämmer partitionsnyckeln och skickar den till tjänsten Alphabet.Processing för bearbetning.
11. I dialogrutan **Skapa en tjänst** väljer du **Tillståndslös** tjänst och kallar den "Alphabet.Web" som visas nedan.
    
    ![Skärmbild av tillståndslös tjänst](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Uppdatera slutpunktsinformationen i tjänstenManifest.xml för tjänsten Alphabet.WebApi för att öppna en port som visas nedan.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Du måste returnera en samling ServiceInstanceListeners på klasswebbplatsen. Återigen kan du välja att implementera en enkel HttpCommunicationListener.
    
    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] {new ServiceInstanceListener(context => this.CreateInputListener(context))};
    }
    private ICommunicationListener CreateInputListener(ServiceContext context)
    {
        // Service instance's URL is the node's IP & desired port
        EndpointResourceDescription inputEndpoint = context.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
        string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
        var uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInputRequest);
    }
    ```
14. Nu måste du implementera bearbetningslogiken. HttpCommunicationListener anropar `ProcessInputRequest` när en begäran kommer in. Så låt oss gå vidare och lägga till koden nedan.
    
    ```csharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        String output = null;
        try
        {
            string lastname = context.Request.QueryString["lastname"];
            char firstLetterOfLastName = lastname.First();
            ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    
            ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
            ResolvedServiceEndpoint ep = partition.GetEndpoint();
    
            JObject addresses = JObject.Parse(ep.Address);
            string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
            UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
            primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
            string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    
            output = String.Format(
                    "Result: {0}. <p>Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. <br>Processing service partition ID: {4}. <br>Processing service replica address: {5}",
                    result,
                    partitionKey,
                    firstLetterOfLastName,
                    lastname,
                    partition.Info.Id,
                    primaryReplicaAddress);
        }
        catch (Exception ex) { output = ex.Message; }
    
        using (var response = context.Response)
        {
            if (output != null)
            {
                output = output + "added to Partition: " + primaryReplicaAddress;
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    ```
    
    Låt oss gå igenom det steg för steg. Koden läser den första bokstaven `lastname` i frågesträngparametern i en röding. Sedan bestämmer den partitionsnyckeln för den här bokstaven genom `A` att subtrahera det hexadecimala värdet från från det hexadecimala värdet av efternamnens första bokstav.
    
    ```csharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Kom ihåg, för detta exempel använder vi 26 partitioner med en partitionsnyckel per partition.
    Därefter får vi tjänstpartitionen `partition` för `ResolveAsync` den här `servicePartitionResolver` nyckeln med hjälp av metoden på objektet. `servicePartitionResolver`definieras som
    
    ```csharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    Metoden `ResolveAsync` tar tjänsten URI, partitionsnyckeln och en annulleringstoken som parametrar. Tjänst-URI för bearbetningstjänsten är `fabric:/AlphabetPartitions/Processing`. Därefter får vi slutpunkten för partitionen.
    
    ```csharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Slutligen bygger vi slutpunkts-URL:en plus frågesträngen och anropar bearbetningstjänsten.
    
    ```csharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    När bearbetningen är klar skriver vi tillbaka utdata.
15. Det sista steget är att testa tjänsten. Visual Studio använder programparametrar för lokal distribution och molndistribution. Om du vill testa tjänsten med 26 partitioner `Local.xml` lokalt måste du uppdatera filen i mappen ApplicationParameters i AlphabetPartitions-projektet enligt nedan:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. När du är klar med distributionen kan du kontrollera tjänsten och alla dess partitioner i Service Fabric Explorer.
    
    ![Skärmbild av Service Fabric Explorer](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. I en webbläsare kan du testa partitioneringslogiken genom att ange `http://localhost:8081/?lastname=somename`. Du kommer att se att varje efternamn som börjar med samma bokstav lagras i samma partition.
    
    ![Skärmdump av webbläsare](./media/service-fabric-concepts-partitioning/samplerunning.png)

Hela källkoden för exemplet är tillgänglig på [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="next-steps"></a>Nästa steg
Information om service fabric-koncept finns i följande:

* [Tillgång till Service Fabric-tjänster](service-fabric-availability-services.md)
* [Skalbarhet för Service Fabric-tjänster](service-fabric-concepts-scalability.md)
* [Kapacitetsplanering för Service Fabric-applikationer](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
