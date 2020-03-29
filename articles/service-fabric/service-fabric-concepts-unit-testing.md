---
title: Enhetstestningtillståndskänsliga tjänster i Azure Service Fabric
description: Lär dig mer om begreppen och metoderna för enhetstestning Av Service Fabric Stateful Services.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 12e8a47d9685dee12594f4e2afaa848d9688d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433913"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Enhetstestning tillståndskänsliga tjänster i Service Fabric

Den här artikeln beskriver begreppen och praxis för enhetstestning Service Fabric Stateful Services. Enhetstestning inom Service Fabric förtjänar sina egna överväganden på grund av att programkoden aktivt körs under flera olika sammanhang. I den här artikeln beskrivs de metoder som används för att säkerställa att programkoden omfattas av var och en av de olika sammanhang som den kan köra.

## <a name="unit-testing-and-mocking"></a>Enhetstestning och hån
Enhetstestning i samband med den här artikeln är automatiserad testning som kan utföras inom ramen för en testlöpare som MSTest eller NUnit. Enhetstesterna i den här artikeln utför inte åtgärder mot en fjärrresurs, till exempel en databas eller RESTFUL API. Dessa fjärrresurser bör hånas. Hån i samband med den här artikeln kommer att fejka, spela in och kontrollera returvärden för fjärrresurser.

### <a name="service-fabric-considerations"></a>Service Fabric överväganden
Enhet som testar en service fabric-tillståndskänslig tjänst har flera överväganden. För det första körs servicekoden på flera noder men under olika roller. Enhetstester bör utvärdera koden under varje roll för att uppnå fullständig täckning. De olika rollerna skulle vara Primär, Aktiv sekundär, Inaktiv sekundär och Okänd. Rollen Ingen behöver vanligtvis ingen särskild täckning eftersom Service Fabric anser att den här rollen är ogiltig eller null-tjänst. För det andra kommer varje nod att ändra sin roll vid en viss punkt. För att uppnå fullständig täckning bör kodkörningssökvägen testas med rolländringar.

## <a name="why-unit-test-stateful-services"></a>Varför enhet test stateful tjänster? 
Enhetstestning tillståndskänsliga tjänster kan hjälpa till att avslöja några vanliga misstag som görs som inte nödvändigtvis skulle fångas av konventionella program eller domänspecifika enhetstestning. Om den tillståndskänsliga tjänsten till exempel har något minnestillstånd kan den här typen av testning verifiera att det här minnestillståndet hålls synkroniserat mellan varje replik. Den här typen av testning kan också verifiera att en tillståndskänslig tjänst svarar på annulleringstoken som skickas in av Service Fabric-orkestrering på rätt sätt. När avbokningar utlöses bör tjänsten stoppa alla tidskrävande och/eller asynkrona åtgärder.  

## <a name="common-practices"></a>Vanliga metoder

I följande avsnitt får du råd om de vanligaste metoderna för enhetstestning av en tillståndskänslig tjänst. Det ger också råd om vad ett hånfullt lager bör ha att nära anpassa sig till Service Fabric orkestrering och statlig förvaltning. [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) från och med 3.3.0 eller senare är ett sådant bibliotek som tillhandahåller de nedsatta funktioner som rekommenderas och följer de metoder som beskrivs nedan.

### <a name="arrangement"></a>Arrangemang

#### <a name="use-multiple-service-instances"></a>Använda flera tjänstinstanser
Enhetstester bör köra flera instanser av en tillståndskänslig tjänst. Detta simulerar vad som faktiskt händer i klustret där Service Fabric etablerar flera repliker som kör tjänsten över olika noder. Var och en av dessa instanser kommer att köras under en annan kontext dock. När du kör testet bör varje instans förberedas med den rollkonfiguration som förväntas i klustret. Om tjänsten till exempel förväntas ha målreplikstorlek på 3, skulle Service Fabric etablera tre repliker på olika noder. En av dem är den primära och de andra två är Active Secondary's.

I de flesta fall varierar sökvägen för tjänstkörning något för var och en av dessa roller. Om tjänsten till exempel inte ska acceptera begäranden från en Aktiv sekundär, kan tjänsten ha en kontroll för det här ärendet för att återkalla ett informativt undantag som anger att en begäran har försökts på en sekundär. Om du har flera instanser kan den här situationen testas.

Dessutom, med flera instanser gör det möjligt för testerna att byta roller för var och en av dessa instanser för att kontrollera svaren är konsekventa trots rolländringarna.

#### <a name="mock-the-state-manager"></a>Håna tillståndshanteraren
Tillståndshanteraren bör behandlas som en fjärrresurs och därför hånas. När du hånar tillståndshanteraren måste det finnas en del underliggande minneslagring för att spåra vad som sparas till tillståndshanteraren så att det kan läsas och verifieras. Ett enkelt sätt att uppnå detta är att skapa falska instanser av var och en av de typer av tillförlitliga samlingar. Använd en datatyp som ligger nära de åtgärder som utförs mot den samlingen i dessa utkast. Följande är några föreslagna datatyper för varje tillförlitlig insamling

