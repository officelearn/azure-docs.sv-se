---
title: Partitionering Service Fabric-tjänster | Microsoft Docs
description: Beskriver hur du partitionera tillståndskänslig Service Fabric-tjänster. Partitioner möjliggör lagring av data på lokala datorer så att data och beräkning kan skalas tillsammans.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 3b7248c8-ea92-4964-85e7-6f1291b5cc7b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: msfussell
ms.openlocfilehash: bc6f25c7a8a779d949fbd09f9a9a9a37ec83f56a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="partition-service-fabric-reliable-services"></a>Partitionen Service Fabric reliable services
Den här artikeln innehåller en introduktion till de grundläggande principerna för partitionering tillförlitliga Azure Service Fabric-tjänster. Källkoden som används i artikeln finns även på [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Partitionering
Partitionering är inte unikt för Service Fabric. Det är faktiskt en core mönster för att skapa skalbara tjänster. I en bredare mening vi tror om partitionering som ett koncept för att dela tillstånd (data) och beräkna till mindre tillgänglig enheter för att förbättra skalbarhet och prestanda. Ett välkänt formulär partitionering är [Datapartitionering][wikipartition], även kallad horisontell partitionering.

### <a name="partition-service-fabric-stateless-services"></a>Tillståndslösa tjänster för Service Fabric för partition
Du kan se om en partition är en logisk enhet som innehåller en eller flera instanser av en tjänst för tillståndslösa tjänster. Bild 1 visar tillståndslösa tjänsten med fem instanser distribuerade i ett kluster med en partition.

![Tillståndslösa tjänsten](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Det finns två typer av tillståndslösa tjänstelösningar verkligen. Den första är en tjänst som kvarstår dess tillstånd externt, till exempel i en Azure SQL database (till exempel en webbplats som lagrar sessionsinformation och data). Den andra är endast beräkning-tjänster (till exempel Kalkylatorn eller image thumbnailing) som inte hanterar några beständiga tillstånd.

I antingen fallet partitionering tillståndslösa tjänsten är ett mycket sällsynt scenario--skalbarhet och tillgänglighet uppnås normalt genom att lägga till flera instanser. Den enda gång som du vill överväga flera partitioner för tillståndslösa tjänstinstanser är när du behöver för att uppfylla särskilda routning begäran.

Exempelvis bör du fall där användare med ID: N i ett visst intervall endast hanteras av en viss tjänst-instans. Ett annat exempel på när du gick partitionera en tillståndslös tjänst är när du har en verkligen partitionerade serverdel (t.ex. ett delat SQL database) och du vill styra vilken tjänstinstans ska skriva till databasen Fragmentera-- eller utföra annat arbete förberedelse inom den tillståndslösa tjänsten som kräver samma partitionering information som används i serverdelen. Dessa typer av scenarier kan också lösas på olika sätt och kräver inte nödvändigtvis service partitionering.

Resten av den här genomgången fokuserar på tillståndskänsliga tjänster.

### <a name="partition-service-fabric-stateful-services"></a>Tillståndskänsliga tjänster för Service Fabric för partition
Service Fabric gör det enkelt att utveckla skalbara tillståndskänsliga tjänster genom att erbjuda förstklassig kan partition tillstånd (data). Begreppsmässigt, du kan se om en partition av en tillståndskänslig service som en skalningsenhet som är mycket pålitlig via [repliker](service-fabric-availability-services.md) som distribueras och balanserat över noder i ett kluster.

Partitionering i samband med Service Fabric tillståndskänsliga tjänster refererar till process för att fastställa att en viss tjänst partition är ansvarig för en del av den fullständiga statusen för tjänsten. (Som tidigare nämnts, en partition är en uppsättning [repliker](service-fabric-availability-services.md)). En fantastiska med Service Fabric är att den placerar partitionerna på olika noder. Detta innebär att de ska växa till en nod resursgräns. När data behöver växa, partitioner växer och Service Fabric balanserar partitioner mellan noder. Detta säkerställer fortsatt effektiv användning av maskinvaruresurser.

För att ge dig ett exempel, anta att du börjar med ett kluster med 5 och en tjänst som är konfigurerad med 10 partitioner och ett mål för tre repliker. I det här fallet Service Fabric skulle balansera och distribuera replikerna över kluster – och du skulle hamna med två primära [repliker](service-fabric-availability-services.md) per nod.
Om du nu behöver skala upp till 10 noder klustret Service Fabric skulle balansera om primärt [repliker](service-fabric-availability-services.md) alla 10 noder. Likaså om du skalas upp till 5 noder skulle Service Fabric balansera om alla repliker över 5 noder.  

Bild 2 visar fördelningen av 10 partitioner före och efter skalning av klustret.

![Tillståndskänslig service](./media/service-fabric-concepts-partitioning/partitions.png)

Därför kan uppnås skalbara eftersom förfrågningar från klienter distribueras mellan datorer, bättre prestanda i programmet och konkurrerar om tillgång till segment av minskas.

## <a name="plan-for-partitioning"></a>Planera för partitionering
Innan du implementerar en tjänst, bör du alltid partitioneringsstrategi som behövs för att skala ut. Det finns olika sätt, men alla fokusera på vad programmet måste uppnå. Kontexten för den här artikeln ska vi bör du några av de viktigaste aspekterna.

En bra metod är att tänka strukturen för det tillstånd som måste vara partitionerade som det första steget.

Låt oss ta ett enkelt exempel. Om du skapar en tjänst för en countywide avsökning, kan du skapa en partition för varje ort i delstaten. Sedan kan du lagra rösterna för varje person i stad i partitionen som motsvarar den staden. Bild 3 illustrerar en uppsättning användare och den stad som de finns.

![Enkel partition](./media/service-fabric-concepts-partitioning/cities.png)

Som befolkningen i orter varierar mycket, kan det sluta med några partitioner som innehåller stora mängder data (till exempel Seattle) och andra partitioner med mycket lite tillstånd (t.ex. Kirkland). Vad är effekten av med partitioner med en ojämn mängder tillstånd?

Om du tycker om exemplet igen kan kan du enkelt se att den partition som innehåller rösterna för Seattle kommer mer trafik än Kirkland en. Service Fabric ser till att det handlar om samma antal primära och sekundära repliker på varje nod som standard. Därför kan det sluta med noder som har repliker som fungerar mer nätverkstrafik och andra som hanterar mindre trafik. Du vill helst undvika varm eller kall punkter så här i ett kluster.

För att undvika detta ska du göra två saker från en partitionering synsätt:

* Försök att partitionera tillståndet så att det är jämnt fördelad över alla partitioner.
* Rapportera belastning från varje replik för tjänsten. (Mer information om hur checka ut den här artikeln på [mått och Läs in](service-fabric-cluster-resource-manager-metrics.md)). Service Fabric ger möjlighet att rapportera belastning som används av tjänster, till exempel minne eller antal poster. Utifrån de mätvärden som rapporterats, upptäcker Service Fabric att en del partitioner hanterar högre belastning än andra och balanserar klustret genom att flytta repliker till lämpligare noder så att övergripande ingen nod är överbelastad.

Du kan ibland vet hur mycket data visas i en given partition. Så att en allmän rekommendation är att båda--först sprids genom användning av en partitioneringsstrategi som data jämnt över partitioner och andra, med reporting belastningen.  Den första metoden förhindrar situationer som beskrivs i röstning exempel, medan andra kan jämna ut temporära skillnader i åtkomst eller belastningsutjämning över tid.

En annan aspekt av partition planering är att välja rätt antal partitioner börja med.
Från ett Service Fabric-perspektiv finns det inget som förhindrar börjat med ett högre antal partitioner än förväntat för ditt scenario.
I själva verket under förutsättning att det maximala antalet partitioner är en giltig metod.

I sällsynta fall kan du få behöver fler partitioner än du ursprungligen har valt. Eftersom du inte kan ändra antalet partitioner efteråt, behöver du tillämpa vissa avancerade partition metoder, till exempel skapa en ny service-instans av samma service-typen. Du kan också behöva implementera vissa klientsidan logik som skickar begäranden till rätt tjänstinstansen, baserat på klientsidan kunskap som din klientkod måste underhåll.

Ett annat övervägande för partitionering av planering är tillgängliga datorresurser. När tillståndet behöver komma åt och lagras, är du bundna till följer:

* Gränser för bandbredd i nätverket
* System minnesgränserna
* Disk Lagringsgränser

Så vad händer om du stöter på resursen begränsningar i ett kluster som körs? Svaret är att du kan helt enkelt skala ut klustret för att anpassa de nya krav.

[Kapacitetsplaneringsguiden](service-fabric-capacity-planning.md) ger vägledning att fastställa hur många noder i klustret måste.

## <a name="get-started-with-partitioning"></a>Kom igång med partitionering
Det här avsnittet beskrivs hur du kommer igång med partitionering din tjänst.

Service Fabric kan välja mellan tre partitionsscheman:

* Låg partitionering (kallas även UniformInt64Partition).
* Namngivna partitionering. Program med hjälp av den här modellen normalt har data som kan vara bucketed, inom en begränsad mängd. Några vanliga exempel datafält som används som namngivna partitionsnycklar skulle vara regioner, postnummer, kundgrupper eller andra företag gränser.
* Singleton partitioneras. Singleton-partitioner används vanligtvis när tjänsten inte kräver ytterligare routning. Till exempel använder tillståndslösa tjänster den här partitioneringsschema som standard.

Namnet och scheman som Singleton partitionering är särskilda typer av låg partitioner. Som standard låg Visual Studio-mallar för Service Fabric-partitionering, som det är den vanligaste. Resten av den här artikeln fokuserar på ranged partitioneringsschema.

### <a name="ranged-partitioning-scheme"></a>Låg partitioneringsschema
Det här används för att ange ett heltalsintervall (som identifieras med en nyckel för låg och hög nyckel) och ett antal partitioner (n). N partitioner, varje ansvarar för en icke-överlappande underintervall av övergripande partitionsnyckelintervallet skapas. Till exempel en ranged partitioneringsschema med en låg nyckel 0, övre nyckel 99 och antalet 4 skulle skapa fyra partitioner, enligt nedan.

![Intervallet partitionering](./media/service-fabric-concepts-partitioning/range-partitioning.png)

En vanlig metod är att skapa ett hash-värde baserat på en unik nyckel i datauppsättningen. Några vanliga exempel nycklar skulle vara vehicle ID-nummer (VIN), en anställnings-ID eller en unik sträng. Du skulle sedan generera en Hashkod, modulus viktiga intervallet ska användas som din nyckel med hjälp av den här Unik nyckel. Du kan ange det tillåtna intervallet för viktiga övre och nedre gränser.

### <a name="select-a-hash-algorithm"></a>Välj en hash-algoritm
En viktig del av hashing är att välja hash-algoritm. En faktor är om målet är att gruppera liknande nycklar nära varandra (ort känsliga hashing)-- eller om aktiviteten ska distribueras brett för alla partitioner (hash-distribution), vilket är mer vanligt.

Egenskaperna för en fungerande distribution hash-algoritm är att det är enkelt att beräkna den har några kollisioner och nycklar distribueras jämnt. Ett bra exempel på en effektiv hash-algoritm är den [FNV 1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) hash-algoritm.

Mer allmän hash-kod algoritmen alternativ är den [Wikipedia sida på hash-funktioner](http://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Skapa en tillståndskänslig tjänst med flera partitioner
Nu ska vi skapa din första tillförlitliga tillståndskänslig tjänst med flera partitioner. I det här exemplet skapar du ett enkelt program där du vill lagra alla efternamn som börjar med samma bokstav i samma partition.

Innan du skriva kod som du behöver tänka på partitioner och partitionsnycklar. Du behöver 26 partitioner (en för varje bokstäver i alfabetet) men vad om låg och hög nycklar?
Eftersom vi bokstavligt vill ha en partition per enhetsbeteckning kan vi använder 0 som nyckeln låg och 25 som övre nyckel som varje bokstav är egen.

> [!NOTE]
> Detta är en förenklad scenariot i verkligheten distributionen är ojämnt. Senaste namn som börjar med bokstäver ”S” eller ”M” är vanligare än de som börjar med ”X” eller ”Y”.
> 
> 

1. Öppna **Visual Studio** > **filen** > **nya** > **projekt**.
2. I den **nytt projekt** dialogrutan Välj Service Fabric-programmet.
3. Anropa projektet ”AlphabetPartitions”.
4. I den **skapar du en tjänst** dialogrutan Välj **Stateful** tjänst och kalla den ”Alphabet.Processing” som visas i bilden nedan.
       ![Dialogrutan Ny tjänst i Visual Studio][1]

  <!--  ![Stateful service screenshot](./media/service-fabric-concepts-partitioning/createstateful.png)-->

5. Ange antalet partitioner. Öppna filen Applicationmanifest.xml finns i mappen ApplicationPackageRoot AlphabetPartitions-projektet och uppdatera parametern Processing_PartitionCount-26 enligt nedan.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Du måste också uppdatera egenskaperna LowKey och HighKey för elementet StatefulService i ApplicationManifest.xml enligt nedan.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. För tjänsten är tillgänglig, öppna en slutpunkt på en port genom att lägga till slutpunktselementet i ServiceManifest.xml (finns i mappen PackageRoot) för tjänsten Alphabet.Processing enligt nedan:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Tjänsten är nu konfigurerad för att lyssna på en intern slutpunkt med 26 partitioner.
7. Sedan måste åsidosätta den `CreateServiceReplicaListeners()` metoden i klassen bearbetning.
   
   > [!NOTE]
   > För det här exemplet förutsätter vi att du använder en enkel HttpCommunicationListener. Mer information om tillförlitlig kommunikation finns [tjänsten tillförlitlig kommunikation modellen](service-fabric-reliable-services-communication.md).
   > 
   > 
8. En rekommenderad mönster för URL: en som en replik som lyssnar på är följande format: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    Så att du vill konfigurera din kommunikation lyssnare för att lyssna på rätt slutpunkter och med det här mönstret.
   
    Flera repliker av den här tjänsten kan finnas på samma dator, så att den här adressen måste vara unikt på repliken. Det är därför partitions-ID + replik-ID är URL-adressen. HttpListener kan lyssna på flera adresser på samma port som URL-prefixet är unikt.
   
    Extra GUID är det för en avancerad fall där sekundära repliker också lyssna efter begäranden i skrivskyddat läge. När så är fallet, som du vill kontrollera att en ny unik adress används vid övergång från primär till sekundär för att tvinga klienter att matcha adressen igen. ”+” används som den här adressen så att repliken lyssnar på alla tillgängliga värdar (IP, FQDM localhost, osv.) Koden nedan visar ett exempel.
   
    ```CSharp
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
   
    Det är också värt att nämna att publicerade URL: en är skiljer sig från lyssnande URL-prefixet.
    URL: en lyssnande ges till HttpListener. Publicerade URL: en är den URL som publicerats till Service Fabric Naming Service, som används för identifiering av tjänst. Klienter begär den här adressen genom att discovery-tjänsten. Den adress som klienterna hämta måste ha en faktisk IP eller FQDN för noden för att kunna ansluta. Så du behöver ersätta '+' med nodens IP eller FQDN som ovan.
9. Det sista steget är att lägga till standardbearbetningslogiken i tjänsten som visas nedan.
   
    ```CSharp
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
                addResult ? "sucessfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest` läser värdena för frågesträngparametern som används för att anropa partition och anrop `AddUserAsync` att lägga till efternamn tillförlitliga ordlistan `dictionary`.
10. Lägg till en tillståndslös tjänst projekt för att se hur du kan anropa en viss partition.
    
    Den här tjänsten fungerar som ett enkelt webbgränssnitt som accepterar efternamn som en frågesträngsparameter anger Partitionsnyckeln och skickar det till tjänsten Alphabet.Processing för bearbetning.
11. I den **skapar du en tjänst** dialogrutan Välj **Stateless** tjänst och kalla den ”Alphabet.Web” enligt nedan.
    
    ![Skärmbild av tillståndslösa tjänsten](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Uppdatera slutpunktsinformationen i ServiceManifest.xml Alphabet.WebApi-tjänsten för att öppna en port som visas nedan.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Du måste returnera en samling med ServiceInstanceListeners i klassen Web. Igen, kan du välja att implementera en enkel HttpCommunicationListener.
    
    ```CSharp
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
14. Nu måste du implementera logik för bearbetning. HttpCommunicationListener anropen `ProcessInputRequest` när en begäran kommer. Så Låt oss och Lägg till koden nedan.
    
    ```CSharp
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
    
    Nu ska vi gå igenom den steg för steg. Koden läser den första bokstaven i frågesträngparametern `lastname` till char. Sedan den avgör Partitionsnyckeln för den här bokstaven genom att subtrahera det hexadecimala värdet av `A` från det hexadecimala värdet för senaste namnen med versal.
    
    ```CSharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Kom ihåg att det här exemplet använder vi 26 partitioner med en partitionsnyckel per partition.
    Nu ska vi får service partitionen `partition` för den här nyckeln med hjälp av den `ResolveAsync` metod på den `servicePartitionResolver` objekt. `servicePartitionResolver` definieras som
    
    ```CSharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    Den `ResolveAsync` metoden tar tjänsten URI, Partitionsnyckeln och en annullering token som parametrar. Tjänsten URI för behandling av tjänsten är `fabric:/AlphabetPartitions/Processing`. Kan gå vi slutpunkten för partitionen.
    
    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Slutligen vi skapar slutpunkts-URL plus frågesträngen och anropa behandling av tjänsten.
    
    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    När bearbetningen är klar, vi skriva utdata tillbaka.
15. Det sista steget är att testa tjänsten. Visual Studio använder programmet parametrar för lokal och distribution. Om du vill testa tjänsten med 26 partitioner lokalt, måste du uppdatera den `Local.xml` filen i mappen ApplicationParameters AlphabetPartitions-projektet enligt nedan:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Du kan kontrollera tjänsten och alla dess partitioner i Service Fabric Explorer när du är klar med distributionen.
    
    ![Service Fabric Explorer skärmbild](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. I en webbläsare, kan du testa partitionering logiken genom att ange `http://localhost:8081/?lastname=somename`. Du ser att varje efternamn som börjar med samma enhetsbeteckning som lagras i samma partition.
    
    ![Skärmbild för webbläsare](./media/service-fabric-concepts-partitioning/samplerunning.png)

Källkoden hela det här exemplet är tillgängligt på [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="next-steps"></a>Nästa steg
Information om Service Fabric-begrepp finns i:

* [Tillgänglighet för Service Fabric-tjänster](service-fabric-availability-services.md)
* [Skalbarheten för Service Fabric-tjänster](service-fabric-concepts-scalability.md)
* [Kapacitetsplanering för Service Fabric-program](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png