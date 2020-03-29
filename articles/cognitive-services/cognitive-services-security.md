---
title: Säkerhet
titleSuffix: Azure Cognitive Services
description: Läs mer om de olika säkerhetsövervägandena för användning av Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: c86d806c408c2e8226e632a0b15e1e8729c987f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131540"
---
# <a name="azure-cognitive-services-security"></a>Azure Cognitive Services-säkerhet

Säkerhet bör anses vara högsta prioritet när du utvecklar alla program. Med uppkomsten av artificiell intelligens aktiverade applikationer, säkerhet är ännu viktigare. I den här artikeln beskrivs olika aspekter av Azure Cognitive Services-säkerhet, till exempel användning av säkerhet i transportlager, autentisering och korrekt konfigurera känsliga data.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Alla Cognitive Services-slutpunkter som exponeras via HTTP framtvingar TLS 1.2. Med ett tillämpat säkerhetsprotokoll bör konsumenter som försöker anropa en Cognitive Services-slutpunkt följa dessa riktlinjer:

* Klientoperativsystemet (OS) måste stödja TLS 1.2
* Språket (och plattformen) som används för att ringa HTTP-anropet måste ange TLS 1.2 som en del av begäran
  * Beroende på språk och plattform görs det antingen implicit eller explicit att ange TLS

För .NET-användare bör du beakta <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">metodtipsen <span class="docon docon-navigate-external x-hidden-focus"> </span>för transportlagersäkerhet </a>.

## <a name="authentication"></a>Autentisering

När du diskuterar autentisering finns det flera vanliga missuppfattningar. Autentisering och auktorisering är ofta förvirrade för varandra. Identitet är också en viktig komponent i säkerheten. En identitet är en samling information om ett <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">huvudnamn <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. Identitetsleverantörer (IdP) tillhandahåller identiteter till autentiseringstjänster. Autentisering är handlingen att verifiera en användares identitet. Auktorisering är specifikationen av åtkomsträttigheter och privilegier till resurser för en viss identitet. Flera av Cognitive Services-erbjudandena inkluderar rollbaserad åtkomstkontroll (RBAC). RBAC skulle kunna användas för att förenkla en del av ceremonin som arbetar med att manuellt hantera huvudmän. Mer information finns i [rollbaserad åtkomstkontroll för Azure-resurser](../role-based-access-control/overview.md).

Mer information om autentisering med prenumerationsnycklar, åtkomsttoken och Azure Active Directory (AAD) finns <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">i autentisera<span class="docon docon-navigate-external x-hidden-focus"></span>begäranden till Azure Cognitive Services</a>.

## <a name="environment-variables-and-application-configuration"></a>Miljövariabler och programkonfiguration

Miljövariabler är namnvärdespar som lagras i en viss miljö. Ett säkrare alternativ till att använda hårdkodade värden för känsliga data är att använda miljövariabler. Hårdkodade värden är osäkra och bör undvikas.

> [!CAUTION]
> Använd **inte** hårdkodade värden för känsliga data, och gör det är ett stort säkerhetsproblem.

> [!NOTE]
> Medan miljövariabler lagras i oformaterad text, är de isolerade till en miljö. Om en miljö äventyras, så är också variablerna med miljön.

### <a name="set-environment-variable"></a>Ange miljövariabel

Om du vill ange miljövariabler använder `ENVIRONMENT_VARIABLE_KEY` du ett av `value` följande kommandon – där den är den namngivna nyckeln och är det värde som lagras i miljövariabeln.

# <a name="command-line"></a>[Kommandorad](#tab/command-line)

Skapa och tilldela en miljövariabel med beständig, med tanke på värdet.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

I en ny instans av **Kommandotolken**läser du miljövariabeln.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Skapa och tilldela en miljövariabel med beständig, med tanke på värdet.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

I en ny instans av **Windows PowerShell**läser du miljövariabeln.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Skapa och tilldela en miljövariabel med beständig, med tanke på värdet.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

I en ny instans av **Bash**, läs miljövariabeln.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> När du har ställt in en miljövariabel startar du om den integrerade utvecklingsmiljön (IDE) för att säkerställa att nyligen tillagda miljövariabler är tillgängliga.

### <a name="get-environment-variable"></a>Hämta miljövariabel

För att få en miljövariabel måste den läsas in i minnet. Tänk på följande kodavsnitt beroende på vilket språk du använder. Dessa kodavsnitt visar hur du hämtar miljövariabeln med tanke på `ENVIRONMENT_VARIABLE_KEY` och tilldelar en variabel med namnet `value`.

# <a name="c"></a>[C#](#tab/csharp)

Mer information finns <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>i .

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

Mer information finns <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>i .

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

Mer information finns <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>i .

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

Mer information finns <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>i .

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Mer information finns <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>i .

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

Mer information finns <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>i .

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="next-steps"></a>Nästa steg

* Utforska de olika [kognitiva tjänsterna](welcome.md)
* Läs mer om [virtuella nätverk för Cognitive Services](cognitive-services-virtual-networks.md)