- IReliableDictionary<TKey, TValue> -> System.Collections.Concurrent.ConcurrentDictionary<TKey, TValue>
- IReliableQueue\<T> -> System.Collections.Generic.Queue\<T>
- IReliableConcurrentQueue\<T> -> System.Collections.Concurrent.ConcurrentQueue\<T>

#### <a name="many-state-manager-instances-single-storage"></a>Många state manager-instanser, en lagring
Som tidigare nämnts bör tillståndshanteraren och tillförlitliga samlingar behandlas som en fjärrresurs. Därför bör och kommer dessa resurser att hånas inom enhetstesterna. Men när du kör flera instanser av en tillståndskänslig tjänst blir det en utmaning att hålla varje hånad tillståndshanterare synkroniserad över olika tillståndskänsliga tjänstinstanser. När den tillståndskänsliga tjänsten körs i klustret tar servicestrukturen hand om att hålla varje sekundär repliks tillståndshanterare överens med den primära repliken. Därför bör testerna fungera på samma sätt så att de kan simulera rolländringar.

Ett enkelt sätt att uppnå denna synkronisering är att använda ett singleton-mönster för det underliggande objektet som lagrar data som skrivs till varje tillförlitlig samling. Om till exempel en tillståndskänslig `IReliableDictionary<string, string>`tjänst använder en . Den mock state manager bör `IReliableDictionary<string, string>`returnera ett hån mot . Att håna kan `ConcurrentDictionary<string, string>` använda en för att hålla reda på nyckeln / värdepar skrivna. Bör `ConcurrentDictionary<string, string>` vara en singleton som används av alla instanser av de statliga chefer som skickas till tjänsten.

#### <a name="keep-track-of-cancellation-tokens"></a>Håll reda på avbokningstoken
Annulleringstokens är en viktig men ofta förbisedd aspekt av tillståndskänsliga tjänster. När Service Fabric startar en primär replik för en tillståndskänslig tjänst tillhandahålls en annulleringstoken. Den här annulleringstoken är avsedd att signalera till tjänsten när den tas bort eller nedgraderas till en annan roll. Den tillståndskänsliga tjänsten bör stoppa alla tidskrävande eller asynkrona åtgärder så att Service Fabric kan slutföra arbetsflödet för rolländring.

När enhetstester körs bör alla annulleringstoken som tillhandahålls till RunAsync, ChangeRoleAsync, OpenAsync och CloseAsync hållas under testkörningen. Om du håller på dessa token kan testet simulera en tjänstavstängning eller degradering och verifiera att tjänsten svarar på rätt sätt.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Testa end-to-end med hånade fjärrresurser
Enhetstester bör köra så mycket av programkoden som kan ändra tillståndet för den tillståndskänsliga tjänsten som möjligt. Det rekommenderas att testerna vara mer end-to-end i naturen. De enda hån som finns är att spela in, simulera och/eller verifiera interaktioner med fjärrresurser. Detta inkluderar interaktioner med tillståndshanteraren och tillförlitliga samlingar. Följande utdrag är ett exempel på gherkin för ett test som visar end-to-end testning:

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

Det här testet bekräftar att de data som samlas in på en replik är tillgängliga för en sekundär replik när den befordras till primär. Förutsatt att en tillförlitlig samling är stödarkivet för medarbetardata, Aa potentiella fel som kan `CommitAsync` fångas med detta test är om programkoden inte körs på transaktionen för att spara den nya medarbetaren. I så fall skulle den andra begäran om att få medarbetare inte returnera medarbetare tillagd av den första begäran.

### <a name="acting"></a>Agerar
#### <a name="mimic-service-fabric-replica-orchestration"></a>Härma nde av replikorkestrering av Härma Service Fabric
När du hanterar flera tjänstinstanser bör testerna initiera och riva ned dessa tjänster på samma sätt som service fabric-orkestreringen. När en tjänst till exempel skapas på en ny primär replik anropar Service Fabric CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync och RunAsync. Livscykelhändelserna dokumenteras i följande artiklar:

