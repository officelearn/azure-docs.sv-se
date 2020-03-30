---
title: Skala beräkningsnoder automatiskt i en Azure Batch-pool | Microsoft-dokument
description: Aktivera automatisk skalning på en molnpool för att dynamiskt justera antalet beräkningsnoder i poolen.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 10/24/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017,fasttrack-edit
ms.openlocfilehash: 398b6d9c3fc05a6cf164b4003f57b94ecd6c1972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054019"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Skapa en automatisk formel för skalning av beräkningsnoder i en batchpool

Azure Batch kan automatiskt skala pooler baserat på parametrar som du definierar. Med automatisk skalning lägger Batch dynamiskt till noder i en pool när aktivitetskraven ökar och tar bort beräkningsnoder när de minskar. Du kan spara både tid och pengar genom att automatiskt justera antalet beräkningsnoder som används av batch-programmet.

Du aktiverar automatisk skalning på en pool med beräkningsnoder genom att associera med den en *formel för automatisk skalning* som du definierar. Batch-tjänsten använder formeln för automatisk skalning för att bestämma antalet beräkningsnoder som behövs för att köra din arbetsbelastning. Beräkningsnoder kan vara dedikerade noder eller [noder med låg prioritet](batch-low-pri-vms.md). Batch svarar på tjänstmåttdata som samlas in med jämna mellanrum. Med hjälp av dessa måttdata justerar Batch antalet beräkningsnoder i poolen baserat på formeln och med ett konfigurerbart intervall.

Du kan aktivera automatisk skalning antingen när en pool skapas eller på en befintlig pool. Du kan också ändra en befintlig formel på en pool som är konfigurerad för automatisk skalning. Med batch kan du utvärdera formler innan du tilldelar dem till pooler och övervaka statusen för automatisk skalningskörningar.

I den här artikeln beskrivs de olika entiteter som utgör dina formler för automatisk skalning, inklusive variabler, operatorer, åtgärder och funktioner. Vi diskuterar hur du hämtar olika beräkningsresurs- och aktivitetsmått i Batch. Du kan använda dessa mått för att justera antalet nodnoder i poolen baserat på resursanvändning och aktivitetsstatus. Vi beskriver sedan hur du skapar en formel och aktiverar automatisk skalning på en pool med hjälp av både batch-REST- och .NET-API:er. Slutligen avslutar vi med några exempel formler.

