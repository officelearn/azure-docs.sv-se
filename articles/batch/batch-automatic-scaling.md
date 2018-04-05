---
title: Automatiskt skala compute-noder i en Azure Batch-pool | Microsoft Docs
description: Aktivera automatisk skalning på poolen moln att dynamiskt justera antalet beräkningsnoder i poolen.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 06/20/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1114ea90ae6976a3bc3580ebae5fd853de0274a1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Skapa en automatisk skalning formel för att skala beräkningsnoder i en Batch-pool

Azure Batch skala automatiskt poolerna baserat på parametrarna som du definierar. Automatisk skalning Batch lägger till dynamiskt noder i en pool som aktiviteten krav och tar bort compute-noder som de minska. Du kan spara både tid och pengar genom att automatiskt justera antalet datornoderna används av Batch-program. 

Du aktiverar automatisk skalning på en pool av datornoderna genom att associera med en *Autoskala formeln* som du definierar. Batch-tjänsten använder Autoskala formel för att bestämma antalet compute-noder som behövs för att köra din arbetsbelastning. Compute-noder kan vara dedikerade noder eller [låg prioritet noder](batch-low-pri-vms.md). Batch svarar tjänsten mått data som samlas in regelbundet. Med den här mätvärdesdata kan justerar Batch antalet beräkningsnoder i poolen baserat på din formel och en konfigurerbar intervall.

Du kan aktivera automatisk skalning när poolen har skapats, eller på en befintlig adresspool. Du kan även ändra en befintlig formel på en pool som har konfigurerats för autoskalning. Batch gör det möjligt att utvärdera dina formler innan du tilldelar dem till pooler och övervaka statusen för automatisk skalning körs.

Den här artikeln beskrivs de olika enheter som utgör ditt Autoskala formler, inklusive variabler, operatorer, funktioner och funktioner. Diskuterar vi hur du skaffar olika beräknings-resurs- och mått i Batch. Du kan använda de här måtten för att justera antalet för din pool-noder baserat på resursen användnings- och status. Sedan beskrivs hur du skapar en formel och aktiverar automatisk skalning på en pool med hjälp av både .NET-API: erna och Batch REST. Slutligen vi med några formler.

