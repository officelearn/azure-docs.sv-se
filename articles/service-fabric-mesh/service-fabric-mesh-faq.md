---
title: Vanliga frågor för Azure Service Fabric nät | Microsoft Docs
description: Läs mer om vanliga frågor och svar för Azure Service Fabric-nät.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 06/25/2018
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 849f8e4f961bcfde2bb4984185d571601060d957
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076663"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Vanliga frågor och svar om Service Fabric-nät
Azure Service Fabric-nät är en fullständigt hanterad tjänst som ger utvecklare möjlighet att distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring och nätverk. Den här artikeln innehåller svar på vanliga frågor.

## <a name="quota-and-cost"></a>Kvoter och kostnad

**Vad är kostnaden för att delta i förhandsgranskningen?**

Det finns inga avgifter för att distribuera program eller behållare till nät preview. Men körs du rekommenderas att ta bort de resurser som du distribuerar och inte låta dem vara, om du inte aktivt testar den.

**Finns det en kvotgräns på antalet kärnor och RAM-minne?**

Ja, kvoter för varje prenumeration är:

- Antal program - 5 
- Antalet kärnor per program – 12 
- Totalt RAM-minne per program – 48 GB 
- Antalet slutpunkter nätverks- och Ingångsanspråk – 5  
- Antalet Azure volymer som du kan koppla - 10 
- Antal repliker Service – 3 
- Den största behållare som du kan distribuera är begränsad till 4 kärnor, 16 GB RAM-minne.
- Du kan allokera partiella kärnor till dina behållare i steg om 0,5 kärnor upp till högst 6 kärnor.

**Kan jag lämna mitt program som körs över natten?**

Ja, du kan, men du uppmanas att ta bort alla resurser som du distribuerar och inte låta dem vara körs, om du inte aktivt testar den. Den här principen kan ändras i framtiden och resurserna som kan tas bort om de är missbruk.

## <a name="supported-container-os-images"></a>Stöds behållaravbildningar OS
Följande behållare OS-avbildningar kan användas när du distribuerar tjänster.

- Windows - windowsservercore och nanoserver
    - Windows Server 2016
    - Windows Server version 1709
- Linux
    - Inga kända begränsningar

## <a name="features-gaps-and-known-issues"></a>Funktioner luckor och kända problem

**När du har distribuerat mitt program, verkar den nätverksresurs som är associerade med det inte en IP-adress**

Det finns ett känt problem i dag med IP-adressen som följer efter en fördröjning. Kontrollera status för nätverksresursen på några minuter att se den associera IP-adressen.

**Mitt program inte kan få åtkomst till rätt nätverk/volym-resursen**

I din programmodell som du behöver använda fullständigt resurs-ID för nätverk och volymer för att kunna komma åt den associerade resursen. Här är hur detta ser ut i Snabbstart-exemplet:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

**Jag ser inte den aktuella programmodellen stöder ett sätt att kryptera min hemligheter**

Ja, kryptera hemligheter stöds inte i den aktuella privata förhandsvisningen. 

**DNS fungerar inte på samma sätt i min Service Fabric-kluster för utveckling och nät**

Det finns ett känt problem där du kan behöva referera till tjänster på olika sätt i det lokala utvecklingsklustret och i Azure-nät. I det lokala utvecklingsklustret Använd {serviceName}. {applicationName}. I Azure Service Fabric nät, använder du {servicename}. Azure nät stöder för närvarande inte dns-matchning mellan program.

Andra kända DNS-problem med att köra ett Service Fabric-kluster för utveckling i Windows 10 finns här: [felsöka Windows-behållare](/azure/service-fabric/service-fabric-how-to-debug-windows-containers).

**Det här felet uppstår när med hjälp av CLI-modulen _ImportError: Det går inte att importera namn sdk_no_wait**

Om du använder äldre CLI-version än 2.0.30, kan du få detta fel-

```
cannot import name 'sdk_no_wait'
Traceback (most recent call last):
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\cli.py", line 193, in invoke cmd_result = self.invocation.execute(args)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 241, in execute self.commands_loader.load_arguments(command)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 201, in load_arguments self.command_table[command].load_arguments() # this loads the arguments via reflection
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core\commands_init_.py", line 142, in load_arguments super(AzCliCommand, self).load_arguments()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\knack\commands.py", line 76, in load_arguments cmd_args = self.arguments_loader()
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 332, in default_arguments_loader op = handler or self.get_op_handler(operation)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\azure\cli\core_init_.py", line 375, in get_op_handler op = import_module(mod_to_import)
File "C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\importlib_init_.py", line 126, in import_module return _bootstrap._gcd_import(name[level:], package, level)
File "", line 978, in _gcd_import
File "", line 961, in _find_and_load
File "", line 950, in _find_and_load_unlocked
File "", line 655, in _load_unlocked
File "", line 678, in exec_module
File "", line 205, in _call_with_frames_removed
File "C:\Users\annayak.azure\cliextensions\azure-cli-sbz\azext_sbz\custom.py", line 18, in 
from azure.cli.core.util import get_file_json, shell_safe_json_parse, sdk_no_wait
ImportError: cannot import name 'sdk_no_wait'.
```

**Jag felmeddelandet matchningsfel distribution namn när du installerar CLI-tillägg-paketet**

Detta innebär inte att tillägget inte har installerats. Du bör fortfarande att kunna använda CLI-kommandon utan problem.

**När jag skalar ut se att alla behållare som påverkas, inklusive min som körs som**

Detta är ett fel och bör åtgärdas i nästa körning uppdatering.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Service Fabric-nät kan läsa den [översikt](service-fabric-mesh-overview.md).
