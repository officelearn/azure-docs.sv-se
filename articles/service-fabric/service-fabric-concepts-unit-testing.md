---
title: Enhetstestning tillståndskänsliga tjänster i Azure Service Fabric | Microsoft Docs
description: Läs mer om de begreppen och teknikerna för Enhetstestning tillståndskänslig Service Fabric-tjänster.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: atsenthi
ms.openlocfilehash: ca473b9947a9b0df610a9c3dac66914b06cc9217
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881461"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Enhetstestning tillståndskänsliga tjänster i Service Fabric

Den här artikeln beskriver de begreppen och teknikerna för Enhetstestning tillståndskänslig Service Fabric-tjänster. Enhetstestning i Service Fabric ska ha egna överväganden på att programkoden körs aktivt under flera olika kontexter. Den här artikeln beskrivs de metoder som används för att se till att programkoden är täcks av var och en av de olika kontexterna som den kan köras.

## <a name="unit-testing-and-mocking"></a>Enhet testning och simulerade
Enhetstestning i kontexten för den här artikeln är automatiserad testning som kan utföras inom ramen för en testaren, till exempel MSTest eller NUnit. Enhetstester i den här artikeln utför inte åtgärder mot en fjärransluten resurs, till exempel en databas eller en RESTFul-API. Vara bör mockade dessa fjärranslutna resurser. Simulerade i kontexten för den här artikeln falska, post, och styra för fjärranslutna resurser.

### <a name="service-fabric-considerations"></a>Service Fabric-överväganden
En tillståndskänslig Service Fabric-tjänst för Enhetstestning har flera saker. Det första körs kod som på flera noder, men i olika roller. Enhetstester bör utvärdera koden under varje roll för att få fullständig täckning. De olika rollerna är primär, aktiva sekundära, inaktiv sekundär och okänd. Ingen roll vanligtvis behöver inte några särskilda täckning som Service Fabric tar hänsyn till den här rollen är ogiltigt eller null-tjänsten. Gå sedan ändras varje nod dess roll vid en given tidpunkt. För att uppnå fullständig täckning bör koden körning sökväg testas med rollen ändringar äger rum.

## <a name="why-unit-test-stateful-services"></a>Varför enhetstestar tillståndskänsliga tjänster? 
Enhetstestning tillståndskänsliga tjänster kan bidra till att avslöja vanliga misstag som görs som inte skulle nödvändigtvis fångas upp av konventionella program eller domänspecifika Enhetstestning. Till exempel om den tillståndskänsliga tjänsten har några InMemory-tillstånd, kan den här typen av testning Kontrollera att det här tillståndet i minnet hålls synkroniserade över varje replik. Den här typen av testning kan också kontrollera att en tillståndskänslig tjänst svarar på annulleringstoken som skickas av Service Fabric-orchestration på rätt sätt. När avbokningar utlöses ska tjänsten stoppa länge körs och/eller asynkrona åtgärder.  

## <a name="common-practices"></a>Vanliga metoder

Följande avsnitt om de vanligaste metoderna för en tillståndskänslig tjänst för Enhetstestning. Även om vad ett simulerade lager måste ha för att justera nära till Service Fabric orchestration och tillståndshantering finns. Simulering bibliotek finns bibliotek som tillhandahåller den här funktionen. [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) från och med 3.3.0 eller senare är ett bibliotek som tillhandahåller simulerade funktionen rekommenderas och följer de metoder som beskrivs nedan.

### <a name="arrangement"></a>Placering

#### <a name="use-multiple-service-instances"></a>Använda flera instanser av tjänsten
Enhetstester bör köra flera instanser av en tillståndskänslig tjänst. Det här simulerar vad som faktiskt händer på klustret där Service Fabric tillhandahåller flera repliker som kör tjänsten på olika noder. Var och en av dessa instanser ska köra under en annan kontext men. När du kör testet bör en förbereder sig av varje instans med rollkonfigurationen som förväntat på klustret. Om tjänsten förväntas ha Målstorlek replik 3, skulle Service Fabric etablera tre repliker på olika noder. Varav som den primära servern och de andra två som aktiv sekundär.

I de flesta fall kan varierar tjänsten körningssökvägen något för var och en av dessa roller. Om tjänsten inte ska acceptera begäranden från en aktiv sekundär, kan tjänsten ha en kontroll för det här ärendet utlöser tillbaka ett informativa undantag som anger att en begäran gjordes på en sekundär. Att ha flera instanser kan den här situationen som ska testas.

Dessutom kan har flera instanser testerna för att växla rollerna för var och en av dessa instanser för att verifiera svar är konsekvent trots rollen ändringarna.

