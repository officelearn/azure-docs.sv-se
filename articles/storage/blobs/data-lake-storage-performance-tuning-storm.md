---
title: 'Justera prestanda: Storm, HDInsight & Azure Data Lake Storage Gen2 | Microsoft-dokument'
description: Azure Data Lake Storage Gen2 Storm prestandajustering riktlinjer
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 125c583512f6bae34c2dd3c3dd76a1b96a181ac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327906"
---
# <a name="tune-performance-storm-hdinsight--azure-data-lake-storage-gen2"></a>Justera prestanda: Storm, HDInsight & Azure Data Lake Storage Gen2

Förstå de faktorer som bör beaktas när du justerar prestanda för en Azure Storm-topologi. Det är till exempel viktigt att förstå egenskaperna hos det arbete som utförs av pipar och bultar (oavsett om arbetet är I / O eller minnesintensiva). Den här artikeln innehåller en rad riktlinjer för prestandajustering, inklusive felsökning av vanliga problem.

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Storage Gen2-konto**. Instruktioner om hur du skapar ett finns i [Snabbstart: Skapa ett lagringskonto för analytiskt](data-lake-storage-quickstart-create-account.md).
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen2-konto. Se [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Se till att du aktiverar Fjärrskrivbord för klustret.
* **Köra ett Storm-kluster på Data Lake Storage Gen2**. Mer information finns i [Storm på HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Riktlinjer för prestandajustering för Data Lake Storage Gen2**.  Allmänna prestandabegrepp finns i Vägledning för [Prestandajustering för Datasjölagring Gen2](data-lake-storage-performance-tuning-guidance.md).   

## <a name="tune-the-parallelism-of-the-topology"></a>Stämma parallellism av topologin

Du kanske kan förbättra prestanda genom att öka samtidigheten hos I/O till och från Data Lake Storage Gen2. En Storm-topologi har en uppsättning konfigurationer som avgör parallellismen:
* Antal arbetsprocesser (arbetarna är jämnt fördelade över de virtuella datorerna).
* Antal piput executor-instanser.
* Antal bultut executor-instanser.
* Antal pipaktiviteter.
* Antal bultuppgifter.

På ett kluster med 4 virtuella datorer och 4 arbetsprocesser bör du till exempel tänka på följande 32 piputdelser och 32 pipuppgifter och 256 bultutdelsorer och 512 bultuppgifter:

Varje arbetsledare, som är en arbetsnod, har en JVM-process (Single Worker Java Virtual Machine). Denna JVM-process hanterar 4 pipgängor och 64 bultgängor. Inom varje tråd körs aktiviteter sekventiellt. Med föregående konfiguration har varje piptråd 1 uppgift och varje bulttråd har 2 uppgifter.

I Storm, här är de olika komponenter inblandade, och hur de påverkar nivån på parallellism du har:
* Huvudnoden (kallas Nimbus in Storm) används för att skicka in och hantera jobb. Dessa noder har ingen inverkan på graden av parallellism.
* Handledsnoderna. I HDInsight motsvarar detta en azure-dator för arbetsnoden Azure.
* Arbetaruppgifterna är Stormprocesser som körs i de virtuella datorerna. Varje arbetaruppgift motsvarar en JVM-instans. Storm distribuerar antalet arbetsprocesser som du anger till arbetsnoderna så jämnt som möjligt.
* Pip- och bultut executor-instanser. Varje executor-instans motsvarar en tråd som körs inom arbetarna (JVMs).
* Storm uppgifter. Det här är logiska aktiviteter som var och en av dessa trådar kör. Detta ändrar inte nivån på parallellism, så du bör utvärdera om du behöver flera uppgifter per executor eller inte.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Få bästa prestanda från Data Lake Storage Gen2

När du arbetar med Data Lake Storage Gen2 får du bästa prestanda om du gör följande:
* Smält samman dina små bilagor i större storlekar.
* Gör så många samtidiga förfrågningar som du kan. Eftersom varje bultgänga gör blockerande läsningar, vill du ha någonstans i intervallet 8-12 trådar per kärna. Detta håller nätverkskortet och processorn väl utnyttjas. En större virtuell dator möjliggör fler samtidiga begäranden.  

### <a name="example-topology"></a>Exempel topologi

Anta att du har ett 8-arbetsnodkluster med en D13v2 Azure VM. Den här virtuella datorn har 8 kärnor, så bland de 8 arbetarnoderna har du 64 totala kärnor.

Låt oss säga att vi gör 8 bultgängor per kärna. Med tanke på 64 kärnor, det betyder att vi vill ha 512 totala bult executor instanser (det vill säga trådar). I det här fallet, låt oss säga att vi börjar med en JVM per virtuell dator, och främst använda tråden samtidighet inom JVM för att uppnå samtidighet. Det innebär att vi behöver 8 arbetaruppgifter (en per Azure VM) och 512 bultutnrönare. Med tanke på den här konfigurationen försöker Storm fördela arbetarna jämnt mellan arbetsnoder (kallas även arbetsledarnoder), vilket ger varje arbetsnod 1 JVM. Nu inom arbetsledarna försöker Storm fördela utförarna jämnt mellan arbetsledare, vilket ger varje handledare (det vill vara JVM) 8 trådar vardera.

## <a name="tune-additional-parameters"></a>Justera ytterligare parametrar
När du har den grundläggande topologin kan du överväga om du vill justera någon av parametrarna:
* **Antal JVMs per arbetsnod.** Om du har en stor datastruktur (till exempel en uppslagstabell) som du är värd för i minnet, kräver varje JVM en separat kopia. Du kan också använda datastrukturen i många trådar om du har färre JVM:er. För bultens I/O gör antalet JVMs inte lika stor skillnad som antalet trådar som läggs till i dessa JVMs. För enkelhetens skull är det en bra idé att ha en JVM per anställd. Beroende på vad din bult gör eller vilken programbehandling du behöver, men du kan behöva ändra detta nummer.
* **Antal piputsedare.** Eftersom i föregående exempel används bultar för att skriva till Data Lake Storage Gen2, är antalet pipar inte direkt relevant för bultprestandan. Men beroende på hur mycket bearbetning eller I / O som händer i pipen, är det en bra idé att ställa in pipar för bästa prestanda. Se till att du har tillräckligt med pipar för att kunna hålla bultarna upptagna. Utmatningshastigheterna för piparna bör matcha bultarnas genomströmning. Den faktiska konfigurationen beror på pipen.
* **Antal aktiviteter.** Varje bult går som en enda tråd. Ytterligare uppgifter per bult ger inte någon ytterligare samtidighet. Den enda gången de är till nytta är om din process för att erkänna tuppeln tar en stor del av din bult utförande tid. Det är en bra idé att gruppera många tupplar i en större tillägg innan du skickar en bekräftelse från bulten. Så i de flesta fall ger flera uppgifter ingen ytterligare fördel.
* **Lokal eller blanda gruppering.** När den här inställningen är aktiverad skickas tupplar till bultar inom samma arbetsprocess. Detta minskar kommunikation och nätverkssamtal mellan processer. Detta rekommenderas för de flesta topologier.

Detta grundscenario är en bra utgångspunkt. Testa med dina egna data för att justera föregående parametrar för att uppnå optimal prestanda.

## <a name="tune-the-spout"></a>Ställ in pipen

Du kan ändra följande inställningar för att ställa in pipen.

- **Tuple timeout: topology.message.timeout.secs**. Den här inställningen bestämmer hur lång tid ett meddelande tar att slutföra och ta emot bekräftelse innan det anses misslyckas.

- **Max minne per arbetsprocess: worker.childopts**. Med den här inställningen kan du ange ytterligare kommandoradsparametrar för Java-arbetarna. Den vanligaste inställningen här är XmX, som bestämmer det maximala minne som allokeras till en JVM-heap.

- **Max pip väntar: topologi.max.spout.pending**. Den här inställningen bestämmer antalet tupplar som kan vara flygning (ännu inte bekräftats på alla noder i topologin) per piptråd när som helst.

  En bra beräkning att göra är att uppskatta storleken på var och en av dina tupplar. Räkna sedan ut hur mycket minne en piptråd har. Det totala minne som allokerats till en tråd, dividerat med det här värdet, bör ge dig den övre gränsen för den eftersökta parametern max pip.

Standardbulten Data Lake Storage Gen2 Storm har en parameter för storlekssynkronisering (fileBufferSize) som kan användas för att finjustera den här parametern.

I I/O-intensiva topologier är det en bra idé att ha varje bulttråd skriva till sin egen fil och att ställa in en filrotationsprincip (fileRotationSize). När filen når en viss storlek töms strömmen automatiskt och en ny fil skrivs till. Den rekommenderade filstorleken för rotation är 1 GB.

## <a name="monitor-your-topology-in-storm"></a>Övervaka din topologi i Storm  
Medan din topologi körs kan du övervaka den i användargränssnittet Storm. Här är de viktigaste parametrarna att titta på:

* **Total svarstid för processkörning.** Detta är den genomsnittliga tiden en tuple tar att släppas ut av pipen, bearbetas av bulten, och erkände.

* **Total svarstid för bultprocessen.** Detta är den genomsnittliga tiden som tupel vid bulten tills den får en bekräftelse.

* **Total svarstid för bultstring.** Detta är den genomsnittliga tiden som bulten i körningsmetoden är förbrukad.

* **Antal fel.** Detta avser antalet tupplar som inte kunde bearbetas fullt ut innan de tog time out.

* **Kapacitet.** Detta är ett mått på hur upptagen ditt system är. Om detta nummer är 1, dina bultar fungerar så fort de kan. Om det är mindre än 1, öka parallellismen. Om det är större än 1, minska parallellismen.

## <a name="troubleshoot-common-problems"></a>Felsöka vanliga problem
Här är några vanliga felsökningsscenarier.
* **Många tupplar är timing out.** Titta på varje nod i topologin för att avgöra var flaskhalsen är. Den vanligaste orsaken till detta är att bultarna inte kan hålla jämna steg med piparna. Detta leder till tupplar igensättning av de interna buffertarna i väntan på att bearbetas. Överväg att öka timeout-värdet eller minska den väntande maxutloppet.

* **Det finns en hög total processkörningsfördröjning, men en låg bultprocessfördröjning.** I det här fallet är det möjligt att tupplar inte erkänns tillräckligt snabbt. Kontrollera att det finns ett tillräckligt antal bekräftelser. En annan möjlighet är att de väntar i kön för länge innan bultarna börjar bearbeta dem. Minska den max pip som väntar.

* **Det finns en hög bult utföra latens.** Detta innebär att körning() metoden för din bult tar för lång tid. Optimera koden eller titta på skrivstorlekar och färgbeteende.

### <a name="data-lake-storage-gen2-throttling"></a>Data Lake Storage Gen2 begränsning
Om du når gränserna för bandbredd som tillhandahålls av Data Lake Storage Gen2 kan det hända att aktivitetsfel visas. Kontrollera om det finns begränsningsfel i aktivitetsloggarna. Du kan minska parallellismen genom att öka behållarens storlek.    

Om du vill kontrollera om du får begränsat aktiverar du felsökningsloggningen på klientsidan:

1. I **Ambari** > **Storm** > **Config** > Avancerad**storm-worker-log4j**, ändra ** &lt;rotnivå ="info"&gt; ** till ** &lt;rotnivå ="debug"&gt;**. Starta om alla noder/-tjänst för att konfigurationen ska börja gälla.
2. Övervaka Storm-topologiloggarna på arbetsnoder (under&lt;/var/log/storm/worker-artifacts/ TopologyName&gt;/&lt;port&gt;/worker.log) för undantag för datasjölagring gen2.

## <a name="next-steps"></a>Nästa steg
Ytterligare prestandajustering för Storm kan refereras i [den här bloggen](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Ett ytterligare exempel att köra finns [i det här på GitHub](https://github.com/hdinsight/storm-performance-automation).
