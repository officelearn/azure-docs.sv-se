---
title: Felsöka Azure Service Fabric-appar i Linux
description: Lär dig hur du övervakar och diagnostiserar dina Service Fabric-tjänster på en lokal Linux-utvecklingsdator.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: fa8c4053a348c539c2e9e7a87d002d0fcf4a4d52
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991338"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Övervaka och diagnostisera tjänster i en lokal linux-maskinutvecklingsinstallation


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Övervakning, identifiering, diagnos och felsökning gör det möjligt för tjänster att fortsätta med minimala störningar i användarupplevelsen. Övervakning och diagnostik är avgörande i en faktisk distribuerad produktionsmiljö. Genom att anta en liknande modell under utveckling av tjänster säkerställs att diagnostikpipelinen fungerar när du flyttar till en produktionsmiljö. Service Fabric gör det enkelt för tjänstutvecklare att implementera diagnostik som sömlöst kan fungera både för lokala utvecklingsinställningar för en enda dator och verkliga produktionskluster.


## <a name="debugging-service-fabric-java-applications"></a>Felsöka Java-program för serviceinfrastruktur

För Java-program finns [flera loggningsramverk](https://en.wikipedia.org/wiki/Java_logging_framework) tillgängliga. Eftersom `java.util.logging` är standardalternativet med JRE används det också för [kodexemplen i GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started). Följande diskussion förklarar hur `java.util.logging` du konfigurerar ramverket.

Med java.util.logging kan du omdirigera dina programloggar till minne, utdataströmmar, konsolfiler eller sockets. För vart och ett av dessa alternativ finns det standardhanterare som redan finns i ramverket. Du kan `app.properties` skapa en fil för att konfigurera filhanteraren för att programmet ska omdirigera alla loggar till en lokal fil.

Följande kodavsnitt innehåller en exempelkonfiguration:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

Mappen som `app.properties` filen pekar på måste finnas. När `app.properties` filen har skapats måste du också ändra `entrypoint.sh` startpunktsskriptet `<applicationfolder>/<servicePkg>/Code/` `java.util.logging.config.file` i `app.properties` mappen för att ange egenskapen till filen. Posten ska se ut som följande utdrag:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Den här konfigurationen resulterar i att loggar `/tmp/servicefabric/logs/`samlas in på ett roterande sätt på . Loggfilen i det här fallet heter mysfapp%u.%g.log där:
* **%u** är ett unikt tal för att lösa konflikter mellan samtidiga Java-processer.
* **%g** är det genereringsnummer som ska skilja mellan roterande loggar.

Som standard om ingen hanterare är uttryckligen konfigurerad registreras konsolhanteraren. Man kan visa loggarna i syslog under /var/log/syslog.

Mer information finns [i kodexemplen i GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Felsökning av Program för service fabric C#


Flera ramverk är tillgängliga för att spåra CoreCLR-program på Linux. Mer information finns i [.NET-tillägg för loggning](https://github.com/dotnet/extensions/tree/master/src/Logging).  Eftersom EventSource är bekant för C#-utvecklare använder den här artikeln EventSource för spårning i CoreCLR-exempel på Linux.

Det första steget är att inkludera System.Diagnostics.Tracing så att du kan skriva loggarna till minne, utdataströmmar eller konsolfiler.  För loggning med EventSource lägger du till följande projekt i project.json:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Du kan använda en anpassad EventListener för att lyssna efter tjänsthändelsen och sedan omdirigera dem på rätt sätt till att spåra filer. Följande kodavsnitt visar ett exempel på hur du loggar med EventSource och en anpassad EventListener:


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


Föregående utdrag utdata loggarna till en fil `/tmp/MyServiceLog.txt`i . Det här filnamnet måste uppdateras på rätt sätt. Om du vill omdirigera loggarna till konsolen använder du följande kodavsnitt i den anpassade klassen EventListener:

```csharp
public static TextWriter Out = Console.Out;
```

Exemplen på [C# Samples](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) använder EventSource och en anpassad EventListener för att logga händelser till en fil.



## <a name="next-steps"></a>Nästa steg
Samma spårningskod som läggs till i ditt program fungerar också med diagnostiken av ditt program i ett Azure-kluster. Kolla in de här artiklarna som beskriver de olika alternativen för verktygen och beskriver hur du ställer in dem.
* [Samla in loggar med Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md)