- [Tillståndskänslig tjänststart](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Tillståndskänslig tjänstavstängning](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Primär swappar för tillståndskänslig tjänst](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Kör ändringar av replikrollen
Enhetstesterna bör ändra rollerna för tjänstinstanserna på samma sätt som servicetyg-orkestreringen. Rolltillståndsdatorn dokumenteras i följande artikel:

[Dator för replikrolltillstånd](service-fabric-concepts-replica-lifecycle.md#replica-role)

Simulera rolländringar är en av de mer kritiska aspekterna av testning och kan avslöja problem där replikens tillstånd inte är förenligt med varandra. Inkonsekvent repliktillstånd kan uppstå på grund av lagring av minnestillstånd i statiska instansvariabler eller instansvariabler på klassnivå. Exempel på detta kan vara annulleringstoken, uppräkning och konfigurationsobjekt/värden. Detta säkerställer också att tjänsten respekterar de annulleringstoken som tillhandahålls under RunAsync för att tillåta rolländringen att ske. Simulera rolländringar kan också avslöja problem som kan uppstå om koden inte skrivs för att tillåta en anrop av RunAsync flera gånger.

#### <a name="cancel-cancellation-tokens"></a>Avbryt avbokningstoken
Det bör finnas enhetstester där annulleringstoken som anges till RunAsync avbryts. Detta gör det möjligt för testet att kontrollera att tjänsten stängs av på ett smidigt sätt. Under denna avstängning bör alla tidskrävande eller asynkrona åtgärder stoppas. Exempel på en tidskrävande process som kan finnas på en tjänst är ett som lyssnar efter meddelanden i en tillförlitlig kö. Detta kan finnas direkt inom RunAsync eller en bakgrundstråd. Implementeringen bör innehålla logik för att avsluta åtgärden om den här annulleringstoken avbryts.

Om de tillståndskänsliga tjänsterna använder sig av cache- eller minnestillstånd som bara ska finnas på den primära, bör den bortskaffas just nu. Detta för att säkerställa att det här tillståndet är konsekvent om noden blir en primär igen senare. Annulleringstestning gör det möjligt för testet att kontrollera att detta tillstånd bortskaffas på rätt sätt.

#### <a name="execute-requests-against-multiple-replicas"></a>Köra begäranden mot flera repliker
Assert-tester bör köra samma begäran mot olika repliker. När du parkopplas med rolländringar kan konsekvensproblem upptäckas. Ett exempeltest kan utföra följande steg:
1. Köra en skrivbegäran mot den aktuella primära
2. Kör en läsbegäran som returnerar data som skrivits i steg 1 mot aktuell primär
3. Befordra en sekundär till primär. Detta bör också nedgradera den nuvarande primära till sekundära
4. Kör samma läsbegäran från steg 2 mot den nya sekundära.

I det sista steget kan testet hävda att de data som returneras är konsekventa. Ett potentiellt problem som detta kan avslöja är att de data som returneras av tjänsten kan vara i minnet men backas upp i slutändan av en tillförlitlig samling. Att minnesdata kanske inte synkroniseras korrekt med vad som finns i den tillförlitliga samlingen.

Minnesdata används vanligtvis för att skapa sekundära index eller aggregeringar av data som finns i en tillförlitlig samling.

### <a name="asserting"></a>Hävda
#### <a name="ensure-responses-match-across-replicas"></a>Se till att svaren matchar över repliker
Enhetstester bör hävda att ett svar för en viss begäran är konsekvent över flera repliker när de övergår till primär. Detta kan skapa potentiella problem där data som tillhandahålls i svaret antingen inte backas upp av en tillförlitlig samling eller hålls i minnet utan en mekanism för att synkronisera dessa data över repliker. Detta säkerställer att tjänsten skickar tillbaka konsekventa svar efter att Service Fabric balanserar om eller växlar över till en ny primär replik.

#### <a name="verify-service-respects-cancellation"></a>Verifiera att tjänsten respekterar annullering
Tidskrävande eller asynkrona processer som ska avslutas när en annulleringstoken avbryts bör verifieras att de faktiskt avslutas efter annullering. Detta säkerställer att processer som inte är avsedda att fortsätta köras på icke-primära replikstopp innan övergången är klar, trots att repliken byter roller. Detta kan också avslöja problem där en sådan process blockerar en rolländrings- eller avstängningsbegäran från Service Fabric från att slutföras.

#### <a name="verify-which-replicas-should-serve-requests"></a>Kontrollera vilka repliker som ska betjäna begäranden
Testerna bör bekräfta det förväntade beteendet om en begäran dirigeras till en icke-primär replik. Service Fabric ger möjlighet att ha sekundära repliker servering begäranden. Skrivningar till tillförlitliga samlingar kan dock bara förekomma från den primära repliken. Om ditt program avser att endast primära repliker ska kunna hantera begäranden eller, kan endast en delmängd av begäranden hanteras av en sekundär, bör testerna hävda det förväntade beteendet för både positiva och negativa fall. Det negativa fallet som är en begäran dirigeras till en replik som inte ska hantera begäran och, det positiva är motsatsen.

## <a name="next-steps"></a>Nästa steg
Lär dig hur [du enhet test stateful tjänster](service-fabric-how-to-unit-test-stateful-services.md).