> [!IMPORTANT]
> När du skapar ett batchkonto kan du ange [kontokonfigurationen](batch-api-basics.md#account), som avgör om pooler allokeras i en batchtjänstprenumeration (standard) eller i din användarprenumeration. Om du har skapat ditt Batch-konto med standardkonfigurationen för Batch-tjänsten är ditt konto begränsat till ett maximalt antal kärnor som kan användas för bearbetning. Batch-tjänsten skalar beräkningsnoder endast upp till den kärngränsen. Därför kanske batch-tjänsten inte når det målantal beräkningsnoder som anges av en formel för automatisk skalning. Se [Kvoter och begränsningar för Azure Batch-tjänsten](batch-quota-limit.md) för information om hur du visar och ökar dina kontokvoter.
>
>Om du har skapat ditt konto med konfigurationen Användarprenumeration delar ditt konto in kärnkvoten för prenumerationen. Mer information finns i [Virtual Machines limits](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) (Gränser för virtuella datorer) i [Azure subscription and service limits, quotas, and constraints](../azure-resource-manager/management/azure-subscription-service-limits.md) (Prenumerations- och tjänstgränser, kvoter och begränsningar i Azure).
>
>

## <a name="automatic-scaling-formulas"></a>Automatisk skalning formler

En automatisk skalningsformel är ett strängvärde som du definierar som innehåller en eller flera uttryck. Formeln för automatisk skalning tilldelas till en pools [element för automatisk skalningsform][rest_autoscaleformula] (Batch REST) eller [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] (Batch .NET). Batch-tjänsten använder formeln för att bestämma målantalet beräkningsnoder i poolen för nästa bearbetningsintervall. Formelsträngen får inte överstiga 8 KB, kan innehålla upp till 100 satser som är avgränsade med semikolon och kan innehålla radbrytningar och kommentarer.

Du kan tänka dig automatisk skalning formler som en Batch automatisk skalning "språk." Formelsatser är friformade uttryck som kan innehålla både tjänstedefinierade variabler (variabler som definieras av batchtjänsten) och användardefinierade variabler (variabler som du definierar). De kan utföra olika åtgärder på dessa värden med hjälp av inbyggda typer, operatorer och funktioner. Ett utdrag kan till exempel ha följande format:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formler innehåller vanligtvis flera satser som utför åtgärder på värden som erhålls i tidigare utdrag. Till exempel, först får `variable1`vi ett värde för , `variable2`sedan skicka den till en funktion för att fylla:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Inkludera dessa satser i formeln för automatisk skalning för att komma fram till ett målantal beräkningsnoder. Dedikerade noder och noder med låg prioritet har var och en sina egna målinställningar, så att du kan definiera ett mål för varje typ av nod. En formel för automatisk skalning kan innehålla ett målvärde för dedikerade noder, ett målvärde för noder med låg prioritet eller båda.

Målantalet noder kan vara högre, lägre eller samma som det aktuella antalet noder av den typen i poolen. Batch utvärderar en pools formel för automatisk skalning med ett visst intervall (se [automatiska skalningsintervall).](#automatic-scaling-interval) Batch justerar målnumret för varje typ av nod i poolen till det nummer som formeln för automatisk skalning anger vid tidpunkten för utvärderingen.

### <a name="sample-autoscale-formulas"></a>Exempel på formler för automatisk skalning

Nedan finns exempel på två formler för automatisk skalning, som kan justeras för att fungera för de flesta scenarier. Variablerna `startingNumberOfVMs` och `maxNumberofVMs` i exempelformlerna kan anpassas till dina behov.

#### <a name="pending-tasks"></a>Väntande aktiviteter

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

Med den här formeln för automatisk skalning skapas poolen från början med en enda virtuell dator. Måttet `$PendingTasks` definierar antalet uppgifter som körs eller står i kö. Formeln hittar det genomsnittliga antalet väntande aktiviteter under de senaste 180 sekunderna och anger variabeln `$TargetDedicatedNodes` därefter. Formeln säkerställer att målantalet dedikerade noder aldrig överstiger 25 virtuella datorer. När nya uppgifter skickas växer poolen automatiskt. När aktiviteterna är slutförda blir virtuella datorer kostnadsfria en efter en och den automatiska skalningsformeln krymper poolen.

Den här formeln skalar dedikerade noder, men kan ändras så att den även gäller för skalning av noder med låg prioritet.

#### <a name="preempted-nodes"></a>Föregripna noder 

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

I det här exemplet skapas en pool som börjar med 25 noder med låg prioritet. Varje gång en nod med låg prioritet föregås av en dedikerad nod. Precis som med det `maxNumberofVMs` första exemplet förhindrar variabeln poolen från att överskrida 25 virtuella datorer. Det här exemplet är användbart för att dra nytta av virtuella datorer med låg prioritet samtidigt som du ser till att endast ett fast antal förebyggande åtgärder kommer att inträffa under poolens livstid.

## <a name="variables"></a>Variabler

Du kan använda både **tjänstdefinierade** och **användardefinierade** variabler i formler för automatisk skalning. De tjänstdefinierade variablerna är inbyggda i batch-tjänsten. Vissa tjänstdefinierade variabler är läs-och skriv-skrivningar och vissa är skrivskyddade. Användardefinierade variabler är variabler som du definierar. I exempelformeln som visas `$TargetDedicatedNodes` i `$PendingTasks` föregående avsnitt och är tjänstdefinierade variabler. variabler `startingNumberOfVMs` `maxNumberofVMs` och är användardefinierade variabler.

> [!NOTE]
> Tjänstdefinierade variabler föregås alltid av ett dollartecken ($). För användardefinierade variabler är dollartecknet valfritt.
>
>

I följande tabeller visas både skriv-och skrivskyddade variabler som definieras av batch-tjänsten.

Du kan hämta och ange värden för dessa tjänstdefinierade variabler för att hantera antalet beräkningsnoder i en pool:

| Läs-skrivtjänstdefinierade variabler | Beskrivning |
| --- | --- |
| $TargetDedicatedNodes |Målantalet dedikerade beräkningsnoder för poolen. Antalet dedikerade noder anges som ett mål eftersom en pool kanske inte alltid uppnår önskat antal noder. Om till exempel målantalet dedikerade noder ändras av en utvärdering av automatisk skalning innan poolen har nått det ursprungliga målet, kanske poolen inte når målet. <br /><br /> En pool i ett konto som skapats med batch-tjänstkonfigurationen kanske inte uppnår sitt mål om målet överskrider en batchkontonod eller kärnkvot. En pool i ett konto som skapats med användarprenumerationskonfigurationen kanske inte uppnår sitt mål om målet överskrider den delade kärnkvoten för prenumerationen.|
| $TargetLowPriorityNodes |Målantalet beräkningsnoder med låg prioritet för poolen. Antalet noder med låg prioritet anges som ett mål eftersom en pool kanske inte alltid uppnår önskat antal noder. Om till exempel målantalet noder med låg prioritet ändras av en utvärdering av automatisk skalning innan poolen har nått det ursprungliga målet, kanske poolen inte når målet. En pool kanske inte heller uppnår sitt mål om målet överskrider en batchkontonod eller kärnkvot. <br /><br /> Mer information om beräkningsnoder med låg prioritet finns i [Använda virtuella datorer med låg prioritet med batch](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Åtgärden som inträffar när beräkningsnoder tas bort från en pool. Möjliga värden:<ul><li>**requeue**– Standardvärdet. Avslutar aktiviteter omedelbart och placerar dem tillbaka i jobbkön så att de schemaläggs om. Den här åtgärden säkerställer att målantalet noder når så snabbt som möjligt, men kan vara mindre effektivt, eftersom alla pågående uppgifter avbryts och måste startas om, vilket slösar bort allt arbete de redan hade gjort. <li>**avsluta**--Avslutar aktiviteter omedelbart och tar bort dem från jobbkön.<li>**taskcompletion**--Waits for currently running tasks to finish and then removes the node from the pool. Använd det här alternativet om du vill undvika att aktiviteter avbryts och köas på ett sätt, vilket slösar bort allt arbete som aktiviteten har utfört. <li>**retaineddata**--Waits for all local task-retained data on the node to be cleaned up before removing the node from the pool.</ul> |

> [!NOTE]
> Variabeln `$TargetDedicatedNodes` kan också anges med `$TargetDedicated`hjälp av alias . På samma `$TargetLowPriorityNodes` sätt kan variabeln anges `$TargetLowPriority`med hjälp av alias . Om både den fullständigt namngivna variabeln och dess alias anges av formeln, har värdet som tilldelats den fullständigt namngivna variabeln företräde.
>
>

Du kan hämta värdet för dessa tjänstdefinierade variabler för att göra justeringar som baseras på mått från batch-tjänsten:

| Skrivskyddade tjänstdefinierade variabler | Beskrivning |
| --- | --- |
| $CPUPercent |Den genomsnittliga procentandelen CPU-användning. |
| $WallClockSeconds |Antalet sekunder som förbrukas. |
| $MemoryBytes |Genomsnittligt antal megabyte som används. |
| $DiskBytes |Genomsnittligt antal gigabyte som används på de lokala diskarna. |
| $DiskReadBytes |Antalet lästa byte. |
| $DiskWriteBytes |Antalet skrivna byte. |
| $DiskReadOps |Antalet utförda lästa diskåtgärder. |
| $DiskWriteOps |Antalet skrivdiskåtgärder som utförs. |
| $NetworkInBytes |Antalet inkommande byte. |
| $NetworkOutBytes |Antalet utgående byte. |
| $SampleNodeCount |Antalet beräkningsnoder. |
| $ActiveTasks |Antalet aktiviteter som är klara att köras men som ännu inte körs. Antalet $ActiveTasks omfattar alla aktiviteter som är i aktivt tillstånd och vars beroenden har uppfyllts. Alla aktiviteter som är i aktivt tillstånd men vars beroenden inte har uppfyllts utesluts från $ActiveTasks antalet. För en aktivitet med flera instanser kommer $ActiveTasks att innehålla antalet instanser som angetts för aktiviteten.|
| $RunningTasks |Antalet aktiviteter i körläge. |
| $PendingTasks |Summan av $ActiveTasks och $RunningTasks. |
| $SucceededTasks |Antalet aktiviteter som har slutförts. |
| $FailedTasks |Antalet aktiviteter som misslyckades. |
| $CurrentDedicatedNodes |Det aktuella antalet dedikerade beräkningsnoder. |
| $CurrentLowPriorityNodes |Det aktuella antalet beräkningsnoder med låg prioritet, inklusive alla noder som har föregripits. |
| $PreemptedNodeCount | Antalet noder i poolen som är i ett förutbestämt tillstånd. |

> [!TIP]
> De skrivskyddade, tjänstdefinierade variabler som visas i föregående tabell är *objekt* som tillhandahåller olika metoder för att komma åt data som är associerade med var och en. Mer information finns i [Hämta exempeldata](#getsampledata) senare i den här artikeln.
>
>

## <a name="types"></a>Typer

Dessa typer stöds i en formel:

* double
* doubleVec (på ett sätt)
* doubleVecList (dubbelVecList)
* sträng
* tidsstämpel - tidsstämpel är en sammansatt struktur som innehåller följande medlemmar:

  * år
  * månad (1-12)
  * dag (1-31)
  * veckodag (i formatet nummer, till exempel 1 för måndag)
  * timme (i 24-timmars nummerformat, till exempel 13 betyder 1 PM)
  * minut (00-59)
  * andra (00-59)
* tidinval

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>Åtgärder

Dessa åtgärder tillåts på de typer som visas i föregående avsnitt.

| Åtgärd | Operatörer som stöds | Typ av resultat |
| --- | --- | --- |
| dubbel *operatör* dubbel |+, -, *, / |double |
| dubbel *operator* operatörstidinföring |* |tidinval |
| doubleVec *operatör* dubbel |+, -, *, / |doubleVec (på ett sätt) |
| doubleVec *operatör* doubleVec |+, -, *, / |doubleVec (på ett sätt) |
| *timeinterval-operator dubbel* |*, / |tidinval |
| timeinterval *operatör* tidinterval |+, - |tidinval |
| tidsfördämning *operatör* tidsstämpel |+ |timestamp |
| *tidsstämpeloperatörens* tidsförstämning |+ |timestamp |
| tidsstämpel *operatör* tidsstämpel |- |tidinval |
| *operatören*dubbel |-, ! |double |
| *operatörens*tidsförstövding |- |tidinval |
| dubbel *operatör* dubbel |<, <=, ==, >=, >, != |double |
| *strängoperatorsträng* |<, <=, ==, >=, >, != |double |
| tidsstämpel *operatör* tidsstämpel |<, <=, ==, >=, >, != |double |
| timeinterval *operatör* tidinterval |<, <=, ==, >=, >, != |double |
| dubbel *operatör* dubbel |&& &#124;&#124; |double |

När du testar en dubbel med`double ? statement1 : statement2`en ternära operator ( ), är nonzero **sant**och noll är **falskt**.

## <a name="functions"></a>Funktioner
Dessa fördefinierade **funktioner** är tillgängliga för dig att använda för att definiera en automatisk skalningsformel.

| Funktion | Returtyp | Beskrivning |
| --- | --- | --- |
| avg(doubleVecList) |double |Returnerar medelvärdet för alla värden i doubleVecList. |
| len(doubleVecList) |double |Returnerar längden på vektorn som skapas från doubleVecList. |
| lg(dubbel) |double |Returnerar loggbasen 2 i dubbel. |
| lg(doubleVecList) |doubleVec (på ett sätt) |Returnerar komponentvis logbas 2 i doubleVecList. En vec(double) måste uttryckligen skickas för parametern. Annars antas den dubbla lg(double)-versionen. |
| ln(dubbel) |double |Returnerar den naturliga loggen för dubbel. |
| ln(doubleVecList) |doubleVec (på ett sätt) |Returnerar den naturliga loggen för dubbel. |
| log(dubbel) |double |Returnerar loggbasen 10 av dubbeln. |
| log(doubleVecList) |doubleVec (på ett sätt) |Returnerar komponentvis logbas 10 i doubleVecList. En vec(double) måste uttryckligen skickas för den enkeldubbelparametern. Annars antas den dubbla log(double)-versionen. |
| max(doubleVecList) |double |Returnerar det maximala värdet i doubleVecList. |
| min(doubleVecList) |double |Returnerar minimivärdet i doubleVecList. |
| norm (doubleVecList) |double |Returnerar tvånormen för vektorn som skapas från doubleVecList. |
| percentil(doubleVec v, dubbel p) |double |Returnerar percentilelementet i vektorn v. |
| rand() |double |Returnerar ett slumpmässigt värde mellan 0,0 och 1,0. |
| intervall (doubleVecList) |double |Returnerar skillnaden mellan min- och maxvärdena i doubleVecList. |
| std(doubleVecList) |double |Returnerar exempelstandardavvikelsen för värdena i doubleVecList. |
| stopp() | |Stoppar utvärderingen av uttrycket för automatisk skalning. |
| summa(doubleVecList) |double |Returnerar summan av alla komponenter i doubleVecList. |
| tid(strängdatumTime="") |timestamp |Returnerar tidsstämpeln för den aktuella tiden om inga parametrar skickas, eller tidsstämpeln för dateTime-strängen om den skickas. DateTime-format som stöds är W3C-DTF och RFC 1123. |
| val (doubleVec v, dubbel i) |double |Returnerar värdet för det element som finns på plats i vektor v, med ett startindex på noll. |

Vissa av de funktioner som beskrivs i föregående tabell kan acceptera en lista som ett argument. Den kommaavgränsade listan är en kombination av *double* och *doubleVec*. Ett exempel:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*DoubleVecList-värdet* konverteras till en enda *doubleVec* före utvärdering. Om `v = [1,2,3]`till exempel anropar `avg(v)` det `avg(1,2,3)`är likvärdigt med att ringa . Att `avg(v, 7)` ringa är `avg(1,2,3,7)`likvärdigt med att ringa .

## <a name="obtain-sample-data"></a><a name="getsampledata"></a>Hämta exempeldata

Formler för automatisk skalning fungerar på måttdata (exempel) som tillhandahålls av batch-tjänsten. En formel växer eller krymper poolstorleken baserat på de värden som den hämtar från tjänsten. De tjänstdefinierade variabler som beskrivits tidigare är objekt som tillhandahåller olika metoder för åtkomst till data som är associerade med objektet. Följande uttryck visar till exempel en begäran om att få de sista fem minuterna av CPU-användning:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Metod | Beskrivning |
| --- | --- |
| GetSample() |Metoden `GetSample()` returnerar en vektor med dataexempel.<br/><br/>Ett exempel är 30 sekunder till ett värde av måttdata. Med andra ord erhålls prover var 30:e sekund. Men som nämnts nedan finns det en fördröjning mellan när ett prov samlas in och när det är tillgängligt för en formel. Därför kan inte alla prover för en viss tidsperiod vara tillgängliga för utvärdering med en formel.<ul><li>`doubleVec GetSample(double count)`<br/>Anger antalet exempel som ska hämtas från de senaste exemplen som samlades in.<br/><br/>`GetSample(1)`returnerar det senast tillgängliga provet. För mått `$CPUPercent`som bör detta dock inte användas eftersom det är omöjligt att veta *när* exemplet samlades in. Det kan vara nyligen, eller på grund av systemproblem, kan det vara mycket äldre. Det är bättre i sådana fall att använda ett tidsintervall som visas nedan.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Anger en tidsram för insamling av exempeldata. Alternativt anges också procentandelen av de exempel som måste vara tillgängliga inom den begärda tidsramen.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`skulle returnera 20 prover om alla prover för de senaste 10 minuterna finns i CPUPercent-historiken. Om den sista minuten av historien inte var tillgänglig, dock endast 18 prover skulle returneras. Om så är fallet:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`skulle misslyckas eftersom endast 90 procent av proverna är tillgängliga.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`skulle lyckas.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Anger en tidsram för insamling av data, med både starttid och sluttid.<br/><br/>Som nämnts ovan finns det en fördröjning mellan när ett prov samlas in och när det är tillgängligt för en formel. Tänk på den här `GetSample` fördröjningen när du använder metoden. Se `GetSamplePercent` nedan. |
| GetSamplePeriod() |Returnerar den period av exempel som togs i en historisk exempeldatauppsättning. |
| Antal() |Returnerar det totala antalet exempel i måtthistoriken. |
| HistoriaBeginTime() |Returnerar tidsstämpeln för det äldsta tillgängliga dataexemplet för måttet. |
| GetSamplePercent() |Returnerar procentandelen exempel som är tillgängliga för ett visst tidsintervall. Ett exempel:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Eftersom `GetSample` metoden misslyckas om procentandelen returnerade `samplePercent` exempel är mindre än `GetSamplePercent` den angivna, kan du använda metoden för att kontrollera först. Sedan kan du utföra en alternativ åtgärd om det inte finns tillräckliga exempel, utan att stoppa den automatiska skalningsutvärderingen. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Exempel, exempelprocent och metoden *GetSample()*
Kärnåtgärden för en formel för automatisk skalning är att hämta aktivitets- och resursmåttdata och sedan justera poolstorleken baserat på dessa data. Därför är det viktigt att ha en tydlig förståelse för hur formler för automatisk skalning interagerar med måttdata (exempel).

**Prover**

Batch-tjänsten tar regelbundet exempel på aktivitets- och resursmått och gör dem tillgängliga för dina formler för automatisk skalning. Dessa prover registreras var 30:e sekund av batch-tjänsten. Det finns dock vanligtvis en fördröjning mellan när dessa prover spelades in och när de görs tillgängliga för (och kan läsas av) dina formler för automatisk skalning. På grund av olika faktorer, till exempel nätverksproblem eller andra infrastrukturproblem, kan det hända att prover inte registreras för ett visst intervall.

**Exempelprocent**

När `samplePercent` skickas till `GetSample()` metoden `GetSamplePercent()` eller metoden anropas, avser _procent_ en jämförelse mellan det totala antalet exempel som registreras av batch-tjänsten och antalet exempel som är tillgängliga för formeln för automatisk skalning.

Låt oss titta på en 10-minuters tidsrymd som ett exempel. Eftersom prover registreras var 30:e sekund inom en 10-minuters tidsperiod, skulle det maximala totala antalet prover som registreras av Batch vara 20 samplingar (2 per minut). På grund av den inneboende svarstiden för rapporteringsmekanismen och andra problem i Azure kan det dock bara finnas 15 exempel som är tillgängliga för din formel för automatisk skalning för läsning. Så, till exempel, för den 10-minutersperioden, endast 75% av det totala antalet prover som registrerats kan vara tillgängliga för din formel.

**GetSample() och provintervall**

Dina formler för automatisk skalning kommer att &mdash; växa och krympa dina pooler lägga till noder eller ta bort noder. Eftersom noder kostar pengar vill du se till att formlerna använder en intelligent analysmetod som baseras på tillräckliga data. Därför rekommenderar vi att du använder en trendanalys i formler. Den här typen växer och krymper dina pooler baserat på en rad insamlade prover.

För att göra `GetSample(interval look-back start, interval look-back end)` det, använd för att returnera en vektor av prover:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

När ovanstående rad utvärderas av Batch returneras ett urvalsområde som en vektor av värden. Ett exempel:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

När du har samlat vektorn för exempel kan `min()` `max()`du `avg()` sedan använda funktioner som , och härleda meningsfulla värden från det insamlade området.

Om du vill ha ytterligare säkerhet kan du tvinga en formelutvärdering att misslyckas om mindre än en viss samplingsprocent är tillgänglig för en viss tidsperiod. När du tvingar en formelutvärdering att misslyckas instruerar du Batch att upphöra med ytterligare utvärdering av formeln om den angivna procentandelen av samplingarna inte är tillgängliga. I det här fallet görs ingen ändring av poolens storlek. Om du vill ange en obligatorisk procentandel av exemplen för `GetSample()`utvärderingen som ska lyckas anger du den som den tredje parametern till . Här anges ett krav på 75 procent av proverna:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Eftersom exempeltillgängligheten kan uppstå är det viktigt att alltid ange ett tidsintervall med en starttid som är äldre än en minut. Det tar ungefär en minut för prover att sprida sig `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` genom systemet, så prover i intervallet kanske inte är tillgängliga. Återigen kan du använda parametern procent för `GetSample()` att tvinga fram ett visst exempelprocentkrav.

> [!IMPORTANT]
> Vi **rekommenderar starkt** att du undviker att ** *bara* förlita dig på `GetSample(1)` formler för automatisk skalning**. Detta beror `GetSample(1)` i huvudsak säger till Batch-tjänsten, "Ge mig det sista provet du har, oavsett hur länge sedan du hämtade den." Eftersom det bara är ett enda exempel, och det kan vara ett äldre exempel, kanske det inte är representativt för den större bilden av den senaste aktiviteten eller resurstillståndet. Om du `GetSample(1)`använder kontrollerar du att den är en del av en större sats och inte den enda datapunkt som formeln är beroende av.
>
>

## <a name="metrics"></a>Mått

Du kan använda både resurs- och aktivitetsmått när du definierar en formel. Du justerar målantalet dedikerade noder i poolen baserat på måttdata som du hämtar och utvärderar. Se avsnittet [Variabler](#variables) ovan för mer information om varje mått.

<table>
  <tr>
    <th>Mått</th>
    <th>Beskrivning</th>
  </tr>
  <tr>
    <td><b>Resurs</b></td>
    <td><p>Resursmått baseras på processorn, bandbredden, minnesanvändningen för beräkningsnoder och antalet noder.</p>
        <p> Dessa tjänstdefinierade variabler är användbara för att göra justeringar baserat på antal nod:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Dessa tjänstdefinierade variabler är användbara för att göra justeringar baserat på nodresursanvändning:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Aktivitet</b></td>
    <td><p>Aktivitetsmått baseras på status för aktiviteter, till exempel Aktiv, Väntande och Slutförd. Följande tjänstdefinierade variabler är användbara för att göra justeringar av poolstorlek baserat på aktivitetsmått:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Skriva en formel för automatisk skalning

Du skapar en formel för automatisk skalning genom att bilda satser som använder ovanstående komponenter och kombinerar sedan dessa satser till en komplett formel. I det här avsnittet skapar vi ett exempel på formel för automatisk skalning som kan utföra vissa verkliga skalningsbeslut.

Låt oss först definiera kraven för vår nya formel för automatisk skalning. Formeln bör:

1. Öka målantalet dedikerade beräkningsnoder i en pool om CPU-användningen är hög.
1. Minska antalet dedikerade beräkningsnoder i en pool när CPU-användningen är låg.
1. Begränsa alltid det maximala antalet dedikerade noder till 400.
1. När du minskar antalet noder ska du inte ta bort noder som kör aktiviteter. Om det behövs väntar du tills aktiviteterna är klara med att ta bort noder.

Om du vill öka antalet noder under hög CPU-användning definierar`$totalDedicatedNodes`du uttrycket som fyller en användardefinierad variabel ( ) med ett värde som är 110 procent av det aktuella målantalet dedikerade noder, men bara om den minsta genomsnittliga CPU-användningen under de senaste 10 minuterna var över 70 procent. Annars använder du värdet för det aktuella antalet dedikerade noder.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

För att *minska* antalet dedikerade noder under låg CPU-användning, `$totalDedicatedNodes` anger nästa sats i vår formel samma variabel till 90 procent av det aktuella målantalet dedikerade noder om den genomsnittliga CPU-användningen under de senaste 60 minuterna var under 20 procent. Annars använder du det `$totalDedicatedNodes` aktuella värdet för det som vi befolkade i uttalandet ovan.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Nu begränsa målantalet dedikerade beräkningsnoder till högst 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Här är den kompletta formeln:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Skapa en pool med automatisk skalning med Batch-SDK:er

Automatisk skalning av pool kan konfigureras med någon av [batch-SDK:erna,](batch-apis-tools.md#azure-accounts-for-batch-development) [powershell-cmdlets](batch-powershell-cmdlets-get-started.md) [för batch-REST-API-batchen](https://docs.microsoft.com/rest/api/batchservice/) och [batch-CLI](batch-cli-get-started.md). I det här avsnittet kan du se exempel för både .NET och Python.

### <a name="net"></a>.NET

Så här skapar du en pool med automatisk skalning aktiverad i .NET:

1. Skapa poolen med [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Ange egenskapen [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) till `true`.
1. Ange egenskapen [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) med formeln för automatisk skalning.
1. (Valfritt) Ange egenskapen [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (standard är 15 minuter).
1. Genomför poolen med [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) eller [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Följande kodavsnitt skapar en pool som är aktiverad automatiskt i .NET. Poolens formel för automatisk skalning anger målantalet dedikerade noder till 5 på måndagar och 1 på varannan dag i veckan. Det [automatiska skalningsintervallet](#automatic-scaling-interval) är inställt på 30 minuter. I detta och de andra C# utdrag `myBatchClient` i den här artikeln, är en korrekt initierad instans av [klassen BatchClient.][net_batchclient]

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> När du skapar en pool som är aktiverad automatiskt ska du inte ange parametern _targetDedicatedNodes_ eller parametern _targetLowPriorityNodes_ i anropet till **CreatePool**. Ange i stället egenskaperna **Autoskalningsbar** och **AutoscaleFormula** i poolen. Värdena för dessa egenskaper bestämmer målnumret för varje typ av nod. Om du vill ändra storlek på en pool med automatisk skalning (till exempel med [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync]) **inaktiverar** du också automatisk skalning i poolen och ändrar sedan storlek på den.
>
>

#### <a name="automatic-scaling-interval"></a>Automatiskt skalningsintervall

Som standard justerar batch-tjänsten en pools storlek enligt formeln för automatisk skalning var 15:e minut. Det här intervallet kan konfigureras med hjälp av följande poolegenskaper:

* [CloudPool.AutoScaleevaluationInterval][net_cloudpool_autoscaleevalinterval] (batch .NET)
* [automatisk skalningInvärderingInterval][rest_autoscaleinterval] (REST API)

Det minsta intervallet är fem minuter och det maximala är 168 timmar. Om ett intervall utanför det här intervallet anges returnerar batch-tjänsten ett fel på en felaktig begäran (400).

> [!NOTE]
> Automatisk skalning är för närvarande inte avsett att svara på ändringar på mindre än en minut, utan är i stället avsett att justera storleken på poolen gradvis när du kör en arbetsbelastning.
>
>

### <a name="python"></a>Python

På samma sätt kan du skapa en pool med automatisk skalning med Python SDK genom att:

1. Skapa en pool och ange dess konfiguration.
1. Lägg till poolen i tjänstklienten.
1. Aktivera automatisk skalning på poolen med en formel som du skriver.

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18; 
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5; 
             $isWorkingWeekdayHour = $workHours && $isWeekday; 
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10), 
                                            pool_enable_auto_scale_options=None, 
                                            custom_headers=None, raw=False)
```

> [!TIP]
> Fler exempel på hur du använder Python SDK finns i [batch Python Quickstart-databasen](https://github.com/Azure-Samples/batch-python-quickstart) på GitHub.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Aktivera automatisk skalning på en befintlig pool

Varje Batch SDK är ett sätt att aktivera automatisk skalning. Ett exempel:

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (batch .NET)
* [Aktivera automatisk skalning på en pool][rest_enableautoscale] (REST API)

När du aktiverar automatisk skalning på en befintlig pool bör du tänka på följande:

* Om automatisk skalning för närvarande är inaktiverad i poolen när du utfärdar begäran om automatisk skalning måste du ange en giltig formel för automatisk skalning när du utfärdar begäran. Du kan också ange ett utvärderingsintervall för automatisk skalning. Om du inte anger något intervall används standardvärdet på 15 minuter.
* Om automatisk skalning är aktiverat i poolen kan du ange en formel för automatisk skalning, ett utvärderingsintervall eller båda. Du måste ange minst en av dessa egenskaper.

  * Om du anger ett nytt utvärderingsintervall för automatisk skalning stoppas det befintliga utvärderingsschemat och ett nytt schema startas. Det nya schemats starttid är den tidpunkt då begäran om automatisk skalning utfärdades.
  * Om du utelämnar formeln för automatisk skalning eller utvärderingsintervallet fortsätter batch-tjänsten att använda det aktuella värdet för den inställningen.

> [!NOTE]
> Om du har angett värden för *parametrarna targetDedicatedNodes* eller *targetLowPriorityNodes* för **metoden CreatePool** när du skapade poolen i .NET eller för jämförbara parametrar på ett annat språk, ignoreras dessa värden när den automatiska skalningsformeln utvärderas.
>
>

Det här C#-kodavsnittet använder [batch-NET-biblioteket][net_api] för att aktivera automatisk skalning på en befintlig pool:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Uppdatera en formel för automatisk skalning

Om du vill uppdatera formeln på en befintlig pool med automatisk skalning anropar du åtgärden för att aktivera automatisk skalning igen med den nya formeln. Om automatisk skalning till exempel redan `myexistingpool` är aktiverad när följande .NET-kod körs, ersätts `myNewFormula`dess formel för automatisk skalning med innehållet i .

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Uppdatera intervallet för automatisk skalning

Om du vill uppdatera utvärderingsintervallet för automatisk skalning för en befintlig pool med automatisk skalning anropar du åtgärden för att aktivera automatisk skalning igen med det nya intervallet. Om du till exempel vill ange utvärderingsintervallet för automatisk skalning till 60 minuter för en pool som redan är automatiskt skalbar aktiverad i .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Utvärdera en formel för automatisk skalning

Du kan utvärdera en formel innan du använder den på en pool. På så sätt kan du testa formeln för att se hur dess satser utvärderas innan du sätter formeln i produktion.

Om du vill utvärdera en formel för automatisk skalning måste du först aktivera automatisk skalning på poolen med en giltig formel. Om du vill testa en formel på en pool som ännu inte har `$TargetDedicatedNodes = 0` aktiverat automatisk skalning använder du formeln med en rad när du först aktiverar automatisk skalning. Använd sedan något av följande för att utvärdera formeln som du vill testa:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) eller [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Dessa batch .NET-metoder kräver ID för en befintlig pool och en sträng som innehåller formeln för automatisk skalning för att utvärdera.

* [Utvärdera en automatisk skalningsformel](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    I den här REST API-begäran anger du pool-ID:n i URI och formeln för automatisk skalning i elementet *autoScaleFormula* i begärandetexten. Svaret på åtgärden innehåller all felinformation som kan vara relaterad till formeln.

I det här [batchkodavsnittet för batch.NET][net_api] utvärderar vi en formel för automatisk skalning. Om poolen inte har automatisk skalning aktiverad aktiverar vi den först.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

En lyckad utvärdering av formeln som visas i det här kodavsnittet ger resultat som liknar:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Få information om automatisk skalningskörningar

För att säkerställa att formeln fungerar som förväntat rekommenderar vi att du regelbundet kontrollerar resultatet av de automatiska skalningskörningar som Batch utför på poolen. Om du vill göra det får du (eller uppdaterar) en referens till poolen och undersöker egenskaperna för den senaste körningen för automatisk skalning.

I Batch .NET har egenskapen [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) flera egenskaper som ger information om den senaste automatiska skalningskörningen som utförs på poolen:

* [AutoscaleRun.Tidsstämpel](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [Ta på automatiskt.Resultat](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoscaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

I REST API [returnerar hämta information om en poolbegäran](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) information om poolen, som innehåller den senaste automatiska skalningskörningsinformationen i egenskapen [autoScaleRun.](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool)

Följande C#-kodavsnitt använder batch.NET-biblioteket för att skriva ut information om den senaste automatisk skalning som kördes på pool _myPool:_

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Exempelutdata för föregående utdrag:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Exempel på formler för automatisk skalning

Låt oss titta på några formler som visar olika sätt att justera mängden beräkningsresurser i en pool.

### <a name="example-1-time-based-adjustment"></a>Exempel 1: Tidsbaserad justering

Anta att du vill justera poolstorleken baserat på veckodag och tid på dagen. Det här exemplet visar hur du ökar eller minskar antalet noder i poolen i enlighet med detta.

Formeln får först den aktuella tiden. Om det är en veckodag (1-5) och inom arbetstid (08.00 till 18.00) anges målpoolens storlek till 20 noder. Annars är den inställd på 10 noder.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```
`$curTime`kan justeras för att återspegla din `time()` lokala tidszon `TimeZoneInterval_Hour` genom att lägga till produkten av och din UTC-förskjutning. Använd till `$curTime = time() + (-6 * TimeInterval_Hour);` exempel för Mountain Daylight Time (MDT). Tänk på att förskjutningen måste justeras i början och slutet av sommartid (om tillämpligt).

### <a name="example-2-task-based-adjustment"></a>Exempel 2: Aktivitetsbaserad justering

I det här exemplet justeras poolstorleken baserat på antalet aktiviteter i kön. Både kommentarer och radbrytningar är acceptabla i formelsträngar.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exempel 3: Redovisning av parallella uppgifter

I det här exemplet justeras poolstorleken baserat på antalet aktiviteter. Den här formeln tar också hänsyn till [maxtaspercomputenode-värdet][net_maxtasks] som har ställts in för poolen. Den här metoden är användbar i situationer där [parallella aktivitetskörning](batch-parallel-node-tasks.md) har aktiverats i poolen.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Exempel 4: Ange en ursprunglig poolstorlek

I det här exemplet visas ett C#-kodavsnitt med en formel för automatisk skalning som anger poolstorleken till ett angivet antal noder för en inledande tidsperiod. Sedan justeras poolstorleken baserat på antalet pågående och aktiva aktiviteter efter att den inledande tidsperioden har förflutit.

Formeln i följande kodavsnitt:

* Ställer in den ursprungliga poolstorleken till fyra noder.
* Justerar inte poolstorleken inom de första 10 minuterna av poolens livscykel.
* Efter 10 minuter får du maxvärdet för antalet löp- och aktiva aktiviteter under de senaste 60 minuterna.
  * Om båda värdena är 0 (vilket anger att inga aktiviteter har körts eller varit aktiva under de senaste 60 minuterna) är poolstorleken inställd på 0.
  * Om något av värdena är större än noll görs ingen ändring.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Nästa steg

* [Maximera Azure Batch-beräkningsresursanvändning med samtidiga noduppgifter](batch-parallel-node-tasks.md) innehåller information om hur du kan köra flera uppgifter samtidigt på beräkningsnoderna i poolen. Förutom automatisk skalning kan den här funktionen bidra till att sänka jobbtiden för vissa arbetsbelastningar, vilket sparar pengar.
* För en annan effektivitetsbooster, se till att batch-programmet frågar batch-tjänsten på det mest optimala sättet. Se [Fråga Azure Batch-tjänsten effektivt](batch-efficient-list-queries.md) om du vill lära dig hur du begränsar mängden data som korsar kabeln när du frågar statusen för potentiellt tusentals beräkningsnoder eller uppgifter.

[net_api]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[net_batchclient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient
[net_cloudpool_autoscaleformula]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula
[net_cloudpool_autoscaleevalinterval]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval
[net_enableautoscaleasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync
[net_maxtasks]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode
[net_poolops_resizepoolasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync

[rest_api]: https://docs.microsoft.com/rest/api/batchservice/
[rest_autoscaleformula]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_autoscaleinterval]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_enableautoscale]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
