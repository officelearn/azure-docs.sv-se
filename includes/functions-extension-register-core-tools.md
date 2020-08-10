---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: 2936d22eacef73daef4433b3fd296dd4757fa410
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031139"
---
Om du inte kan använda paket för tillägg kan du använda Azure Functions Core Tools lokalt för att installera de speciella tilläggs paket som krävs för ditt projekt. 

> [!NOTE]
> Om du vill installera tillägg manuellt med hjälp av kärn verktyg måste du ha installerat .NET Core 2. x SDK.

När du uttryckligen installerar tillägg läggs en .NET-projekt fil med namnet Extensions. CSPROJ till i projektets rot. Den här filen definierar den uppsättning NuGet-paket som krävs av dina funktioner. Även om du kan arbeta med [NuGet-paketets referenser](/nuget/consume-packages/package-references-in-project-files) i den här filen kan du installera tillägg utan att behöva redigera filen manuellt.

Det finns flera sätt att använda kärn verktyg för att installera de nödvändiga tilläggen i det lokala projektet. 

#### <a name="install-all-extensions"></a>Installera alla tillägg 

Använd följande kommando för att automatiskt lägga till alla tilläggs paket som används av bindningarna i det lokala projektet:

```dotnetcli
func extensions install
```
Kommandot läser *function.jspå* fil för att se vilka paket du behöver, installerar dem och återkonstruerar tilläggs projektet (Extensions. CSPROJ). Den lägger till eventuella nya bindningar i den aktuella versionen men uppdaterar inte befintliga bindningar. Använd `--force` alternativet för att uppdatera befintliga bindningar till den senaste versionen när du installerar nya.

#### <a name="install-a-specific-extension"></a>Installera ett angivet tillägg

Använd följande kommando för att installera ett särskilt tilläggs paket i en speciell version, i det här fallet lagrings tillägget:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```