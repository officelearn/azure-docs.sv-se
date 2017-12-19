---
title: "Felsöka Azure mikrotjänster i Linux | Microsoft Docs"
description: "Lär dig mer om att övervaka och diagnostisera dina tjänster som skrivits med Microsoft Azure Service Fabric på en dator för lokal utveckling."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 4bc73f581f4855ebc724df19dd56fab8bf103854
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Övervaka och diagnostisera tjänster i en inställning för utveckling av lokal dator


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Övervaka, identifiera, diagnostisera och felsöka Tillåt för tjänster att fortsätta med minimala störningar för användarupplevelsen. Övervaknings- och diagnostikfunktionerna är viktiga i en verklig distribuerade produktionsmiljö. Införandet av en liknande modell under utvecklingen av tjänster garanterar att diagnostiska pipeline fungerar när du flyttar till en produktionsmiljö. Service Fabric gör det enkelt för tjänstutvecklare att implementera diagnostik fungerar sömlöst över både inställningar för enskild dator lokal utveckling och produktion verkliga klustret installationsprogram.


## <a name="debugging-service-fabric-java-applications"></a>Felsökning av Service Fabric Java-program

För Java-program, [flera loggning ramverk](http://en.wikipedia.org/wiki/Java_logging_framework) är tillgängliga. Eftersom `java.util.logging` är standardalternativet med JRE, det används också för de [kodexempel i github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  I följande avsnittet beskriver hur du konfigurerar den `java.util.logging` framework.

Med hjälp av java.util.logging dirigera du dina programloggar till minne utdataströmmar, console-filer eller sockets. Det finns standard hanterare som redan tillhandahålls inom ramen för var och en av dessa alternativ. Du kan skapa en `app.properties` fil att konfigurera Filhanteraren för programmet att omdirigera alla loggar till en lokal fil.

Följande kodavsnitt innehåller en exempelkonfiguration:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

Mappen som pekar på den `app.properties` filen måste finnas. Efter den `app.properties` filen har skapats, måste du också ändra skriptet post punkt `entrypoint.sh` i den `<applicationfolder>/<servicePkg>/Code/` mapp för att ange egenskapen `java.util.logging.config.file` till `app.propertes` filen. Posten bör se ut som följande utdrag:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Den här konfigurationen leder loggar som samlas in i ett roterar sätt på `/tmp/servicefabric/logs/`. Loggfilen i det här fallet heter mysfapp%u.%g.log där:
* **%u** är ett unikt nummer för att lösa konflikter mellan samtidiga Java processer.
* **%g** är antalet generation att skilja mellan rotera loggar.

Som standard om ingen hanterare uttryckligen har konfigurerats i konsolen hanteraren är registrerad. En kan visa loggarna i syslog under /var/log/syslog.

Mer information finns i [kodexempel i github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  


## <a name="debugging-service-fabric-c-applications"></a>Felsöka Service Fabric C#-program


Flera ramverk är tillgängliga för att spåra CoreCLR program på Linux. Mer information finns i [GitHub: loggning](http:/github.com/aspnet/logging).  Eftersom EventSource är bekant för C# utvecklare, som den här artikeln använder EventSource för spårning i CoreCLR prov på Linux.

Det första steget är att inkludera System.Diagnostics.Tracing så att du kan skriva dina loggar till minne, utdataströmmar eller console-filer.  Lägg till följande projektet din project.json för loggning med hjälp av EventSource är:

```
    "System.Diagnostics.StackTrace": "4.0.1"
```

Du kan använda en anpassad EventListener att lyssna efter händelsen tjänsten och sedan på lämpligt sätt dirigera dem till spårningsfiler. Följande kodavsnitt visar ett exempel på implementering av loggning med hjälp av EventSource och anpassade EventListener:


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
         Out.Write(" {0} ",  Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
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


Föregående kodfragment matar ut loggar till en fil i `/tmp/MyServiceLog.txt`. Det här namnet måste uppdateras korrekt. Om du vill omdirigera loggar till konsolen använder du följande kodavsnitt i din anpassade EventListener-klass:

```csharp
public static TextWriter Out = Console.Out;
```

Exempel på [C#-exempel](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) använda EventSource och anpassade EventListener för att logga händelser till en fil.



## <a name="next-steps"></a>Nästa steg
Samma spårning koden som lagts till i ditt program fungerar även med diagnostik för programmet på ett Azure-kluster. Gå igenom dessa artiklar som beskrivs de olika alternativen för verktyg och beskriver hur du ställer in.
* [Hur du samlar in loggar med Azure-diagnostik](service-fabric-diagnostics-how-to-setup-lad.md)
