---
title: Skala beräkningsnoder automatiskt i en Azure Batch-pool | Microsoft Docs
description: Aktivera automatisk skalning för en cloud-pool för att dynamiskt justera antalet beräkningsnoder i poolen.
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
ms.openlocfilehash: ab41211fb0b0b6360bdbc255e367d0492c2438ed
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331083"
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Skapa en formel för automatisk skalning för att skala beräkningsnoder i en Batch-pool

Azure Batch kan automatiskt skala poolerna baserat på parametrar som du definierar. Batch lägger till dynamiskt noder till poolen som aktiviteten behov öka med automatisk skalning och tar bort compute-noder som de minskar. Du kan spara både tid och pengar genom att automatiskt justera antalet beräkningsnoder som används av ett Batch-program. 

Du aktiverar automatisk skalning i en pool med beräkningsnoder genom att associera med den en *autoskalningsformel* som du definierar. Batch-tjänsten använder formeln för automatisk skalning för att avgöra antalet beräkningsnoder som behövs för att köra din arbetsbelastning. Compute-noder kan vara dedikerade noder eller [lågprioritetsnoder](batch-low-pri-vms.md). Batch svarar på service-mätvärden som samlats in med jämna mellanrum. Batch justerar med hjälp av den här mätvärden, antalet beräkningsnoder i poolen baserat på din formel och ett konfigurerbart intervall.

Du kan aktivera automatisk skalning när poolen skapas, eller på en befintlig pool. Du kan också ändra en befintlig formel på en pool som är konfigurerad för automatisk skalning. Batch kan du utvärdera dina formler innan du tilldelar dem till pooler och för att övervaka status för automatisk skalning körs.

Den här artikeln beskrivs de olika entiteter som utgör din Autoskala formler, inklusive variabler, operatörer, åtgärder och funktioner. Diskuterar vi hur du skaffar olika compute-resurs- och mått i Batch. Du kan använda de här måtten för att justera din pool nodantal baserat på resursstatus för användning och aktivitet. Sedan beskrivs hur du konstruerar en formel och aktivera automatisk skalning för en pool med hjälp av både Batch REST och .NET API: er. Slutligen kan avslutar vi med några formler.