#### <a name="mock-the-state-manager"></a>Simulera tillstånd manager
State Manager ska behandlas som en fjärransluten resurs och därför mockade. När simulerade tillstånd manager, måste det finnas vissa underliggande InMemory-lagring för att spåra vad som sparas i hanteraren för tillstånd så att den kan läsas och verifierats. Ett enkelt sätt att uppnå detta är att skapa fingerad instanser för var och en av typerna av tillförlitliga samlingar. Använda en datatyp som stämmer överens med de åtgärder som utförs mot samlingen inom dessa mocks. Här följer några förslag datatyper för varje tillförlitlig samling

- IReliableDictionary<TKey, TValue> -> System.Collections.Concurrent.ConcurrentDictionary<TKey, TValue>
- IReliableQueue<T> -> System.Collections.Generic.Queue<T>
- IReliableConcurrentQueue<T> -> System.Collections.Concurrent.ConcurrentQueue<T>

#### <a name="many-state-manager-instances-single-storage"></a>Många tillstånd Manager instanser, enskild lagring
Som tidigare nämnts, ska State Manager och Reliable Collections behandlas som en fjärransluten resurs. Därför bör dessa resurser och kommer mockade inom enhetstesterna. När du kör flera instanser av en tillståndskänslig tjänst ska det vara en utmaning att synkronisera varje simulerat tillståndshanterare över olika tillståndskänslig tjänstinstanser. När den tillståndskänsliga tjänsten körs på klustret, hand Service Fabric tar om att hålla tillståndshanterare för varje sekundär replik konsekvent med den primära repliken. Därför bör testerna fungerar på samma sätt så att de kan simulera rollen ändringar.

Ett enkelt sätt som den här synkroniseringen kan uppnås, är att använda ett singleton-mönster för det underliggande objektet som lagrar data som skrivs till varje tillförlitlig samling. Exempel: om en tillståndskänslig tjänst använder ett `IReliableDictionary<string, string>`. Fingerad tillståndshanterare ska returnera ett utkast av `IReliableDictionary<string, string>`. Den utkast kan använda en `ConcurrentDictionary<string, string>` att hålla reda på nyckel/värde-par som skrivits. Den `ConcurrentDictionary<string, string>` ska en Singleton-instans som används av alla instanser av tillstånd chefer skickas till tjänsten.

#### <a name="keep-track-of-cancellation-tokens"></a>Håll koll på annulleringstoken
Annulleringstoken är ett viktigt ännu ofta förbises aspekt av tillståndskänsliga tjänster. När Service Fabric startar en primär replik för en tillståndskänslig tjänst tillhandahålls en annullering-token. Den här annullering token är avsedd att skicka en signal till tjänsten när den tas bort eller nedgraderas till en annan roll. Den tillståndskänsliga tjänsten ska avbrytas långvarigt eller asynkrona åtgärder så att Service Fabric kan slutföra arbetsflödet för ändring av rollen.

När du kör enheten testar alla annulleringstoken som angetts för RunAsync, bör ChangeRoleAsync och OpenAsync CloseAsync behållas när testet körs. Vägen till dessa token kan testa att simulera en avstängning av tjänst eller degradering och verifiera tjänsten svarar på rätt sätt.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Testa slutpunkt till slutpunkt med simulerat fjärranslutna resurser
Enhetstester ska utföras så mycket av den programkod som kan ändra tillståndet för den tillståndskänsliga tjänsten som möjligt. Du rekommenderas att testerna ska vara mer-slutpunkt till sin natur. De enda mocks som finns är att registrera, simulera och/eller verifiera fjärresursen interaktioner. Detta inkluderar interaktion med State Manager och tillförlitliga samlingar. Följande fragment är ett exempel på gherkin för ett test som visar slutpunkt till slutpunkt testning:

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

Det här testet kontrollerar att de data som inhämtas på en replik är tillgänglig för en sekundär replik när det befordras till primär. Om vi antar att en tillförlitlig samling är lagringsenheten för data som anställda, är Aa potentiella fel som kan fångas upp med det här testet om programkoden inte kördes `CommitAsync` för transaktionen att spara den nya medarbetaren. I så fall returneras inte andra förfrågan om att hämta anställda medarbetare har lagts till av den första begäran.

### <a name="acting"></a>Fungerar
#### <a name="mimic-service-fabric-replica-orchestration"></a>Efterlikna orchestration för Service Fabric-replik
När du hanterar flera instanser av tjänsten, ska testerna initiera och plocka ner dessa tjänster på samma sätt som för Service Fabric-samordning. Till exempel anropa Service Fabric CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync och RunAsync när en tjänst har skapats på en ny primär replik. Livscykelhändelser finns dokumenterade i följande artiklar:

