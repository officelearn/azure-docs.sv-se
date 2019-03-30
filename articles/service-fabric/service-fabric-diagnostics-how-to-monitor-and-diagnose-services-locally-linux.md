---
title: Felsöka Azure Service Fabric-appar i Linux | Microsoft Docs
description: Lär dig mer om att övervaka och diagnostisera dina Service Fabric-tjänster på en lokal Linux-utvecklingsdator.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: f0b850038a29dd0949def97b359b2b7a5ce920bc
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58659754"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Övervaka och diagnostisera tjänster i en inställning för utveckling av lokal dator


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Övervakning, identifiera, diagnostisera och felsöka kan efter tjänster att fortsätta med minimala störningar för användarupplevelsen. Övervakning och diagnostik är viktiga i en faktisk distribuerade produktionsmiljö. Börja använda en liknande modell under utvecklingen av tjänster garanterar att diagnostiska pipelinen fungerar när du flyttar till en produktionsmiljö. Service Fabric gör det enkelt för tjänstutvecklare att implementera diagnostik som fungerar sömlöst både enkel dator lokal utveckling inställningar och inställningar för verklig produktion-kluster.


## <a name="debugging-service-fabric-java-applications"></a>Felsöka Service Fabric Java-program

För Java-program, [flera loggningsramverk](https://en.wikipedia.org/wiki/Java_logging_framework) är tillgängliga. Eftersom `java.util.logging` är standardalternativet med JRE, det används också för de [kodexempel i GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started). Följande information beskriver hur du konfigurerar den `java.util.logging` framework.

Du kan använda java.util.logging för att omdirigera programloggarna minne, utdataströmmar, konsolfiler eller sockets. Det finns standard hanterare som redan ges inom ramen för var och en av dessa alternativ. Du kan skapa en `app.properties` filen för att konfigurera Hanteraren för filen för ditt program att omdirigera alla loggar till en lokal fil.

Följande kodfragment innehåller ett exempel på konfiguration:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

Mappen som pekar på den `app.properties` filen måste finnas. Efter den `app.properties` fil skapas, måste du också ändra din startpunktsskriptet `entrypoint.sh` i den `<applicationfolder>/<servicePkg>/Code/` mappen för att ange egenskapen `java.util.logging.config.file` till `app.properties` fil. Posten bör se ut som följande fragment:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Den här konfigurationen leder loggarna som samlas in på ett roterande sätt på `/tmp/servicefabric/logs/`. Loggfilen i det här fallet heter mysfapp%u.%g.log där:
* **%u** är ett unikt nummer du löser konflikter mellan samtidiga Java-processer.
* **%g** är antalet generation att skilja mellan rotera loggar.

Som standard om någon hanterare uttryckligen har konfigurerats i konsolen hanteraren är registrerad. En kan visa loggarna i syslog under /var/log/syslog.

Mer information finns i den [kodexempel i GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Felsöka Service Fabric C#-program


Flera ramverk är tillgängliga för spårning av CoreCLR program på Linux. Mer information finns i [GitHub: loggning](http:/github.com/aspnet/logging).  Eftersom EventSource är välkänd för C#-utvecklare ”, den här artikeln använder EventSource för spårning på CoreCLR-exempel på Linux.

Det första steget är att inkludera System.Diagnostics.Tracing så att du kan skriva dina loggar till minne, utdataströmmar eller konsolfiler.  Lägg till följande projektet i din project.json för loggning med EventSource är:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Du kan använda en anpassad EventListener att lyssna efter händelsen tjänst och sedan på lämpligt sätt dirigera dem till spårningsfiler. Följande kodavsnitt visar ett exempel på implementering av loggning använder EventSource och en anpassad EventListener:


```csharp

public class ServiceEventSource : EventSource
{
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
internal class ServiceEventListener : EventListener
{

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
                using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))
                {
                        // report all event information
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                        if (eventData.Message != null)
                                Out.WriteLine(eventData.Message, eventData.Payload.ToArray());
                        else
                        {
                                string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                                Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");
                        }
                }
        }
}
```


Föregående fragment matar ut loggarna till en fil i `/tmp/MyServiceLog.txt`. Det här filnamnet måste uppdateras på rätt sätt. Om du vill omdirigera loggar till konsolen kan du använda följande kodavsnitt i din anpassade EventListener klass:

```csharp
public static TextWriter Out = Console.Out;
```

Exempel på [C#-exempel](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) använda EventSource och en anpassad EventListener för att logga händelser till en fil.



## <a name="next-steps"></a>Nästa steg
Samma spårning av kod som lagts till i ditt program fungerar även med diagnostik för ditt program på ett Azure-kluster. Läs dessa artiklar som beskrivs de olika alternativen för verktyg och beskriver hur du konfigurerar dem.
* [Samla in loggar med Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md)