> [!IMPORTANT]
> När du skapar ett Batch-konto kan du ange den [kontokonfigurationen](batch-api-basics.md#account), som bestämmer om pooler allokeras i en prenumeration på tjänsten Batch (standard) eller i din användarprenumeration. Om du har skapat ditt Batch-konto med standardkonfiguration för Batch-tjänsten sedan är ditt konto begränsad till ett högsta antal kärnor som kan användas för bearbetning. Batch-tjänsten skalas beräkningsnoder bara upp till den gränsen för kärnor. Därför kanske Batch-tjänsten inte når antalet beräkningsnoder som anges av en autoskalningsformel. Se [kvoter och begränsningar för Azure Batch-tjänsten](batch-quota-limit.md) information om hur du visar och ökar dina kvoter för kontot.
>
>Om du har skapat ditt konto med kontokonfigurationen för Användarprenumeration sedan ditt konto delar kärnkvoten för prenumerationen. Mer information finns i [Virtual Machines limits](../azure-subscription-service-limits.md#virtual-machines-limits) (Gränser för virtuella datorer) i [Azure subscription and service limits, quotas, and constraints](../azure-subscription-service-limits.md) (Prenumerations- och tjänstgränser, kvoter och begränsningar i Azure).
>
>

## <a name="automatic-scaling-formulas"></a>Automatisk skalning formler
En formel för automatisk skalning är ett strängvärde som du definierar som innehåller en eller flera instruktioner. Formel för automatisk skalning är tilldelad till en pool [autoScaleFormula] [ rest_autoscaleformula] element (Batch REST) eller [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] Egenskapen (Batch .NET). Batch-tjänsten använder formeln för att bestämma antalet beräkningsnoder i poolen för nästa intervall av bearbetning. Formeln strängen får inte överskrida 8 KB, kan innehålla upp till 100 rapporter som är avgränsade med semikolon och kan innehålla radbrytningar och kommentarer.

Du kan tänka dig automatisk skalning formler som Batch-Autoskala ”language”. Formeln satser är kostnadsfria formaterad uttryck som kan innehålla både tjänstdefinierade variabler (variabler som definieras av Batch-tjänsten) och användardefinierade variabler (variabler som du definierar). De kan utföra olika åtgärder på dessa värden med hjälp av inbyggda typer, operatorer och funktioner. En instruktion kan till exempel ha följande format:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formler innehåller vanligtvis flera instruktioner som utför åtgärder på värden som hämtas i tidigare rapporter. Exempelvis kan vi hämtar du först ett värde för `variable1`, skickar den till en funktion för att fylla i `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Inkludera dessa instruktioner i din formel för automatisk skalning att komma till ett som målantal compute-noder. Dedikerade noder och noder med låg prioritet har sina egna Målinställningar så att du kan definiera ett mål för varje typ av nod. En formel för automatisk skalning kan innehålla ett målvärde för dedikerade noder, ett målvärde för lågprioriterade virtuella noder eller båda.

Antalet noder kan vara högre, lägre eller samma som det aktuella antalet noder av den typen i poolen. Batch utvärderar autoskalningsformel för en pool med jämna mellanrum (se [för automatisk skalning intervall](#automatic-scaling-interval)). Batch justerar antalet varje typ av nod i poolen för hur många som din formel för automatisk skalning anger vid tidpunkten för utvärdering.

### <a name="sample-autoscale-formula"></a>Exemplet autoskalningsformel

Här är ett exempel på en formel för automatisk skalning som kan ställas in för att fungera för de flesta scenarier. Variablerna `startingNumberOfVMs` och `maxNumberofVMs` i det här exemplet formel kan justeras efter dina behov. Den här formeln kan skalas dedikerade noder, men kan ändras för att tillämpa på skala lågprioriterade noder. 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

Med den här autoskalningsformel skapas poolen med en enda virtuell dator. Den `$PendingTasks` mått definierar antalet aktiviteter som körs eller står i kö. Formeln hittar det genomsnittliga antalet väntande aktiviteter i de senaste 180 sekunder och anger den `$TargetDedicatedNodes` variabeln därefter. Formeln säkerställer att antalet dedikerade noder aldrig överstiger 25 virtuella datorer. När nya aktiviteter skickas växer automatiskt i poolen. Som aktiviteterna slutförs kan virtuella datorer blir kostnadsfria en i taget och formel för automatisk skalning minskar storleken på poolen.

## <a name="variables"></a>Variabler
Du kan använda båda **tjänstdefinierade** och **användardefinierade** variabler i dina formler för automatisk skalning. Definierade-variabler är inbyggda i Batch-tjänsten. Vissa definierade variabler är skrivskyddade och vissa är skrivskyddad. Användardefinierade variabler är variabler som du definierar. I exempel-formeln som visas i det föregående avsnittet `$TargetDedicatedNodes` och `$PendingTasks` är definierade variabler. Variabler `startingNumberOfVMs` och `maxNumberofVMs` användardefinierade variabler.

> [!NOTE]
> Definierade variabler föregås alltid av ett dollartecken ($). För användardefinierade variabler är dollartecknet valfritt.
>
>

I följande tabeller visas både läs-och skrivbara och skrivskyddade variabler som definieras av Batch-tjänsten.

Du kan hämta och ange värdena för variablerna definierade för att hantera antalet beräkningsnoder i en pool:

| Läs-och definierade variabler | Beskrivning |
| --- | --- |
| $TargetDedicatedNodes |Antalet dedikerade beräkningsnoder för poolen. Antal dedikerade noder har angetts som mål eftersom poolen inte kan alltid når det önskade antalet noder. Till exempel om antalet dedikerade noder ändras av en automatisk skalning utvärdering innan poolen har nått det ursprungliga målet, kan sedan poolen inte nå målet. <br /><br /> En pool i ett konto som skapats med konfigurationen av Batch-tjänsten kan inte få sitt mål om målet överskrider en nod- eller core batchkonton. En pool i ett konto som skapats med kontokonfigurationen för Användarprenumeration kan inte få sitt mål om målet överskrider delad kärna-kvot för prenumerationen.|
| $TargetLowPriorityNodes |Antalet lågprioriterade beräkningsnoder för poolen. Antalet noder med låg prioritet har angetts som mål eftersom poolen inte kan alltid når det önskade antalet noder. Till exempel om antalet noder med låg prioritet ändras av en automatisk skalning utvärdering innan poolen har nått det ursprungliga målet, kan sedan poolen inte nå målet. En pool kan också inte uppnå sitt mål om målet överskrider en nod- eller core batchkonton. <br /><br /> Läs mer på beräkningsnoder med låg prioritet [använda virtuella datorer med låg prioritet med Batch (förhandsversion)](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Vad som händer när compute-noder tas bort från poolen. Möjliga värden:<ul><li>**ny kö**– avslutar uppgifter direkt och placerar dem tillbaka i jobbkön så att de schemaläggs.<li>**Avsluta**– avslutar uppgifter direkt och tar bort dem från jobbkön.<li>**taskcompletion**--väntar för pågående aktiviteter för att avsluta och tar bort noden från poolen.<li>**retaineddata**--väntar tills alla lokala uppgift kvarhållna data på noden kan rensas innan du tar bort noden från poolen.</ul> |

Du kan hämta värdet för dessa definierade variabler för justeringar som baseras på mått från Batch-tjänsten:

| Skrivskyddade definierade variabler | Beskrivning |
| --- | --- |
| $CPUPercent |Den genomsnittliga procentandelen av CPU-användning. |
| $WallClockSeconds |Hur många sekunder som förbrukas. |
| $MemoryBytes |Genomsnittligt antal megabyte som används. |
| $DiskBytes |Genomsnittligt antal GB som används på de lokala diskarna. |
| $DiskReadBytes |Antalet lästa byte. |
| $DiskWriteBytes |Antalet skrivna byte. |
| $DiskReadOps |Totalt antal Läs diskåtgärder som utförs. |
| $DiskWriteOps |Totalt antal skrivåtgärder disk som utförs. |
| $NetworkInBytes |Antal inkommande byte. |
| $NetworkOutBytes |Antal utgående byte. |
| $SampleNodeCount |Antal beräkningsnoder. |
| $ActiveTasks |Antal aktiviteter som är redo att köra men ännu inte körs. Antalet $ActiveTasks innehåller alla aktiviteter som är aktiv och vars förutsättningar är uppfyllda. Alla aktiviteter som finns i aktivt läge men vars beroenden är inte uppfyllda undantas från antalet $ActiveTasks.|
| $RunningTasks |Antal aktiviteter körs. |
| $PendingTasks |Summan av $ActiveTasks och $RunningTasks. |
| $SucceededTasks |Antal aktiviteter som har slutförts. |
| $FailedTasks |Antalet uppgifter som misslyckats. |
| $CurrentDedicatedNodes |Det aktuella antalet dedikerade beräkningsnoder. |
| $CurrentLowPriorityNodes |Det aktuella antalet lågprioriterade beräkningsnoder, inklusive alla noder som har återtas. |
| $PreemptedNodeCount | Antalet noder i poolen som är i tillståndet återtas. |

> [!TIP]
> De variabler som skrivskyddad, definierade som visas i föregående tabell är *objekt* som ger olika metoder för att komma åt data som hör till var och en. Mer information finns i [Hämta exempeldata](#getsampledata) senare i den här artikeln.
>
>

## <a name="types"></a>Typer
Dessa typer stöds i en formel:

* double
* doubleVec
* doubleVecList
* sträng
* tidsstämpel--tidsstämpel är en sammansatt struktur som innehåller följande medlemmar:

  * år
  * månad (1-12)
  * dag (1-31)
  * WEEKDAY (i formatet för tal, till exempel 1 för måndag)
  * timme (i 24-timmarsformat talformatet; exempelvis 13 innebär 1 PM)
  * minut (00-59)
  * sekund (00-59)
* TimeInterval

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
Dessa är tillåtet för de typer som anges i föregående avsnitt.

| Åtgärd | Operatorer som stöds | Resultattyp |
| --- | --- | --- |
| dubbla *operatorn* dubbla |+, -, *, / |double |
| dubbla *operatorn* timeinterval |* |TimeInterval |
| doubleVec *operatorn* dubbla |+, -, *, / |doubleVec |
| doubleVec *operatorn* doubleVec |+, -, *, / |doubleVec |
| TimeInterval *operatorn* dubbla |*, / |TimeInterval |
| TimeInterval *operatorn* timeinterval |+, - |TimeInterval |
| TimeInterval *operatorn* tidsstämpel |+ |tidsstämpel |
| tidsstämpel *operatorn* timeinterval |+ |tidsstämpel |
| tidsstämpel *operatorn* tidsstämpel |- |TimeInterval |
| *operatorn*dubbla |-, ! |double |
| *operatorn*timeinterval |- |TimeInterval |
| dubbla *operatorn* dubbla |<, <=, ==, >=, >, != |double |
| sträng *operatorn* sträng |<, <=, ==, >=, >, != |double |
| tidsstämpel *operatorn* tidsstämpel |<, <=, ==, >=, >, != |double |
| TimeInterval *operatorn* timeinterval |<, <=, ==, >=, >, != |double |
| dubbla *operatorn* dubbla |&&, &#124;&#124; |double |

När du testar ett double-värde med en ternär operator (`double ? statement1 : statement2`) inte är noll är **SANT**, och noll är **FALSKT**.

## <a name="functions"></a>Functions
Dessa fördefinierade **functions** är tillgängliga som du kan använda för att definiera en formel för automatisk skalning.

| Funktion | Returtyp | Beskrivning |
| --- | --- | --- |
| AVG(doubleVecList) |double |Returnerar medelvärdet för alla värden i doubleVecList. |
| Len(doubleVecList) |double |Returnerar längden på vektor som skapas från doubleVecList. |
| LG(Double) |double |Returnerar loggen bas 2 av på dubbel. |
| LG(doubleVecList) |doubleVec |Returnerar component-wise loggen bas 2 av doubleVecList. En vec(double) måste uttryckligen angavs för parametern. I annat fall antas den dubbla lg(double)-versionen. |
| LN(Double) |double |Returnerar den naturliga loggen av på dubbel. |
| LN(doubleVecList) |doubleVec |Returnerar component-wise loggen bas 2 av doubleVecList. En vec(double) måste uttryckligen angavs för parametern. I annat fall antas den dubbla lg(double)-versionen. |
| log(Double) |double |Returnerar loggen bas 10 av på dubbel. |
| log(doubleVecList) |doubleVec |Returnerar component-wise loggen bas 10 för doubleVecList. En vec(double) måste vara explicit angavs för parametern för enkel dubbla. I annat fall antas den dubbla log(double)-versionen. |
| Max(doubleVecList) |double |Returnerar det största värdet i doubleVecList. |
| min(doubleVecList) |double |Returnerar det minsta värdet i doubleVecList. |
| norm(doubleVecList) |double |Returnerar de två normen för vektor som skapas från doubleVecList. |
| : e percentilen (doubleVec v dubbla p) |double |Returnerar elementet: e percentilen i vektor v. |
| SLUMP() |double |Returnerar ett slumpmässigt värde mellan 0,0 och 1,0. |
| Range(doubleVecList) |double |Returnerar skillnaden mellan lägsta och högsta värden i doubleVecList. |
| Std(doubleVecList) |double |Returnerar standardavvikelsen för urvalet av värdena i doubleVecList. |
| Stop) | |Stoppar utvärderingen av uttrycket för automatisk skalning. |
| SUM(doubleVecList) |double |Returnerar summan av alla komponenter i doubleVecList. |
| tid (string dateTime = ””) |tidsstämpel |Returnerar tidsstämpeln för den aktuella tiden om inga parametrar skickas eller tidsstämpeln för dateTime-strängen om den skickas. Stöds dateTime-format är W3C-DTF och RFC 1123. |
| val (doubleVec v dubbla i) |double |Returnerar värdet för det element som är på plats i i vektor v, med ett startIndex noll. |

Några av de funktioner som beskrivs i föregående tabell kan acceptera en lista som ett argument. Kommaavgränsad lista är en kombination av *dubbla* och *doubleVec*. Exempel:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Den *doubleVecList* värde konverteras till en enda *doubleVec* innan utvärderingen. Till exempel om `v = [1,2,3]`, sedan anropa `avg(v)` motsvarar att anropa `avg(1,2,3)`. Anropa `avg(v, 7)` motsvarar att anropa `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Hämta exempeldata
Automatisk skalning formler fungerar på måttdata (exempel) som tillhandahålls av Batch-tjänsten. En formel förstoras eller förminskas poolstorlek baserat på de värden som den hämtar från tjänsten. De definierade variabler som beskrivs ovan är som tillhandahåller olika metoder för att komma åt data som är associerade med objektet. Följande uttryck visar exempelvis en begäran att hämta de senaste fem minuterna av CPU-användning:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Metod | Beskrivning |
| --- | --- |
| GetSample() |Den `GetSample()` metoden returnerar en vektor med insamlade data.<br/><br/>Ett exempel är 30 sekunder värt av mätvärden. Med andra ord hämtas exempel med 30 sekunders mellanrum. Men enligt nedan, det finns en fördröjning mellan när ett exempel som samlas in och när den är tillgänglig på en formel. Därför kanske inte alla exempel för en viss tidsperiod för utvärdering av en formel.<ul><li>`doubleVec GetSample(double count)`<br/>Anger antalet exempel som att hämta från de senaste exempel som samlades in.<br/><br/>`GetSample(1)` Returnerar det senaste tillgängliga exemplet. För mått som `$CPUPercent`, men detta bör inte användas eftersom det är omöjligt att veta *när* exemplet samlades in. Det kan vara senaste eller på grund av systemproblem, kan det vara mycket äldre. Det är bättre i sådana fall att använda ett tidsintervall som visas nedan.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Anger en tidsram för att samla in exempeldata. Du kan också anger den också procentandelen exempel som måste vara tillgängliga under den begärda tidsperioden.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` Returnerar 20 prov om alla exempel för de senaste 10 minuterna finns i CPUPercent historiken. Om den sista minuten av tidigare inte var tillgänglig, men skulle endast 18 exempel returneras. I det här fallet:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` misslyckas eftersom 90 procent av exempel som är tillgängliga.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` fungerar.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Anger en tidsram för att samla in data med både en starttid och en sluttid.<br/><br/>Som nämnts ovan är finns det en fördröjning mellan när ett exempel som samlas in och när den är tillgänglig på en formel. Överväg att den här fördröjningen när du använder den `GetSample` metoden. Se `GetSamplePercent` nedan. |
| GetSamplePeriod() |Returnerar perioden på exemplen som utförts i en uppsättning historiska exempeldata. |
| Antal() |Returnerar det totala antalet exempel i historiken för måttet. |
| HistoryBeginTime() |Returnerar tidsstämpeln för den äldsta tillgängliga datasamplingen för måttet. |
| GetSamplePercent() |Returnerar procentandelen exempel som är tillgängliga för ett angivet tidsintervall. Exempel:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Eftersom den `GetSample` metoden inte fungerar om procentandelen exempel returneras är mindre än värdet `samplePercent` anges som du kan använda den `GetSamplePercent` metoden för att kontrollera först. Du kan sedan utföra en annan åtgärd om otillräckligt exemplen finns, utan att stoppa automatisk skalning utvärderingen. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Exempel, exempel procent och *GetSample()* metod
Grundläggande drift av en autoskalningsformel är att hämta aktivitets- och måttdata och sedan justera poolstorlek baserat på dessa data. Det är därför viktigt att ha en förståelse för hur automatisk skalning formler interagerar med mätvärden (exempel).

**Exempel**

Batch-tjänsten med jämna mellanrum tar exempel aktivitets- och mått och gör dem tillgängliga för automatisk skalning formler. Dessa exempel registreras med 30 sekunders mellanrum av Batch-tjänsten. Men det tar normalt en fördröjning mellan när dessa exempel har registrerats och när de är tillgängliga för (och kan läsas av) Autoskala formler. På grund av olika faktorer, till exempel nätverks- eller andra problem med infrastruktur, kan dessutom exempel inte registreras för ett visst intervall.

**Exemplet procent**

När `samplePercent` skickas till den `GetSample()` metod eller `GetSamplePercent()` metoden anropas _procent_ refererar till en jämförelse mellan det totala antalet exempel som registreras av Batch-tjänsten och antalet exempel som är tillgängliga för din formel för automatisk skalning.

Nu ska vi titta på timespan 10: e minut som exempel. Eftersom exempel har registrerats med 30 sekunders mellanrum inom timespan 10: e minut, är det maximala antalet exempel som registreras av Batch 20 prov (2 per minut). Men på grund av den inneboende latensen för mekanismen för rapportering och andra problem i Azure, kan det finnas endast 15-exempel som är tillgängliga för din formel för automatisk skalning för läsning. Så till exempel vara för den 10-minutersperioden, endast 75% av det totala antalet exempel som registreras tillgängliga för din formel.

**Exempel och GetSample() intervall**

Automatisk skalning formler ska växer och minska storleken på dina pooler &mdash; att lägga till noder eller ta bort noder. Eftersom noder kostar pengar, som du vill kontrollera att dina formler använder en intelligent metod för analys som baseras på uppgifter. Därför rekommenderar vi att du använder en trendanalys-type-analys i formeln. Den här typen ökar och minskar dina pooler baserat på ett antal insamlade exempel.

Du gör detta genom att använda `GetSample(interval look-back start, interval look-back end)` att returnera en vektor med exempel:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

När ovanstående rad utvärderas av Batch och returnerar ett antal exempel som en vektor med värden. Exempel:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

När du har lagrat vektor exempel, du kan sedan använda funktioner som `min()`, `max()`, och `avg()` att härleda meningsfulla värden från insamlade intervallet.

För ytterligare säkerhet kan du tvinga Formelutvärdering misslyckas om mindre än en viss procentandel i exemplet är tillgänglig för en viss tidsperiod. När du tvinga Formelutvärdering misslyckas, kan du instruera Batch att upphöra ytterligare utvärdering av formeln om den angivna procentandelen av exemplen inte är tillgänglig. Ingen ändring görs i det här fallet poolstorleken. Om du vill ange en obligatorisk procentandel-exempel för utvärderingen ska lyckas, ange den som tredje parameter ska `GetSample()`. Här är har ett krav i 75 procent av exempel angetts:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Eftersom det kan uppstå en fördröjning i exempel tillgänglighet, är det viktigt att alltid ange ett tidsintervall med titt bak starttid som är äldre än en minut. Det tar ungefär en minut efter exempel ska spridas genom systemet, så exemplen i intervallet `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` kanske inte tillgänglig. Igen, du kan använda parametern procent av `GetSample()` att tvinga ett visst exempel procent krav.

> [!IMPORTANT]
> Vi **rekommenderar** som du **förlita dig inte *endast* på `GetSample(1)` i formlerna Autoskala**. Detta beror på `GetSample(1)` i stort sett säger till Batch-tjänsten ”ge mig senaste exempel du har, oavsett hur länge sedan du hämtade”. Eftersom det är bara ett enda exempel och det kan vara ett exempel på äldre, kanske inte representativ för större bilden för senaste aktivitet eller -resursens tillstånd. Om du använder `GetSample(1)`, se till att det är en del av ett större uttryck och inte bara datapunkten som din formel förlitar sig på.
>
>

## <a name="metrics"></a>Mått
Du kan använda både resurs- och mått när du definierar en formel. Du kan justera antalet dedikerade noder i poolen baserat på mätvärden som du får och utvärdera. Se den [variabler](#variables) ovan för mer information om varje mått.

<table>
  <tr>
    <th>Mått</th>
    <th>Beskrivning</th>
  </tr>
  <tr>
    <td><b>Resurs</b></td>
    <td><p>Resursmått baseras på CPU, bandbredd, minnesanvändningen för compute-noder och antalet noder.</p>
        <p> Dessa definierade variabler är användbara för att göra justeringar baserat på antalet noder:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$preemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Dessa definierade variabler är användbara för att göra justeringar baserat på Resursanvändning för noden:</p>
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
    <td><p>Uppgiften mått baserat på status för aktiviteter, till exempel aktiv, väntar, och slutförs. Följande definierade variabler är användbara för att göra justeringar för pool-storlek baserat på aktivitet mått:</p>
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
Du skapar en autoskalningsformel genom instruktioner som använder de ovanstående komponenterna och sedan kombinera dessa instruktioner i en fullständig formel. I det här avsnittet ska skapa vi en autoskalningsformel för exempel som kan utföra vissa verkliga skalning beslut.

Först ska vi definiera kraven för vår nya formel för automatisk skalning. Formeln bör:

1. Öka antalet dedikerade beräkningsnoder i en pool om processoranvändningen är hög.
2. Minska antalet dedikerade beräkningsnoder i en pool när CPU-användningen är låg.
3. Alltid begränsa det maximala antalet dedikerade noder till 400.

Om du vill öka antalet noder under hög CPU-användning, definiera instruktionen som fyller på en användardefinierad variabel (`$totalDedicatedNodes`) med ett värde som är 110 procent av det aktuella antalet dedikerade noder, men bara om den minsta Genomsnittlig CPU-användningen under den senaste 10 minuterna översteg 70 procent. Annars använder du värdet för det aktuella antalet dedikerade noder.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Att *minska* antalet dedikerade noder under låg CPU-användning, nästa utdrag i vår formel anger samma `$totalDedicatedNodes` variabeln och 90 procent av aktuellt antal dedikerade noder om Genomsnittlig CPU-användning under senaste 60 minuter var under 20 procent. Annars kan du använda det aktuella värdet för `$totalDedicatedNodes` som vi har fyllts i ovan-instruktionen.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Nu begränsa antalet dedikerade beräkningsnoder till högst 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Här är fullständig formel:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>Skapa en pool med automatisk skalning aktiverat med .NET

Följ dessa steg om du vill skapa en pool med automatisk skalning aktiverat i .NET:

1. Skapa poolen med [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
2. Ange den [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) egenskap `true`.
3. Ange den [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) egenskap med din formel för automatisk skalning.
4. (Valfritt) Ange den [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) egenskapen (standardvärdet är 15 minuter).
5. Genomför poolen med [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) eller [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Följande kodfragment skapar en pool för automatisk skalning aktiverat i .NET. Poolens autoskalningsformel anger antalet dedikerade noder till 5 på måndagar och 1 på varannan dag i veckan. Den [intervall för automatisk skalning](#automatic-scaling-interval) är inställd på 30 minuter. I den här och andra C# fragmenten i den här artikeln `myBatchClient` är en korrekt initierad instans av den [BatchClient] [ net_batchclient] klass.

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
> När du skapar en pool med automatisk skalning aktiverat kan inte anger den _targetDedicatedComputeNodes_ parametern eller _targetLowPriorityComputeNodes_ parameter i anropet till **CreatePool** . Ange i stället den **AutoScaleEnabled** och **AutoScaleFormula** egenskaper för poolen. Värden för dessa egenskaper fastställa antalet varje typ av nod. Dessutom att manuellt ändra storlek på en autoskalning-aktiverat pool (till exempel med [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync]), första **inaktivera** automatisk skalning på den lagringspoolen och sedan ändra storlek på den.
>
>

Förutom Batch .NET kan du använda någon av de andra [Batch SDK: erna](batch-apis-tools.md#azure-accounts-for-batch-development), [Batch REST](https://docs.microsoft.com/rest/api/batchservice/), [Batch PowerShell-cmdlets](batch-powershell-cmdlets-get-started.md), och [Batch CLI](batch-cli-get-started.md) till Konfigurera automatisk skalning.


### <a name="automatic-scaling-interval"></a>Intervall för automatisk skalning
Som standard justerar Batch-tjänsten en poolstorlek enligt dess autoskalningsformel var 15: e minut. Det här intervallet kan konfigureras med hjälp av följande egenskaper för poolen:

* [CloudPool.AutoScaleEvaluationInterval] [ net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (REST API)

Den minsta är fem minuter och högsta är 168 timmar. Om ett intervall utanför det här intervallet har angetts returnerar Batch-tjänsten en felaktig begäran (400) fel.

> [!NOTE]
> Automatisk skalning är inte avsedd att svara på ändringar i mindre än en minut, men i stället är avsedd att justera storleken på din pool gradvis som du kör en arbetsbelastning.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Aktivera automatisk skalning på en befintlig pool

Varje Batch-SDK är ett sätt att aktivera automatisk skalning. Exempel:

* [BatchClient.PoolOperations.EnableAutoScaleAsync] [ net_enableautoscaleasync] (Batch .NET)
* [Aktivera automatisk skalning för en pool] [ rest_enableautoscale] (REST API)

När du aktiverar automatisk skalning på en befintlig pool, Tänk på följande saker övervägas:

* Om automatisk skalning är inaktiverat på poolen när du skickar förfrågan om att aktivera automatisk skalning, måste du ange en giltig autoskalningsformel när du skickar ut begäran. Du kan du ange en skala automatiskt utvärderingsintervall. Om du inte anger ett intervall, används standardvärdet 15 minuter.
* Om automatisk skalning är aktiverat i poolen, kan du ange en autoskalningsformel, en utvärderingsintervall eller båda. Du måste ange minst en av de här egenskaperna.

  * Om du anger en ny skala automatiskt utvärderingsintervall befintliga utvärderingsschema har stoppats och ett nytt schema har startats. Starttid för det nya schemat är den tid då begäran om att aktivera automatisk skalning har utfärdats.
  * Om du utelämnar formel för automatisk skalning eller utvärderingsintervall, Batch-tjänsten fortsätter att använda det aktuella värdet för den här inställningen.

> [!NOTE]
> Om du har angett värden för den *targetDedicatedComputeNodes* eller *targetLowPriorityComputeNodes* parametrarna för den **CreatePool** metoden när du skapade poolen i .NET, eller för parametrarna jämförbara på ett annat språk sedan ignoreras dessa värden när automatisk skalningsformel utvärderas.
>
>

Det här kodfragmentet för C# använder den [Batch .NET] [ net_api] -biblioteket för att aktivera automatisk skalning på en befintlig pool:

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

Om du vill uppdatera formel på en befintlig pool för automatisk skalning aktiverat, kan du anropa åtgärden för att aktivera automatisk skalning igen med den nya formeln. Exempel: om automatisk skalning är redan aktiverat på `myexistingpool` när följande .NET-kod körs en formel för automatisk skalning ersätts med innehållet i `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Intervall för automatisk skalning

Om du vill uppdatera skala automatiskt utvärderingsintervall av en befintlig pool för automatisk skalning aktiverat, kan du anropa åtgärden om du vill aktivera automatisk skalning igen med nytt intervall. Till exempel vill ange skala automatiskt utvärderingsintervall till 60 minuter för en pool som redan är aktiverat automatisk skalning i .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Utvärdera en formel för automatisk skalning

Du kan utvärdera en formel innan den tillämpas på en pool. På så sätt kan testa du formeln för att se hur dess uttryck utvärderas innan du placerar formeln till produktion.

Om du vill utvärdera en autoskalningsformel, måste du först aktivera automatisk skalning för poolen med en giltig formel. Testa en formel på en pool som ännu inte har automatisk skalning aktiverat genom att använda en rad formel `$TargetDedicatedNodes = 0` när du först aktiverar automatisk skalning. Sedan använder du något av följande för att utvärdera formeln som du vill testa:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) eller [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Dessa Batch .NET-metoder kräver ID för en befintlig pool och en sträng som innehåller autoskalningsformel som ska utvärderas.

* [Utvärdera en formel för automatisk skalning](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    I det här REST API-begäran anger pool-ID i URI: N och autoskalningsformel i den *autoScaleFormula* element i begärandetexten. Svaret på åtgärden innehåller information om eventuella fel som kan vara relaterade till formeln.

I det här [Batch .NET] [ net_api] kodfragmentet, vi utvärderar en formel för automatisk skalning. Om poolen inte har automatisk skalning aktiverat, aktivera vi det först.

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

Lyckad utvärderingen av formeln som visas i det här kodfragmentet skapar resultat som liknar:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Få information om automatisk skalning körningar

För att säkerställa att din formel fungerar som förväntat, rekommenderar vi att du regelbundet kontrollerar resultaten av automatisk skalning körningar som Batch utför på din pool. Att göra så få (eller uppdatera) en referens till i poolen och kontrollera egenskaperna för dess senaste Autoskala kör.

I Batch .NET den [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) egenskapen har flera egenskaper som innehåller information om den senaste autoskalning köra utförs på poolen:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

I REST-API i [få information om en pool](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) begäran returnerar information om poolen, som innehåller den senaste automatisk skalning som kör information i den [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool#bk_autrun) egenskapen.

I följande C#-kodavsnitt använder Batch .NET-biblioteket för att skriva ut information om den senaste autoskalning som körs på poolen _myPool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Exempel på utdata från föregående fragment:

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

## <a name="example-autoscale-formulas"></a>Automatisk skalning formler
Nu ska vi titta på några formler som visar olika sätt att justera mängden beräkningsresurser i en pool.

### <a name="example-1-time-based-adjustment"></a>Exempel 1: Tidsbaserade justering
Anta att du vill justera poolstorleken baserat på dagen i veckan och tid på dagen. Det här exemplet visar hur du öka eller minska antalet noder i poolen på lämpligt sätt.

Formeln hämtar först den aktuella tiden. Om det är en vardag (1-5) och inom arbetstimmar (8: 00 till 18: 00), har poolstorlek mål angetts till 20 noder. I annat fall är den inställd på 10 noder.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Exempel 2: Uppgiftsbaserade justering
I det här exemplet justeras poolstorleken beroende på antalet aktiviteter i kön. Både kommentarer och radbrytningar accepteras i formeln strängar.

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

### <a name="example-3-accounting-for-parallel-tasks"></a>Exempel 3: Redovisning för parallella uppgifter
Det här exemplet justerar poolstorleken baserat på antalet uppgifter. Den här formeln tar även hänsyn till den [MaxTasksPerComputeNode] [ net_maxtasks] värde som har ställts in för poolen. Den här metoden är användbar i situationer där [parallell körning av aktiviteten](batch-parallel-node-tasks.md) har aktiverats på din pool.

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

### <a name="example-4-setting-an-initial-pool-size"></a>Exempel 4: Ange en inledande poolstorlek
Det här exemplet visar ett C# kodfragment med en autoskalningsformel som anger poolstorleken till ett angivet antal noder för en inledande tidsperiod. Sedan justeras den pool-storlek baserat på antalet körs och aktiva uppgifter när den inledande tidsperioden har gått ut.

Formeln i följande kodavsnitt:

* Anger den första poolstorleken till fyra noder.
* Går inte att justera poolstorleken inom de första 10 minuterna av poolens livscykel.
* Hämtar maxvärdet för antalet efter 10 minuter som körs och aktiva uppgifter inom de senaste 60 minuterna.
  * Om båda värdena är 0 (som anger att inga uppgifter har aktiv eller aktiv under de senaste 60 minuterna), anges poolstorleken till 0.
  * Om antingen värdet är större än noll, har ingen ändring gjorts.

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
* [Maximera resursanvändningen för Azure Batch compute med samtidiga nodaktiviteter](batch-parallel-node-tasks.md) innehåller information om hur du kan köra flera uppgifter samtidigt på beräkningsnoderna i poolen. Förutom automatisk skalning och kan den här funktionen hjälpa till att sänka varaktighet för jobb för vissa arbetsbelastningar, vilket sparar pengar.
* En annan effektivitet booster-paket, se till att ditt Batch-program frågar Batch-tjänsten på bästa sätt. Se [fråga Azure Batch-tjänsten effektivt](batch-efficient-list-queries.md) information om hur du begränsar hur mycket data som överförs när du har frågat tusentals beräkningsnoder eller uppgifter.

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
