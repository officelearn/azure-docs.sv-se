---
title: Partitionera Service Fabric-tjänster | Microsoft Docs
description: Beskriver hur du ska partitionera tillståndskänslig Service Fabric-tjänster. Partitioner möjliggör datalagring på de lokala datorerna så att data- och kan skalas tillsammans.
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
ms.openlocfilehash: ae7eba9997c4f567eb7b07e23ab42c9ac7740698
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388117"
---
# <a name="partition-service-fabric-reliable-services"></a>Partitionera Service Fabric reliable services
Den här artikeln innehåller en introduktion till de grundläggande principerna för Azure Service Fabric tillförlitliga tjänster partitioneras. Källkoden som används i artikeln finns också på [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Partitionering
Partitionering är inte unikt till Service Fabric. Det är i själva verket en core mönster för att skapa skalbara tjänster. Ett bredare perspektiv kan vi tänka på att lägga in partitionering som ett koncept att dela upp tillstånd (data) och compute till mindre tillgängliga enheter för att förbättra skalbarhet och prestanda. En välkänd form av partitionering är [Datapartitionering][wikipartition], även kallad horisontell partitionering.

### <a name="partition-service-fabric-stateless-services"></a>Tillståndslösa tjänster för Service Fabric för partition
För tillståndslösa tjänster kan tycker du om en partition är en logisk enhet som innehåller en eller flera instanser av en tjänst. Bild 1 visar en tillståndslös tjänst med fem instanser som är distribuerade över ett kluster med en partition.

![Tillståndslös tjänst](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Det är egentligen två typer av lösningar för tillståndslös tjänst. Den första är en tjänst som kvarstår dess status externt, till exempel i en Azure SQL-databas (till exempel en webbplats som lagrar information om mötessessioner och data). Den andra är endast beräkning tjänster (t.ex. kalkylator eller avbildning thumbnailing) som inte hanterar några permanent tillstånd.

I antingen fall partitionering en tillståndslös tjänst är ett väldigt ovanligt scenario--skalbarhet och tillgänglighet uppnås normalt genom att lägga till fler instanser. Den enda gången som du vill att flera partitioner för tillståndslös tjänstinstanser är när du behöver för att uppfylla särskilda dirigering av begäranden.

Tänk dig ett fall som där användare med ID: N i ett visst intervall bara hanteras av en viss tjänst-instans som ett exempel. Ett annat exempel på när du kan partitionera en tillståndslös tjänst är när du har en verkligt partitionerade serverdel (t.ex. ett delat SQL database) och du vill styra vilken tjänstinstans ska skriva till databasen fragment-- eller utföra andra förberedelse av inom den tillståndslös tjänst som kräver samma partitionering information som används i serverdelen. Dessa typer av scenarier kan också lösas på olika sätt och nödvändigtvis behöver inte tjänstens partitionering.

Resten av den här genomgången fokuserar på tillståndskänsliga tjänster.

### <a name="partition-service-fabric-stateful-services"></a>Tillståndskänsliga tjänster för Service Fabric för partition
Service Fabric gör det enkelt att utveckla skalbara tillståndskänsliga tjänster genom att erbjuda ett tydligt sätt att partitionen tillstånd (data). Den övergripande du tycker om en partition av en tillståndskänslig tjänst som en skalningsenhet som är mycket pålitlig via [repliker](service-fabric-availability-services.md) som är distribuerad och balanserade över noder i ett kluster.

Partitionering i samband med Service Fabric tillståndskänsliga tjänster refererar till hur du avgör att en viss tjänstpartition är ansvarig för en del av den fullständiga statusen för tjänsten. (Som tidigare nämnts, en partition är en uppsättning [repliker](service-fabric-availability-services.md)). En stor fördel om Service Fabric är att den placerar partitionerna på olika noder. Detta innebär att de växa till en nod gräns. När data behov ökar, partitioner växer och Service Fabric balanserar partitionerna mellan noder. Detta säkerställer en fortsatt effektiv användning av maskinvaruresurser.

Anta att du börjar med ett kluster med 5 noder och en tjänst som är konfigurerade för 10 partitioner och ett mål till tre repliker för att ge dig ett exempel. I det här fallet Service Fabric skulle balansera och distribuera replikerna över klustret – och du skulle få två primära [repliker](service-fabric-availability-services.md) per nod.
Om du nu vill skala ut kluster till 10 noder Service Fabric skulle balansera om primärt [repliker](service-fabric-availability-services.md) i alla 10 noder. På samma sätt, om du skalat tillbaka till 5 noder, Service Fabric skulle balansera om alla repliker för 5 noder.  

Bild 2 visar fördelningen av 10 partitioner före och efter skalning av klustret.

![Tillståndskänslig tjänst](./media/service-fabric-concepts-partitioning/partitions.png)

Därför kan uppnås skalbara eftersom begäranden från klienter distribueras till datorer, övergripande prestanda förbättras och minskar konkurrensen på åtkomst till segment av data.

## <a name="plan-for-partitioning"></a>Planera för partitionering
Innan du implementerar en tjänst, bör du alltid partitioneringsstrategin som krävs för att skala ut. Det finns olika sätt, men alla fokusera på vad programmet måste uppnå. För kontexten för den här artikeln, anta att du har några av de viktigaste aspekterna.

En bra metod är att tänka på strukturen för de tillstånd som måste vara partitionerade som det första steget.

Låt oss ta ett enkelt exempel. Om du bygger en tjänst för en countywide omröstning, kan du skapa en partition för varje ort i regionen. Sedan kan du lagra rösterna för varje person i stad i partition som motsvarar den staden. Bild 3 illustrerar en uppsättning personer och stad där de finns.

![Enkel partition](./media/service-fabric-concepts-partitioning/cities.png)

Eftersom befolkningen i städer varierar mycket, kan du få med vissa partitioner som innehåller stora mängder data (t.ex. Seattle) och andra partitioner med mycket lite tillstånd (t.ex. Kirkland). Vad är konsekvensen av att partitioner med ojämn mängder tillstånd?

Om du tycker om exemplet igen kan du enkelt se att den partition som innehåller röster för Seattle kommer mer trafik än Kirkland något. Service Fabric ser till att det handlar om samma antal primära och sekundära repliker på varje nod som standard. Så kan du få med noder som har repliker som betjänar mer trafik och andra som har mindre trafik. Du helst vill undvika heta och kalla platser så här i ett kluster.

För att undvika detta ska du göra två saker från en partitionering synsätt:

* Försök att partitionera tillståndet så att det är jämnt fördelat över alla partitioner.
* Rapportera belastning från varje replik för tjänsten. (Information om hur Kolla in den här artikeln på [mått och Load](service-fabric-cluster-resource-manager-metrics.md)). Service Fabric ger möjlighet att rapportera belastning som används av tjänster, till exempel mängden minne eller antal poster. Baserat på de mått som rapporteras, upptäcker Service Fabric att vissa partitioner har högre belastning än andra och balanserar klustret genom att flytta repliker till lämpligast noder, så att den totala någon nod är överbelastad.

Ibland kan inte vet du hur mycket data kommer att finnas i en given partition. Så en allmän rekommendation är att göra både--först sprids genom att använda en partitioneringsstrategi som data jämnt över partitionerna och sekund, med reporting belastningen.  Den första metoden förhindrar situationer som beskrivs i röstande exempel samtidigt som de andra bidrar till att jämna ut tillfälliga skillnader i åtkomst eller belastning över tid.

En annan aspekt av partition planering är att välja rätt antal partitioner.
Från en Service Fabric-perspektivet finns det inget som förhindrar att du börjar med ett högre antal partitioner än förväntat för ditt scenario.
I själva verket förutsatt att det maximala antalet partitioner är en giltig metod.

I sällsynta fall kan du få behöva fler partitioner än du ursprungligen har valt. Eftersom du inte kan ändra antalet partitioner i efterhand, skulle du behöva använda vissa avancerade partition-metoder, till exempel skapa en ny tjänstinstans för samma tjänsttypen. Du kan också behöva implementera logik på klientsidan som dirigerar begäranden till rätt tjänst-instans, baserat på klientsidan kunskaper som klientkoden måste underhållas.

Ett annat övervägande för partitionering av planering är tillgängliga datorresurser. Eftersom tillståndet måste vara och som lagras, är du kopplade till följer:

* Gränser för bandbredd i nätverket
* Minnesgränser för system
* Lagringsgränserna för disk

Så vad händer om du stöter på resursbegränsningar i ett aktivt kluster? Svaret är att du kan helt enkelt skala ut klustret för att anpassa dem till nya krav.

[Kapacitetsplaneringsguiden](service-fabric-capacity-planning.md) ger vägledning att fastställa hur många noder som behövs i klustret.

## <a name="get-started-with-partitioning"></a>Kom igång med partitionering
Det här avsnittet beskrivs hur du kommer igång med partitionering din tjänst.

Service Fabric kan välja mellan tre partitionsscheman:

* Intervallet partitionering (kallas även UniformInt64Partition).
* Namnet partitionering. Program med hjälp av den här modellen normalt har data som kan vara bucketas, inom en begränsad uppsättning. Några vanliga exempel på datafält som används som namngiven partitionsnycklar skulle vara regioner, postnummer, kundgrupper eller andra företag gränser.
* Singleton partitionering. Singleton-partitioner används vanligtvis när tjänsten inte kräver några ytterligare routning. Tillståndslösa tjänster använder till exempel det här partitioneringsschemat som standard.

Med namnet och Singleton partitioneringsscheman är särskilda typer av intervallet partitioner. Som standard intervallet Visual Studio-mallar för Service Fabric partitionering, eftersom det är det vanligaste. Resten av den här artikeln fokuserar på ranged partitioneringsschema.

### <a name="ranged-partitioning-scheme"></a>Intervallet partitioneringsschemat
Det här används för att ange ett heltal adressintervall (som identifieras med en låg nyckel och hög nyckel) och ett antal partitioner (n). N partitioner, var ansvarig för en icke-överlappande underintervall av övergripande partitionsnyckelintervall skapas. Till exempel ett ranged partitioneringsschema med en låg nyckel 0, en hög nyckel 99 och antalet 4 skulle skapa fyra partitioner enligt nedan.

![Intervall partitionering](./media/service-fabric-concepts-partitioning/range-partitioning.png)

En vanlig metod är att skapa en hash-värde baserat på en unik nyckel i datauppsättningen. Några vanliga exempel på nycklar skulle vara en vehicle ID-nummer (VIN), en anställnings-ID eller en unik sträng. Med hjälp av den här unika nyckeln skulle du sedan skapa en Hashkod, modulus nyckelintervall, ska användas som din nyckel. Du kan ange de övre och nedre gränserna för det tillåtna intervallet för nyckeln.

### <a name="select-a-hash-algorithm"></a>Välj en hash-algoritm
En viktig del av hashing är att välja hash-algoritm. Ersättning är om målet är att gruppera liknande nycklar nära varandra (ort känsliga hashing)-- eller om aktiviteten ska distribueras brett för alla partitioner (hash-distribution), vilket är vanligare.

Egenskaperna för en bra distribution hashalgoritm är att det är enkelt att beräkna, den har några kollisioner och den distribuerar nycklarna jämnt. Ett bra exempel på en effektiv hash-algoritm är den [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) hash-algoritm.

En bra resurs för allmän hash kod algoritmen val är den [Wikipedia-sida på hash-funktioner](http://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Skapa en tillståndskänslig tjänst med flera partitioner
Nu ska vi skapa din första tillförlitlig tillståndskänslig tjänst med flera partitioner. I det här exemplet skapar du ett väldigt enkelt program där du vill lagra alla efternamn som börjar på samma i samma partition.

Innan du skriver någon kod, måste du tänka på partitioner och partitionsnycklar. Du behöver 26 partitioner (en för varje bokstäver i alfabetet), men vad om låga och höga nyckelvärden?
Eftersom du verkligen vill ha en partition per bokstav kan vi använda 0 som nyckeln låg och 25 som hög nyckeln, eftersom varje bokstav sin egen nyckel.

> [!NOTE]
> Det här är ett förenklat scenario, vilket i verkligheten distributionen är ojämn. Senaste namn som börjar med bokstäverna ”S” eller ”M” är vanligare än de som börjar med ”X” eller ”Y”.
> 
> 

1. Öppna **Visual Studio** > **filen** > **nya** > **projekt**.
2. I den **nytt projekt** dialogrutan väljer du Service Fabric-program.
3. Anropa projektet ”AlphabetPartitions”.
4. I den **skapar du en tjänst** dialogrutan väljer du **tillståndskänslig** -tjänsten och anropa det ”Alphabet.Processing” enligt bilden nedan.
       ![Dialogrutan Ny tjänst i Visual Studio][1]

  <!--  ![Stateful service screenshot](./media/service-fabric-concepts-partitioning/createstateful.png)-->

5. Ange antalet partitioner. Öppna filen Applicationmanifest.xml finns i mappen ApplicationPackageRoot projektets AlphabetPartitions och uppdatera parametern Processing_PartitionCount till 26 enligt nedan.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Du måste också uppdatera LowKey och HighKey egenskaperna för elementet StatefulService i ApplicationManifest.xml enligt nedan.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. För att tjänsten ska vara tillgängliga, öppna en slutpunkt på en port genom att lägga till slutpunkten-elementet i ServiceManifest.xml (som finns i mappen PackageRoot) för tjänsten Alphabet.Processing enligt nedan:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Tjänsten är nu konfigurerad för att lyssna på en intern slutpunkt med 26 partitioner.
7. Nu ska du behöver åsidosätta den `CreateServiceReplicaListeners()` metoden i klassen bearbetning.
   
   > [!NOTE]
   > I det här exemplet förutsätter vi att du använder en enkel HttpCommunicationListener. Läs mer på tillförlitlig kommunikation [The tillförlitlig tjänst modell](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Ett rekommenderat mönster för URL: en som en replik som lyssnar på har följande format: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    Så att du vill konfigurera din kommunikationslyssnaren för att lyssna på rätt slutpunkterna och med det här mönstret.
   
    Flera kopior av den här tjänsten kan finnas på samma dator, så den här adressen måste vara unikt för repliken. Det är därför partitions-ID + replik-ID finns i URL: en. HttpListener kan lyssna på flera adresser på samma port som URL-prefixet är unikt.
   
    Extra GUID finns det för ett avancerade fall där sekundära repliker även lyssna efter begäranden som skrivskyddad. När så är fallet, som du vill se till att en ny unik adress används när övergången från primära till sekundära för att tvinga klienter att matcha adressen igen. ”+” används som den här adressen så att repliken lyssnar på alla tillgängliga värdar (IP, FQDM localhost, osv.) Koden nedan visar ett exempel.
   
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
   
    Det är också värt att publicerade URL: en är skiljer sig från det lyssnande URL-prefixet.
    URL: en som lyssnar ges till HttpListener. Publicerade URL: en är den URL som har publicerats i Service Fabric Naming Service, som används för identifiering av tjänst. Klienter begär den här adressen via av identifieringstjänsten. Den adress som klienter hämtar måste ha en faktisk IP eller FQDN för noden för att kunna ansluta. Så du måste ersätta ”+” med nodens IP eller FQDN som ovan.
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
   
    `ProcessInternalRequest` läser värdena för frågesträngparametern som används för att anropa partition och anrop `AddUserAsync` att lägga till efternamn tillförlitlig ordlista `dictionary`.
10. Vi lägger till en tillståndslös tjänst till projektet för att se hur du kan anropa en viss partition.
    
    Den här tjänsten fungerar som ett enkelt webbgränssnitt som accepterar efternamn som en frågesträngsparameter avgör Partitionsnyckeln och skickar dem till Alphabet.Processing-tjänsten för bearbetning.
11. I den **skapar du en tjänst** dialogrutan väljer du **Stateless** -tjänsten och anropa det ”Alphabet.Web” enligt nedan.
    
    ![Skärmbild för tillståndslös tjänst](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Uppdatera information om slutpunkten i ServiceManifest.xml av tjänsten Alphabet.WebApi så att du öppnar en port som visas nedan.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Du måste returnera en samling med ServiceInstanceListeners i Web-klassen. Igen, kan du implementera en enkel HttpCommunicationListener.
    
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
14. Nu ska du implementera standardbearbetningslogiken. HttpCommunicationListener anrop `ProcessInputRequest` när en begäran kommer in. Så vi går vidare och Lägg till koden nedan.
    
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
    
    Låt oss gå igenom det steg för steg. Koden läser den första bokstaven i frågesträngparametern `lastname` till char. Sedan den avgör Partitionsnyckeln för den här bokstaven genom att subtrahera det hexadecimala värdet `A` från det hexadecimala värdet för första bokstaven i sista namnen.
    
    ```CSharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Kom ihåg att det här exemplet använder vi 26 partitioner med en partitionsnyckel per partition.
    Nu ska vi hämta service partitionen `partition` för den här nyckeln med hjälp av den `ResolveAsync` metoden på den `servicePartitionResolver` objekt. `servicePartitionResolver` definieras som
    
    ```CSharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    Den `ResolveAsync` metoden tar tjänstens URI, Partitionsnyckeln och en uppsägning token som parametrar. Tjänsten URI för tjänst för händelsebearbetning är `fabric:/AlphabetPartitions/Processing`. Nu ska hämta vi slutpunkten för partitionen.
    
    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Slutligen kan vi skapa slutpunkts-URL plus frågesträngen och anropa bearbetningstjänsten.
    
    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    När bearbetningen är klar kan vi skriva utdata tillbaka.
15. Det sista steget är att testa tjänsten. Visual Studio använder programparametrar för lokal och distribution. Om du vill testa tjänsten med 26 partitioner lokalt, måste du uppdatera den `Local.xml` filen i mappen ApplicationParameters projektets AlphabetPartitions enligt nedan:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. När distributionen är klar kan du kontrollera tjänsten och alla dess partitioner i Service Fabric Explorer.
    
    ![Skärmbild av Service Fabric Explorer](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. I en webbläsare kan du testa partitionering logiken genom att ange `http://localhost:8081/?lastname=somename`. Där ser du att varje efternamn som börjar med samma enhetsbeteckning som lagras i samma partition.
    
    ![Skärmbild för webbläsare](./media/service-fabric-concepts-partitioning/samplerunning.png)

Hela källkoden för exemplet finns på [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="reliable-services-and-actor-forking-subprocesses"></a>Reliable Services och aktören Förgrena delprocesser
Service Fabric stöder inte reliable services och därefter reliable actors Förgrena delprocesser. Ett exempel på varför det är inte stöds är [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet) kan inte användas för att registrera ett som inte stöds underprocess och säger upp avtalet token skickas endast till registrerad processer, vilket resulterar i alla typer av problem, till exempel uppgradera fel, när delprocesser inte Stäng när den överordnade processen har tagit emot en token för annullering. 

## <a name="next-steps"></a>Nästa steg
Information om Service Fabric-begrepp finns i följande:

* [Tillgängligheten för Service Fabric-tjänster](service-fabric-availability-services.md)
* [Skalbarhet för Service Fabric-tjänster](service-fabric-concepts-scalability.md)
* [Kapacitetsplanering för Service Fabric-program](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
