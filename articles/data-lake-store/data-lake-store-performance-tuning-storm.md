---
title: Azure Data Lake Store Storm prestandajustering riktlinjer | Microsoft Docs
description: Azure Data Lake Store Storm prestandajustering riktlinjer
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 1dfa93643f45a96ded3fd022aa8b1c71d487acb4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-store"></a>Prestandajustering för Storm på HDInsight och Azure Data Lake Store

Förstå de faktorer som ska beaktas när du finjustera prestanda för en Azure Storm-topologi. Exempelvis är det viktigt att förstå det arbete som utförs av kanaler och bultarna (om arbetet är i/o- eller minnesintensiva) egenskaper. Den här artikeln innehåller en uppsättning prestandajustering riktlinjer, inklusive felsökning av vanliga problem.

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Store-konto**. Anvisningar om hur du skapar en finns [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Ett Azure HDInsight-kluster** med åtkomst till ett Data Lake Store-konto. Se [skapar ett HDInsight-kluster med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Kontrollera att du kan aktivera Fjärrskrivbord för klustret.
* **Kör ett Storm-kluster på Data Lake Store**. Mer information finns i [Storm på HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-overview).
* **Prestandajustering riktlinjer för Data Lake Store**.  Allmänna prestanda begrepp finns [Data Lake Store justera Prestandaråd](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance).  

## <a name="tune-the-parallelism-of-the-topology"></a>Justera topologins parallellitet

Du kanske kan förbättra prestanda genom att öka samtidighet på läsning/skrivning till och från Data Lake Store. En Storm-topologi har en uppsättning av konfigurationer som bestämmer parallellitet:
* Antalet arbetsprocesser (arbetare är jämnt fördelad över de virtuella datorerna).
* Antalet kanal utföraren instanser.
* Antalet instanser av bulten utförare.
* Antal kanal uppgifter.
* Antal bult uppgifter.

Till exempel på ett kluster med 4 virtuella datorer och 4 arbetsprocesser, 32 kanal executors och 32 kanal aktiviteter och 256 bult executors och 512 bult uppgifter, Tänk på följande:

Varje Övervakare som är en arbetsnod, har en enda worker Java virtual machine (JVM)-processen. Den här processen JVM hanterar 4 kanal trådar och 64 bult trådar. Inom varje tråd körs aktiviteter sekventiellt. Varje kanal tråd har 1 aktivitet med föregående konfiguration, och varje bult tråd har 2 aktiviteter.

Här följer de olika komponenterna som ingår i Storm, och hur de påverkar parallellitet som du har:
* Huvudnod (kallas Nimbus i Storm) används för att skicka in och hantera jobb. Dessa noder har ingen inverkan på i vilken grad av parallellitet.
* Handledarens-noder. HDInsight motsvarar en arbetsnod Azure VM.
* Worker-uppgifter är Storm-processer som körs i de virtuella datorerna. Varje worker aktivitet motsvarar en JVM-instans. Storm distribuerar antalet arbetsprocesser som du anger till arbetsnoderna jämnt som möjligt.
* Prata och bultar utföraren instanser. Varje instans utföraren motsvarar en tråd som körs inom arbetare (JVMs).
* Storm-aktiviteter. Detta är logiska aktiviteter som var och en av dessa trådar körs. Detta ändrar inte andelen parallellitet så att du ska utvärdera om du behöver flera uppgifter per utföraren eller inte.

### <a name="get-the-best-performance-from-data-lake-store"></a>Få bästa möjliga prestanda från Data Lake Store

När du arbetar med Data Lake Store kan få du bästa möjliga prestanda om du gör följande:
* Slå samman ett litet lägger till större storlek (helst 4 MB).
* Göra så många samtidiga förfrågningar som möjligt. Eftersom varje bult tråd gör blockerande läsningar, som du vill ha någonstans i intervallet 12 8 trådar per kärna. Detta håller nätverkskortet och Processorn också används. En större VM gör det möjligt för flera samtidiga begäranden.  

### <a name="example-topology"></a>Exempeltopologi

Anta att du har en 8 worker nodskluster med en D13v2 Azure VM. Den här virtuella datorn har 8 kärnor, så mellan 8 worker-noder du ha 64 Totalt antal kärnor.

Anta att vi gör 8 bult trådar per kärna. Den angivna 64 kärnor innebär som vi vill 512 totala bult utföraren instanser (det vill säga trådar). I det här fallet anta att vi börjar med en JVM per virtuell dator och använda huvudsakligen tråd samtidighet i JVM för att uppnå samtidighet. Det innebär att vi behöver 8 worker uppgifter (en per virtuell Azure-dator) och 512 bult executors. Med den här konfigurationen Storm försöker distribuera arbetare jämnt över arbetsnoder (även kallat handledarens noder), ger varje arbetsnod 1 JVM. Nu inom ansvariga försöker Storm distribuera executors jämnt mellan ansvariga, trådar ger varje handledarens (det vill säga JVM) 8 varje.

## <a name="tune-additional-parameters"></a>Finjustera ytterligare parametrar
När du har grundläggande topologi kan överväga du om du vill ändra någon av parametrarna:
* **Antal JVMs per arbetsnoden.** Om du har en stor datastruktur (till exempel en uppslagstabell) kräver en separat kopia av du värden i minnet, varje JVM. Du kan också använda datastrukturen över många trådar om du har färre JVMs. För den bult i/o gör antalet JVMs inte så stor del av en skillnad som antalet trådar som har lagts till i dessa JVMs. För enkelhetens skull är det en bra idé att ha en JVM per worker. Beroende på vad din bult gör eller vilka program som bearbetar du kräva, men du kan behöva ändra det här numret.
* **Antal kanal executors.** Antal kanaler är inte direkt relevant bult prestanda eftersom i föregående exempel använder bultar för att skriva till Data Lake Store. Men, beroende på mängden bearbetning eller i/o som händer i kanal är det en bra idé att finjustera kanaler för bästa prestanda. Se till att du har tillräckligt med kanaler för att kunna hålla bultarna upptagen. Utdata frekvens av kanaler ska matcha genomflödet av bultarna. Den faktiska konfigurationen beror på kanal.
* **Antal uppgifter.** Varje bult körs som en enskild tråd. Ytterligare aktiviteter per bult Ange inte några ytterligare samtidighet. Den enda gång som de är till nytta är om processen för bekräftades tuppeln tar en stor del av din bult körningstid. Det är en bra idé att gruppera många tupplar i en större läggas till innan du skickar en bekräftelse från bulten. Så i de flesta fall ger inga ytterligare fördelar i flera uppgifter.
* **Lokal eller blanda gruppering.** När den här inställningen är aktiverad skickas tupplar till bultar inom samma arbetsprocesser. Detta minskar mellan processer anrop för kommunikation och nätverk. Detta rekommenderas för de flesta topologier.

Det här grundläggande scenariot är en bra utgångspunkt. Testa med dina egna data att justera parametrarna föregående för att uppnå bästa prestanda.

## <a name="tune-the-spout"></a>Finjustera kanal

Du kan ändra följande inställningar för att finjustera kanal.

- **Tuppel timeout: topology.message.timeout.secs**. Den här inställningen avgör hur lång tid ett meddelande tar att slutföra och ta emot bekräftelse, innan den anses misslyckades.

- **Maximalt minne per arbetsprocessen: worker.childopts**. Den här inställningen kan du ange ytterligare parametrar till Java-workers. Den vanligaste inställningen här är XmX som anger den maximala mängd minne som allokerats till en JVM heap.

- **Max kanal väntande: topology.max.spout.pending**. Den här inställningen anger antalet tupplar i kan vara svarta (ännu inte bekräftas på alla noder i topologin) per kanal tråd när som helst.

 En bra beräkning att göra är att beräkna storleken på var och en av dina tupplar. Sedan ta reda på hur mycket minne en kanal tråd. Den totala mängden minne som allokerats till en tråd, dividerat med det här värdet bör ge dig den övre gränsen för högsta antal kanal väntande parametern.

## <a name="tune-the-bolt"></a>Finjustera bulten
När du skriver till Data Lake Store kan du ange en princip för synkronisering (buffert på klientsidan) till 4 MB. Lokaliseraren eller hsync() sedan endast utförs när buffertstorleken är de på det här värdet. Data Lake Store-drivrutinen på worker VM använder automatiskt den här buffring såvida inte du uttryckligen utföra en hsync().

Data Lake Store Storm-bult standard har en storlek sync princip parameter (fileBufferSize) som kan användas för att finjustera den här parametern.

I I/O-intensiva topologier är det en bra idé att varje bult tråd skriva till en egen fil och skapa en princip för filen rotation (fileRotationSize). När filen når en viss storlek, dataströmmen rensas automatiskt och en ny fil ska skrivas till. Den rekommenderade filstorleken för rotation är 1 GB.

### <a name="handle-tuple-data"></a>Hantera tuppel data

I Storm håller en kanal att en tuppel tills tillämpas explicit av bulten. Om en tuppel har lästs av bulten men har inte bekräftats, kan inte kanal beständiga i Data Lake Store-serverdel. När en tuppel bekräftas kanal kan garanteras beständiga av bulten och kan sedan att ta bort datakällan oavsett läser från källan.  

För bästa prestanda på Data Lake Store har bulten bufferten 4 MB tuppel data. Sedan skriva till Data Lake Store tillbaka slutet som en 4 MB skrivåtgärder. När data har skrivits till store (genom att anropa hflush()) bulten kan acceptera data tillbaka till kanal. Detta är det exempel bulten anges här. Det är också tillåtet innehåller ett större antal tupplar innan hflush() anropet görs och tupplar bekräftas. Dock ökar antalet tupplar som rör sig att kanal behöver lagra och ökar mängden minne krävs därför per JVM.

> [!NOTE]
Program kan ha ett krav för att bekräfta tupplar oftare (på datamängder mindre än 4 MB) för andra prestandaskäl. Som kan dock påverka i/o-genomströmning till serverdelen för lagring. Noggrant väga det här förhållandet mot den bult i/o-prestanda.

Om den takt med vilken av tupplar är inte hög så 4 MB bufferten tar lång tid att fylla, bör du åtgärdar detta genom att:
* Minska antalet bultar, så det finns färre buffertar för att fylla.
* Med en tid- eller count-baserad princip, där en hflush() utlöses varje x rensningar eller varje y millisekunder och tupplar ackumulerade hittills är bekräftade tillbaka.

Observera att dataflödet i det här fallet är lägre men med låg hastighet av händelser, maximalt dataflöde kan inte största målet ändå. Dessa åtgärder hjälper dig att minska den totala tiden det tar för en tuppel genomströmning till arkivet. Detta kan roll om du vill använda en realtid pipeline även med en låg händelsehastighet. Även Observera att din inkommande tuppel frekvensen är låg, bör ändra parametern topology.message.timeout_secs så tuppeln inte timeout när de får en buffert eller bearbetas.

## <a name="monitor-your-topology-in-storm"></a>Övervaka topologin i Storm  
När din topologi körs, kan du övervaka den i Storm-användargränssnittet. Här är de huvudsakliga parametrarna för att titta på:

* **Totalt antal processen körning svarstid.** Det här är den genomsnittliga tid en tuppel tar sänds av kanal, bearbetas av bulten och bekräftas.

* **Totalt antal bult processen svarstid.** Det här är den genomsnittliga tid som krävs tuppeln vid bulten tills det mottar en bekräftelse.

* **Totalt antal bult köra svarstid.** Detta är genomsnittstiden användes av bulten i execute-metoden.

* **Antal fel.** Detta avser antalet tupplar som inte kunnat bearbetas fullständigt innan de tidsgränsen.

* **Kapacitet.** Detta är ett mått på hur upptagen systemet är. Om värdet är 1, fungerar lika snabbt som de kan din bultar. Om det är mindre än 1, öka parallellitet. Om den är större än 1, minska parallellitet.

## <a name="troubleshoot-common-problems"></a>Felsöka vanliga problem
Här följer några vanliga scenarier för felsökning.
* **Många tupplar uppnådd tidsgräns.** Titta på varje nod i topologin för att fastställa om orsaken till detta är. Den vanligaste orsaken till detta är att bultarna inte kan Håll dig uppdaterad med kanaler. Detta leder till tupplar belastning interna buffertar väntar på att bearbetas. Överväg att öka timeout-värde eller minskar den maximala kanal väntande.

* **Det finns en hög totala processen körning svarstid, men en låg bult processen fördröjning.** I det här fallet är det möjligt att tuppeln inte är som ska bekräftas tillräckligt snabbt. Kontrollera att det finns ett tillräckligt antal acknowledgers. En annan möjlighet är att de väntar i kö för länge innan bultarna börja bearbeta dem. Minska max kanal väntande.

* **Det finns en hög bult köra svarstid.** Detta innebär att metoden execute() för din bult tar för lång. Optimera kod, eller titta på skrivning storlekar och rensa beteende.

### <a name="data-lake-store-throttling"></a>Begränsning av data Lake Store
Om du klickar på gränserna för bandbredd som tillhandahålls av Data Lake Store, kan du se misslyckade aktiviteter. Uppgiften loggarna för begränsning av fel. Du kan minska parallellitet genom att öka storleken på behållaren.    

Aktivera felsökningsloggning på klientsidan för att kontrollera om du har komma begränsats:

1. I **Ambari** > **Storm** > **Config** > **avancerade storm-worker-log4j**, ändra  **&lt;rot nivå = ”info”&gt;**  till  **&lt;rot nivå = ”debug”&gt;**. Starta om alla noder/för konfigurationen ska börja gälla.
2. Övervaka Storm-topologi som loggar in arbetarnoder (under /var/log/storm/worker-artifacts /&lt;TopologyName&gt;/&lt;port&gt;/worker.log) för Data Lake Store begränsning undantag.

## <a name="next-steps"></a>Nästa steg
Ytterligare prestandajustering för Storm kan refereras i [bloggen](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Ett ytterligare exempel köra finns [denna som finns på GitHub](https://github.com/hdinsight/storm-performance-automation).