- [Tillståndskänsliga tjänsten startades](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Avstängning av tillståndskänslig tjänst](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Tillståndskänslig tjänst primära växlingar](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Kör repliken rollen ändringar
Enhetstesterna bör ändra rollerna för tjänstinstanser på samma sätt som Service Fabric-orkestrering. Rollen tillståndsdator dokumenteras i följande artikel:

[Repliken rollen tillståndsdator](service-fabric-concepts-replica-lifecycle.md#replica-role)

Simulera rollen ändringar är en av de mer kritiska aspekterna för testning och kan avslöja problem där den replikens tillstånd inte stämmer överens med varandra. Inkonsekvent replik tillstånd kan inträffa på grund av att lagra InMemory-tillstånd i statiska eller klass instans av servicenivå variabler. Exempel på detta kan vara annulleringstoken, uppräkningar och objekt/konfigurationsvärden. Detta säkerställer också att tjänsten är respekterar annulleringstoken som angavs under RunAsync att tillåta rolländringen ska ske. Simulera rollen ändringar kan också avslöja problem som kan uppstå om koden inte är skriven så att en körning av RunAsync flera gånger.

#### <a name="cancel-cancellation-tokens"></a>Avbryt annulleringstoken
Det ska finnas enhetstester där annullering token som angavs i RunAsync har avbrutits. Detta gör att test för att verifiera att tjänsten gradvis stängs av. Under den här Stäng långvarigt eller asynkrona åtgärder ska stoppas. Exempel på en tidskrävande process som kan finnas på en tjänst är en som lyssnar efter meddelanden i en tillförlitlig kö. Det kan finnas samtidigt direkt i RunAsync eller en bakgrundstråd. Implementeringen bör innehålla logik för att avsluta åtgärden om denna token för uppsägningen av prenumerationen har avbrutits.

Om den tillståndskänsliga tjänster gör använder av alla cache eller InMemory-läge som endast ska finnas på primärt, bör tas bort just nu. Detta är att säkerställa att det här tillståndet är konsekvent om noden blir en primär igen senare. Annulleringen testning kan test för att verifiera det här tillståndet har tagits bort korrekt.

#### <a name="execute-requests-against-multiple-replicas"></a>Köra begäranden mot flera repliker
Assert test bör köra samma begäran mot olika repliken. Tillsammans med rollen ändringar, kan problem vara utan åtgärd. Ett exempel test kan utföra följande steg:
1. Köra en skrivbegäran mot den aktuella primärt
2. Köra en läsbegäran som returnerar de data som skrivits i steg 1 mot aktuella primära
3. Uppgradera en sekundär till primär. Det bör också degradera den aktuella primärt till sekundära
4. Kör samma läsbegäran från steg 2 mot den nya sekundärt.

I det sista steget, kan testet assert de data som returneras är konsekvent. Ett potentiellt problem som detta kan avslöja är att data som returneras av tjänsten kan vara i minne men slutligen uppbackat av en tillförlitlig samling. Dessa data i minnet kan inte hållas synkroniserade med vad finns i samlingen tillförlitlig.

Minnesinterna data används vanligtvis för att skapa sekundärindex eller sammanställning av data som finns i en tillförlitlig samling.

### <a name="asserting"></a>Bevisar
#### <a name="ensure-responses-match-across-replicas"></a>Kontrollera svar matchar mellan repliker
Enhetstester bör assert att ett svar för en viss begäran är konsekvent över flera kopior när de övergång till primär. Detta kan ge sämre där data i svaret inte är antingen backas upp av en tillförlitlig samling eller i minnet utan en mekanism för att synkronisera data mellan repliker. Se till att tjänsten skickar tillbaka konsekvent svar när Service Fabric balanserar eller växlar över till en ny primär replik.

#### <a name="verify-service-respects-cancellation"></a>Kontrollera tjänsten värnar om annullering
Långvariga eller asynkron processer som ska avbrytas när en token för uppsägningen av prenumerationen har avbrutits bör verifieras att de faktiskt avslutas efter att prenumerationen avslutas. Det säkerställer att trots repliken byter roll, processer som inte är avsedda att hålla igång på icke-primär replik avbrytas innan övergången är klar. Detta kan också avslöja problem där sådan process blockerar begäran om ändring eller stänga av en roll från Service Fabric från att slutföras.

#### <a name="verify-which-replicas-should-serve-requests"></a>Kontrollera vilka repliker ska betjäna begäranden
Testerna bör assert förväntat beteende om en begäran dirigeras till en icke-primär replik. Service Fabric ger möjlighet att ha sekundära repliker betjäna begäranden. Skrivningar till tillförlitliga samlingar kan endast uppstå från den primära repliken. Om ditt program har för avsikt för endast primära repliker att hantera begäranden eller endast en delmängd av begäranden kan hanteras av en sekundär, bör testerna assert förväntat beteende för både positiva och negativa fall. Negativt om att en begäran dirigeras till en replik som inte ska hantera begäran och vilka positiva som motsatsen.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [enhet test tillståndskänsliga tjänster](service-fabric-how-to-unit-test-stateful-services.md).