> [!IMPORTANT]
> När du skapar ett Batch-konto kan du ange den [kontokonfigurationen](batch-api-basics.md#account), som bestämmer om pooler tilldelas i en prenumeration på tjänsten Batch (standard) eller i din prenumeration för användaren. Om du har skapat Batch-kontot med standardkonfiguration för Batch-tjänsten sedan är ditt konto begränsad till ett maximalt antal kärnor som kan användas för bearbetning. Batch-tjänsten skalas datornoderna bara upp till core gränsen. Därför nå Batch-tjänsten inte målet antalet compute-noder som anges av en Autoskala formel. Se [kvoter och gränser för Azure Batch-tjänsten](batch-quota-limit.md) information för att visa och öka kvoter för ditt konto.
>
>Om du har skapat ditt konto med användarens prenumeration konfigurationen delar ditt konto i kärnkvot för prenumerationen. Mer information finns i [Virtual Machines limits](../azure-subscription-service-limits.md#virtual-machines-limits) (Gränser för virtuella datorer) i [Azure subscription and service limits, quotas, and constraints](../azure-subscription-service-limits.md) (Prenumerations- och tjänstgränser, kvoter och begränsningar i Azure).
>
>

## <a name="automatic-scaling-formulas"></a>Automatisk skalning formler
En automatisk skalning formeln är ett strängvärde som du definierar som innehåller en eller flera instruktioner. Autoskala formeln är tilldelad till en pool [autoScaleFormula] [ rest_autoscaleformula] element (Batch REST) eller [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] Egenskapen (Batch .NET). Batch-tjänsten använder din formel för att fastställa målet antalet beräkningsnoder i poolen för nästa intervall för bearbetning. Formeln strängen får inte överskrida 8 KB, kan innehålla upp till 100 rapporter som är avgränsade med semikolon och kan innehålla radbrytningar och kommentarer.

Du kan se automatisk skalning formler som en Batch Autoskala ”språk”. Formeln satser är kostnadsfri utformad uttryck som kan innehålla både tjänsten användardefinierade variabler (variabler som definieras av Batch-tjänsten) och användardefinierade variabler (variabler som du definierar). De kan utföra olika åtgärder på dessa värden med hjälp av inbyggda typer, operatorer och funktioner. En instruktion kan till exempel ha följande format:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formler innehåller vanligtvis flera instruktioner som utför åtgärder på värden som hämtas i tidigare rapporter. Till exempel först vi hämta ett värde för `variable1`, överför den till en funktion för att fylla i `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Med de här uttrycken i din Autoskala formel för att få fram ett mål som antal compute-noder. Dedikerad noder och låg prioritet noder har sina egna Målinställningar så att du kan definiera ett mål för varje nod. En Autoskala formel kan innehålla ett målvärde för dedikerade noder, ett målvärde för låg prioritet noder eller båda.

Antalet noder som mål kan vara högre, lägre eller samma som det aktuella antalet noder av typen i poolen. Batch utvärderar en pool Autoskala formel med ett specifikt intervall (se [autoskalning intervall](#automatic-scaling-interval)). Batch justerar antalet mål för varje typ av noden i poolen till det tal som Autoskala formeln anger vid tiden för utvärderingen.

### <a name="sample-autoscale-formula"></a>Autoskala formeln

Här är ett exempel på en formel för Autoskala som kan justeras för att fungera för de flesta scenarier. Variablerna `startingNumberOfVMs` och `maxNumberofVMs` i exempel formeln kan justeras efter dina behov. Den här formeln skalas dedikerade noder, men kan ändras om du vill tillämpa skala låg prioritet noder. 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

Med den här formeln Autoskala skapas poolen med en enda virtuell dator. Den `$PendingTasks` mått definierar antalet aktiviteter som körs eller står i kö. Formeln söker efter det genomsnittliga antalet väntande uppgifter i de senaste 180 sekunder och anger den `$TargetDedicatedNodes` variabeln därefter. Formeln säkerställer att målet antalet dedicerade noder aldrig överstiger 25 virtuella datorer. När nya aktiviteter skickas växer poolen automatiskt. Som slutförda uppgifter, virtuella datorer blir ledigt i taget och autoskalning formeln krymper poolen.

## <a name="variables"></a>Variabler
Du kan använda båda **tjänst definierade** och **användardefinierade** variabler i Autoskala formlerna. Tjänst-definierade variabler är inbyggda i Batch-tjänsten. Några variabler som definieras av tjänsten är skrivskyddad och vissa är skrivskyddad. Användardefinierade variabler är variabler som du definierar. I exemplet formeln som visas i föregående avsnitt, `$TargetDedicatedNodes` och `$PendingTasks` är variabler som definieras av tjänsten. Variabler `startingNumberOfVMs` och `maxNumberofVMs` är användardefinierade variabler.

> [!NOTE]
> Tjänst-definierade variabler föregås alltid av ett dollartecken ($). För användardefinierade variabler är ett dollartecken valfritt.
>
>

Följande tabeller visar både läs-och skrivbara och skrivskyddade variabler som definieras av Batch-tjänsten.

Du kan hämta och ange värdena för variablerna tjänst definierade hantera antal compute-noder i en pool:

| Läsa / skriva-tjänst-definierade variabler | Beskrivning |
| --- | --- |
| $TargetDedicatedNodes |Mål antalet dedicerade compute-noder för poolen. Antalet dedicerade noder har angetts som mål eftersom en pool inte kan alltid kan uppnå önskat antal noder. Till exempel om mål antalet dedicerade noder har ändrats av en Autoskala utvärdering innan poolen har nått det ursprungliga målet kan sedan poolen inte nå målet. <br /><br /> En pool i ett konto som har skapats med konfigurationen av Batch-tjänsten kan inte uppnå målet om målet är längre än en nod eller core batchkonton. En pool i ett konto som har skapats med användarens prenumeration konfigurationen kan inte uppnå målet om målet överskrider kvoten delade kärnor för prenumerationen.|
| $TargetLowPriorityNodes |Mål för antalet låg prioritet compute-noder för poolen. Antalet noder med låg prioritet har angetts som mål eftersom en pool inte kan alltid kan uppnå önskat antal noder. Till exempel om mål antalet noder för låg prioritet ändras av en Autoskala utvärdering innan poolen har nått det ursprungliga målet, kan sedan poolen inte nå målet. En pool kan också inte uppnå målet om målet är längre än en nod eller core batchkonton. <br /><br /> Mer information på låg prioritet datornoder finns [använda VM med låg prioritet med Batch (förhandsgranskning)](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Vad som händer när datornoderna tas bort från poolen. Möjliga värden:<ul><li>**meddelanden**--avslutar aktiviteter omedelbart och placerar dem på jobbkön så att de schemaläggs.<li>**Avsluta**--avslutar aktiviteter omedelbart och tar bort dem från jobbkön.<li>**taskcompletion**--väntar för pågående aktiviteter för att avsluta och sedan tar bort noden från poolen.<li>**retaineddata**--väntar på att alla lokala uppgift behålls data på noden som ska rensas innan du tar bort noden från poolen.</ul> |

Du kan hämta värdet för dessa variabler som definieras av tjänsten för justeringar som baseras på mått för Batch-tjänsten:

| Skrivskyddade tjänst definierade variabler | Beskrivning |
| --- | --- |
| $CPUPercent |Den genomsnittliga procentandelen av CPU-användning. |
| $WallClockSeconds |Antal sekunder som används. |
| $MemoryBytes |Genomsnittligt antal megabyte som används. |
| $DiskBytes |Genomsnittligt antal gigabyte som används på de lokala diskarna. |
| $DiskReadBytes |Antal lästa byte. |
| $DiskWriteBytes |Antalet skrivna byte. |
| $DiskReadOps |Antal lästa diskåtgärder som utförs. |
| $DiskWriteOps |Antal skrivåtgärder på disken utförs. |
| $NetworkInBytes |Antal inkommande byte. |
| $NetworkOutBytes |Antal utgående byte. |
| $SampleNodeCount |Antal compute-noder. |
| $ActiveTasks |Antal aktiviteter som är redo att köra men ännu inte körs. Antalet $ActiveTasks innehåller alla aktiviteter som är aktiv och vars förutsättningar är uppfyllda. Alla uppgifter som är aktiv men vars beroenden är inte uppfyllda undantas från antalet $ActiveTasks.|
| $RunningTasks |Antal aktiviteter körs. |
| $PendingTasks |Summan av $ActiveTasks och $RunningTasks. |
| $SucceededTasks |Antal åtgärder som har slutförts. |
| $FailedTasks |Antal aktiviteter som har misslyckats. |
| $CurrentDedicatedNodes |Aktuellt antal dedikerade compute-noder. |
| $CurrentLowPriorityNodes |Det aktuella antalet låg prioritet compute-noder, inklusive alla noder som är försenat. |
| $PreemptedNodeCount | Antalet noder i poolen som är i tillståndet försenat. |

> [!TIP]
> Skrivskyddad, tjänst-definierade variabler som visas i föregående tabell är *objekt* som tillhandahåller olika metoder för att komma åt data som är associerade med varje. Mer information finns i [Hämta exempeldata](#getsampledata) senare i den här artikeln.
>
>

## <a name="types"></a>Typer
Dessa typer stöds i en formel:

* dubbla
* doubleVec
* doubleVecList
* sträng
* tidsstämpel--tidsstämpel är en sammansatt struktur som innehåller följande medlemmar:

  * år
  * månaden (1-12)
  * dag (1-31)
  * veckodag (i formatet för tal, till exempel 1 för måndag)
  * timme (i 24-timmarsformat format, till exempel 13 innebär 1 PM)
  * minuter (00-59)
  * andra (00-59)
* timeinterval

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
Dessa åtgärder är tillåtna för de typer som anges i föregående avsnitt.

| Åtgärd | Stöds operatorer | Typ av resultat |
| --- | --- | --- |
| dubbla *operatorn* dubbla |+, -, *, / |dubbla |
| dubbla *operatorn* timeinterval |* |timeinterval |
| doubleVec *operatorn* dubbla |+, -, *, / |doubleVec |
| doubleVec *operatorn* doubleVec |+, -, *, / |doubleVec |
| TimeInterval *operatorn* dubbla |*, / |timeinterval |
| TimeInterval *operatorn* timeinterval |+, - |timeinterval |
| TimeInterval *operatorn* tidsstämpel |+ |tidsstämpel |
| tidsstämpel *operatorn* timeinterval |+ |tidsstämpel |
| tidsstämpel *operatorn* tidsstämpel |- |timeinterval |
| *operatorn*dubbla |-, ! |dubbla |
| *operator*timeinterval |- |timeinterval |
| dubbla *operatorn* dubbla |<, <=, ==, >=, >, != |dubbla |
| strängen *operatorn* sträng |<, <=, ==, >=, >, != |dubbla |
| tidsstämpel *operatorn* tidsstämpel |<, <=, ==, >=, >, != |dubbla |
| TimeInterval *operatorn* timeinterval |<, <=, ==, >=, >, != |dubbla |
| dubbla *operatorn* dubbla |&&, &#124;&#124; |dubbla |

När du testar ett double med en ternär operator (`double ? statement1 : statement2`) inte är noll är **SANT**, och noll är **FALSKT**.

## <a name="functions"></a>Funktioner
De här fördefinierade **funktioner** är tillgängliga som du kan använda för att definiera en automatisk skalning formel.

| Funktion | Returtyp | Beskrivning |
| --- | --- | --- |
| avg(doubleVecList) |dubbla |Returnerar medelvärdet för alla värden i doubleVecList. |
| len(doubleVecList) |dubbla |Returnerar längden på vektorn som skapas från doubleVecList. |
| LG(Double) |dubbla |Returnerar loggen bas 2 i double. |
| lg(doubleVecList) |doubleVec |Returnerar component-wise loggen bas 2 i doubleVecList. En vec(double) måste uttryckligen skickades för parametern. I annat fall antas den dubbla lg(double)-versionen. |
| LN(Double) |dubbla |Returnerar dubbla naturlig log. |
| ln(doubleVecList) |doubleVec |Returnerar component-wise loggen bas 2 i doubleVecList. En vec(double) måste uttryckligen skickades för parametern. I annat fall antas den dubbla lg(double)-versionen. |
| log(Double) |dubbla |Returnerar loggen bas 10 av dubbla. |
| log(doubleVecList) |doubleVec |Returnerar component-wise loggen bas 10 av doubleVecList. En vec(double) måste uttryckligen skickades för den enda dubbla parametern. I annat fall antas den dubbla log(double)-versionen. |
| max(doubleVecList) |dubbla |Returnerar det största värdet i doubleVecList. |
| min(doubleVecList) |dubbla |Returnerar det minsta värdet i doubleVecList. |
| norm(doubleVecList) |dubbla |Returnerar två normen för vektorn som skapas från doubleVecList. |
| percentil (doubleVec v dubbla p) |dubbla |Returnerar elementet percentil för vektorn v. |
| rand() |dubbla |Returnerar ett slumpmässigt värde mellan 0,0 och 1,0. |
| range(doubleVecList) |dubbla |Returnerar skillnaden mellan lägsta och högsta värden i doubleVecList. |
| std(doubleVecList) |dubbla |Returnerar standardavvikelsen för urvalet av värdena i doubleVecList. |
| Stop) | |Stoppar utvärderingen av uttrycket för autoskalning. |
| sum(doubleVecList) |dubbla |Returnerar summan av alla komponenter i doubleVecList. |
| time(string dateTime="") |tidsstämpel |Returnerar tidsstämpeln för den aktuella tiden om inga parametrar skickas eller tidsstämpeln för dateTime-sträng om den skickas. Stöds dateTime-format är W3C DTF och RFC 1123. |
| val (doubleVec v dubbla i) |dubbla |Returnerar värdet för det element som är på plats i i vector v, med startIndex noll. |

Några av de funktioner som beskrivs i föregående tabell kan acceptera en lista som ett argument. Kommaavgränsad lista är en kombination av *dubbla* och *doubleVec*. Exempel:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Den *doubleVecList* värdet konverteras till en enda *doubleVec* för utvärdering. Till exempel om `v = [1,2,3]`, sedan anropa `avg(v)` motsvarar anropar `avg(1,2,3)`. Anropar `avg(v, 7)` motsvarar anropar `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Hämta exempeldata
Autoskala formler fungerar på mätvärdesdata (exempel) som tillhandahålls av Batch-tjänsten. En formel förstoras eller förminskas poolstorlek baserat på de värden som hämtas från tjänsten. Tjänst-definierade variablerna som beskrivs är tidigare objekt som ger olika metoder för att komma åt data som är associerade med objektet. Följande uttryck visar exempelvis en begäran om att hämta de senaste fem minuterna av CPU-användning:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Metod | Beskrivning |
| --- | --- |
| GetSample() |Den `GetSample()` metoden returnerar en vector av insamlade data.<br/><br/>Ett exempel är 30 sekunder värt mått data. Med andra ord hämtas exempel var 30: e sekund. Men som anges nedan, det finns en fördröjning mellan när ett exempel som samlas in och när den är tillgänglig för en formel. Därför kanske inte alla prover för en viss tidsperiod för utvärdering av en formel.<ul><li>`doubleVec GetSample(double count)`<br/>Anger antalet exempel som att hämta från de senaste samplingarna som samlades in.<br/><br/>`GetSample(1)` Returnerar den senaste tillgängliga insamlingen. För mått som `$CPUPercent`, men detta bör inte användas eftersom det är omöjligt att veta *när* provet samlades in. Det kan vara senaste eller på grund av problem med systemet, kan det vara mycket äldre. Det är bättre i sådana fall att använda ett tidsintervall som visas nedan.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Anger en tid för att samla in exempeldata. Du kan också anger den också procentandelen exempel som måste vara tillgängliga i den begärda tidsperioden.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` Returnerar 20 prov om alla prover för de senaste 10 minuterna finns i CPUPercent historiken. Om den senaste minuten tidigare inte var tillgänglig, men skulle endast 18 exempel returneras. I det här fallet:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` misslyckas eftersom endast 90 procent av exemplen är tillgängliga.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` fungerar.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Anger en tid för att samla in data med både en starttid och en sluttid.<br/><br/>Som nämnts ovan är det en fördröjning mellan när ett exempel som samlas in och när den är tillgänglig för en formel. Tänk fördröjningen när du använder den `GetSample` metoden. Se `GetSamplePercent` nedan. |
| GetSamplePeriod() |Returnerar antal prover som har tagits i historiska data exempeldata. |
| Count() |Returnerar det totala antalet prov i historiken för mått. |
| HistoryBeginTime() |Returnerar tidsstämpeln för den äldsta tillgängliga datasampel för måttet. |
| GetSamplePercent() |Returnerar procentandelen av prov som är tillgängliga för ett visst tidsintervall. Exempel:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Eftersom den `GetSample` metoden misslyckas om procentandelen exempel returneras är mindre än den `samplePercent` anges som du kan använda den `GetSamplePercent` metod för att kontrollera först. Du kan sedan utföra åtgärden alternativ om det finns inte tillräckligt prover, utan att stoppa automatisk skalning utvärderingen. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Exempel, exempel procent och *GetSample()* metod
Grundläggande drift av en Autoskala formel är att hämta aktivitets- och mätvärden och justera sedan poolstorlek baserat på dessa data. Därför är det viktigt att ha en förståelse för hur Autoskala formler interagera med mätvärdesdata (exempel).

**Exempel**

Batch-tjänsten med jämna mellanrum tar prover av aktivitets- och mått och gör dem tillgängliga i Autoskala formlerna. Exemplen är registrerade med 30 sekunders mellanrum av Batch-tjänsten. Det finns dock vanligtvis en fördröjning mellan när de exempel som har registrerats och när de görs tillgängliga för (och kan läsas av) Autoskala formler. På grund av olika faktorer, till exempel nätverks- eller andra problem infrastruktur kan dessutom prover inte registreras för ett visst intervall.

**Exempel procent**

När `samplePercent` har överförts till den `GetSample()` metod eller `GetSamplePercent()` metoden anropas _procent_ refererar till en jämförelse mellan det totala antalet prov som är registrerade av Batch-tjänsten och antalet exempel som är tillgängliga för din Autoskala formel.

Nu ska vi titta på timespan 10 minuters som exempel. Eftersom prover har registrerats med 30 sekunders mellanrum inom timespan 10 minuters, är det maximala totala antalet prov som registreras av Batch 20 prov (2 per minut). På grund av inbyggd svarstiden för mekanismen för rapportering och andra problem i Azure, kan det dock endast 15 prover som är tillgängliga för Autoskala formeln för läsning. Så, till exempel för den 10-minutersperioden endast 75% av det totala antalet prov som registrerats vara tillgängliga för din formel.

**Intervall för GetSample() och exempel**

Autoskala formlerna ska växande och minska storleken på din pooler &mdash; att lägga till noder eller ta bort noder. Eftersom noder kostar pengar, som du vill kontrollera att dina formler använder ett intelligent sätt att analys som baseras på uppgifter. Därför rekommenderar vi att du använder en trender typen analys i formler. Den här typen växer och krymper din poolerna baserat på ett intervall av insamlade prover.

Det gör du genom att använda `GetSample(interval look-back start, interval look-back end)` att returnera en vector prover:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

När ovanstående rad utvärderas av Batch returnerar en uppsättning exempel som en vektor med värden. Exempel:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

När du har lagrat vector prover, kan du använda funktioner som `min()`, `max()`, och `avg()` att härleda meningsfulla värden från insamlade intervallet.

För ytterligare säkerhet kan du tvinga en formel utvärdering misslyckas om mindre än en viss procentandel av exemplet är tillgänglig för en viss tidsperiod. Om du framtvingar en formel utvärdering misslyckas instruera Batch att upphöra ytterligare utvärdering av formeln om den angivna procentandelen av prover inte är tillgänglig. Ingen ändring görs i det här fallet poolstorleken. Om du vill ange nödvändiga procent av exempel för utvärdering ska lyckas, ange den som den tredje parametern `GetSample()`. Här är har ett krav för 75 procent av prover angetts:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Eftersom det kan finnas en fördröjning i exempel tillgänglighet, är det viktigt att alltid ange ett tidsintervall med föregående titt starttid som är äldre än en minut. Det tar ungefär en minut efter exempel spridas genom systemet, så exempel i intervallet `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` kanske inte tillgänglig. Igen och du kan använda parametern procentandel av `GetSample()` att tvinga ett visst exempel procentandel krav.

> [!IMPORTANT]
> Vi **rekommenderar** som du **förlita dig inte *endast* på `GetSample(1)` i Autoskala formlerna**. Detta beror på att `GetSample(1)` i stort sett säger till Batch-tjänsten ”ge mig senaste exempel du har, oavsett hur länge sedan den hämtades”. Eftersom det är bara ett exempel på en enda, och det kan vara ett äldre prov, kanske inte representativ för större bild av aktivitet eller resource tillstånd. Om du använder `GetSample(1)`, se till att det är en del av en större instruktion och inte bara datapunkten som är beroende av din formel.
>
>

## <a name="metrics"></a>Mått
Du kan använda både resurs- och mått när du definierar en formel. Du justera antalet dedicerade noder i poolen baserat på de mått som du vill hämta och utvärdera mål. Finns det [variabler](#variables) avsnittet ovan för mer information om varje mått.

<table>
  <tr>
    <th>Mått</th>
    <th>Beskrivning</th>
  </tr>
  <tr>
    <td><b>Resurs</b></td>
    <td><p>Resursen mått baseras på Processorn, bandbredd, minnesanvändningen för compute-noder och antalet noder.</p>
        <p> Dessa definieras av tjänsten variabler är användbara för att göra justeringar baserat på antalet noder:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$preemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Dessa definieras av tjänsten variabler är användbara för att göra justeringar baserat på noden Resursanvändning:</p>
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
    <td><p>Uppgiften mått baserat på status för aktiviteter, till exempel aktiv, väntar, och slutförs. Följande tjänst definierade variabler är användbara för att göra poolstorleken justeringar baserat på uppgiften mått:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Skriv en Autoskala formel
Du skapa en formel Autoskala genom instruktioner som använder komponenterna som ovan och sedan kombinera dessa instruktioner till en fullständig formel. I det här avsnittet skapar vi ett exempel Autoskala formeln som kan utföra vissa verkliga skalning beslut.

Först ska vi definiera krav för vår nya Autoskala formel. Formeln måste:

1. Öka antalet mål för dedikerade beräkningsnoder i poolen om CPU-användningen är hög.
2. Färre mål dedikerade beräkningsnoder i en pool när CPU-användningen är låg.
3. Begränsa alltid det maximala antalet noder dedikerade till 400.

Definiera den instruktion som fyller på en användardefinierad variabel om du vill öka antalet noder under hög CPU-användning (`$totalDedicatedNodes`) med ett värde som är 110 procent av målet antalet dedicerade noder, men bara om den minsta Genomsnittlig CPU-användningen under den senaste 10 minuterna översteg 70 procent. Annars använder du värdet för det aktuella antalet dedicerade noder.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Att *minska* antalet dedicerade noder under låg CPU-användning, nästa instruktionen i vår formel anger samma `$totalDedicatedNodes` variabeln och 90 procent av aktuellt rikta antalet dedicerade noder om Genomsnittlig CPU-användningen i förflutna 60 minuter var under 20 procent. Annars kan använda det aktuella värdet för `$totalDedicatedNodes` som vi har fyllts i instruktionen ovan.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Nu antalet mål dedikerade compute-noder till högst 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Här är klar formeln:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>Skapa en pool med Autoskala-aktiverade med .NET

Följ dessa steg om du vill skapa en pool med autoskalning aktiverat i .NET:

1. Skapa poolen med [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
2. Ange den [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) egenskapen `true`.
3. Ange den [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) egenskap med Autoskala-formel.
4. (Valfritt) Ange den [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) egenskapen (standardvärdet är 15 minuter).
5. Genomför pool med [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) eller [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Följande kodavsnitt skapar en pool med Autoskala-aktiverade i .NET. Poolens Autoskala formeln anger målet antalet noder som är dedikerad till 5 på måndagen och 1 för varje dag i veckan. Den [intervall för automatisk skalning](#automatic-scaling-interval) har angetts till 30 minuter. I det här och andra C# fragmenten i den här artikeln `myBatchClient` är en korrekt initierad instans av den [BatchClient] [ net_batchclient] klass.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "small", // single-core, 1.75 GB memory, 225 GB disk
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> När du skapar en pool med Autoskala-aktiverade inte anger den _targetDedicatedComputeNodes_ parametern eller _targetLowPriorityComputeNodes_ parameter i anropet till **CreatePool** . I stället anger den **AutoScaleEnabled** och **AutoScaleFormula** egenskaper för poolen. Värden för dessa egenskaper avgör i vilket mål för varje typ av noden. Dessutom att manuellt ändra storlek på en Autoskala-aktiverade pool (med exempelvis [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync]), första **inaktivera** automatisk skalning på den poolen och sedan ändrar den.
>
>

Förutom Batch .NET, kan du använda någon av de andra [Batch SDK](batch-apis-tools.md#azure-accounts-for-batch-development), [Batch REST](https://docs.microsoft.com/rest/api/batchservice/), [Batch PowerShell-cmdlets](batch-powershell-cmdlets-get-started.md), och [Batch CLI](batch-cli-get-started.md) till Konfigurera autoskalning.


### <a name="automatic-scaling-interval"></a>Intervall för automatisk skalning
Som standard justeras Batch-tjänsten poolstorlek enligt dess Autoskala formel var 15: e minut. Intervallet kan konfigureras med hjälp av följande egenskaper för programpoolen:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (REST-API)

Det minsta intervallet är fem minuter och högsta är 168 timmar. Om ett intervall som är utanför intervallet har angetts returnerar en felaktig begäran (400) fel i Batch-tjänsten.

> [!NOTE]
> Autoskalning är inte avsedd att svara på ändringar i mindre än en minut, men i stället är avsedd att justera storleken på din pool gradvis som du kör en arbetsbelastning.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Aktivera autoskalning på en befintlig adresspool

Varje Batch-SDK är ett sätt att aktivera autoskalning. Exempel:

* [BatchClient.PoolOperations.EnableAutoScaleAsync] [ net_enableautoscaleasync] (Batch .NET)
* [Aktivera automatisk skalning på poolen] [ rest_enableautoscale] (REST-API)

När du aktiverar autoskalning på en befintlig adresspool, Tänk på följande saker övervägas:

* Om automatisk skalning är inaktiverat på poolen när du skickar en begäran att aktivera autoskalning, måste du ange en giltig Autoskala formel när du skickar en begäran. Du kan också ange ett intervall för utvärdering av Autoskala. Om du inte anger ett intervall, används standardvärdet 15 minuter.
* Om Autoskala är aktiverat i poolen, kan du ange en formel Autoskala, ett intervall för utvärdering eller båda. Du måste ange minst en av dessa egenskaper.

  * Om du anger ett nytt Autoskala utvärdering intervall befintliga utvärderingsschema stoppas och startas ett nytt schema. Det nya schemat är start den tid då en begäran att aktivera autoskalning har utfärdats.
  * Om du utelämnar Autoskala formeln eller intervallet utvärdering, Batch-tjänsten fortsätter att använda det aktuella värdet för den här inställningen.

> [!NOTE]
> Om du har angett värden för den *targetDedicatedComputeNodes* eller *targetLowPriorityComputeNodes* parametrarna för den **CreatePool** metoden när du skapade poolen i .NET, eller jämförbar med parametrarna i ett annat språk sedan ignoreras när automatisk skalning formeln utvärderas.
>
>

Den här C#-kodstycke använder den [Batch .NET] [ net_api] biblioteket för att aktivera autoskalning på en befintlig adresspool:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Uppdatera en Autoskala formel

Om du vill uppdatera formeln på en befintlig Autoskala-aktiverade adresspool anropa åtgärden för att aktivera autoskalning igen med den nya formeln. Om exempelvis autoskalning är redan aktiverat på `myexistingpool` när följande .NET-kod körs dess Autoskala formel ersätts med innehållet i `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Uppdateringsintervall Autoskala

Om du vill uppdatera Autoskala-intervallet för utvärdering av en befintlig Autoskala-aktiverade pool anropa åtgärden för att aktivera autoskalning igen med ett nytt intervall. Till exempel ange intervallet för utvärdering Autoskala till 60 minuter för en pool som redan är aktiverat Autoskala i .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Utvärdera en Autoskala formel

Du kan utvärdera en formel innan den tillämpas på en pool. På så sätt kan testa du formel för att se hur rapporterna utvärdera innan du placerar formeln till produktionen.

Om du vill utvärdera en Autoskala formel, måste du först aktivera autoskalning i poolen med en giltig formel. Testa en formel i en pool som ännu inte har aktiverat autoskalning med en rad formeln `$TargetDedicatedNodes = 0` när du först aktivera autoskalning. Använd sedan någon av följande för att utvärdera formeln som du vill testa:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) eller [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Metoderna Batch .NET kräver ID för en befintlig adresspool och en sträng som innehåller Autoskala formeln att utvärdera.

* [Utvärdera en automatisk skalning formel](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Ange pool-ID i URI: N och Autoskala formeln i i denna REST API-begäran den *autoScaleFormula* element i begärandetexten. Svaret från åtgärden innehåller information om eventuella fel som kan vara relaterad till formeln.

I det här [Batch .NET] [ net_api] kodfragmentet vi utvärderar en Autoskala formel. Om poolen inte har aktiverats autoskalning, aktivera vi det först.

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
        autoscaleFormula: "$TargetDedicatedNodes = {pool.CurrentDedicatedNodes};",
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

Lyckad utvärdering av formeln som visas i det här kodstycket ger resultat som liknar:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Hämta information om Autoskala körs

För att säkerställa att din formel fungerar som förväntat, rekommenderar vi att du regelbundet kontrollera resultatet av autoskalning körs som Batch utför på din pool. Att göra så, hämta (eller uppdatera) en referens till poolen, och kontrollera egenskaperna för dess sista Autoskala kör.

I Batch .NET den [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) -egenskap har flera egenskaper som innehåller information om den senaste autoskalning kör utförs på poolen:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

I REST-API i [få information om poolen](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) begäran returnerar information om poolen, som innehåller den senaste autoskalning körs information den [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool#bk_autrun) egenskapen.

Följande C# kodfragment använder Batch .NET-biblioteket för att skriva ut information om senaste autoskalning körs på poolen _myPool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Utdata från den föregående fragment:

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

## <a name="example-autoscale-formulas"></a>Autoskala formler
Nu ska vi titta på några formler som visar olika sätt att justera beräkningsresurser i poolen.

### <a name="example-1-time-based-adjustment"></a>Exempel 1: Tidsbaserade justering
Anta att du vill justera poolstorleken baserat på dag i veckan och tid på dagen. Det här exemplet visar hur du öka eller minska antalet noder i poolen i enlighet med detta.

Formeln först hämtar den aktuella tiden. Om det är en vardag (1-5) och inom arbetstid (8: 00 till 18: 00) har poolstorleken mål angetts till 20 noder. Annars är den inställd till 10 noder.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Exempel 2: Uppgiftsbaserade justering
I det här exemplet justeras poolstorleken baserat på antalet aktiviteter i kön. Både kommentarer och radbrytningar accepteras i formeln strängar.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exempel 3: Redovisning för parallella aktiviteter
Det här exemplet ändrar poolstorleken baserat på antalet aktiviteter. Den här formeln också tar hänsyn till den [MaxTasksPerComputeNode] [ net_maxtasks] värdet som har angetts för poolen. Den här metoden är användbar i situationer där [parallell körning av aktiviteten](batch-parallel-node-tasks.md) har aktiverats på din pool.

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

### <a name="example-4-setting-an-initial-pool-size"></a>Exempel 4: Ange ett inledande poolstorlek
Det här exemplet illustrerar ett C# kodstycke med en Autoskala formeln som anger poolstorleken till ett angivet antal noder för en inledande tidsperiod. Sedan justeras den poolstorleken baserat på antalet körs och aktiva aktiviteter efter den ursprungliga tidsperioden har löpt ut.

Formeln i följande kodavsnitt:

* Anger den första poolstorleken till fyra noder.
* Justeras inte poolstorleken inom 10 minuter på poolens livscykel.
* Hämtar värdet för max antal efter 10 minuter körs och aktiva aktiviteter inom de senaste 60 minuterna.
  * Om båda värdena är 0 (som anger att inga aktiviteter körs eller aktiv under de senaste 60 minuterna), ange poolstorleken till 0.
  * Ingen ändring görs om antingen värdet är större än noll.

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
* [Maximera Azure Batch beräkning Resursanvändning med samtidiga nod uppgifter](batch-parallel-node-tasks.md) innehåller information om hur du kan köra flera aktiviteter samtidigt på datornoderna i din pool. Förutom autoskalning, kan den här funktionen hjälpa för att sänka jobbet varaktighet för vissa arbetsbelastningar, spara pengar.
* Se till att tillämpningsprogrammet Batch frågar Batch-tjänsten på ett optimalt sätt för en annan effektivitet booster. Se [fråga Azure Batch-tjänsten effektivt](batch-efficient-list-queries.md) information om hur du begränsar hur mycket data som passerar kabeln när du frågar status för tusentals compute-noder eller uppgifter.

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
