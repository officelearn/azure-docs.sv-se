---
title: Använda en anpassad NuGet-feed i Azure dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Använd en anpassad NuGet-feed för att få åtkomst till och använda NuGet-paket i ett Azure dev-utrymme.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
manager: gwallace
ms.openlocfilehash: 44a87491d276e09e1fa8fed3f5e6803648c3e4a2
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305387"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Använda en anpassad NuGet-feed i ett Azure dev-utrymme

En NuGet-feed är ett bekvämt sätt att inkludera paket källor i ett projekt. Azure dev Spaces måste ha åtkomst till denna feed för att beroenden ska kunna installeras korrekt i Docker-behållaren.

## <a name="set-up-a-nuget-feed"></a>Konfigurera en NuGet-feed

Lägg till en [paket referens](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) för ditt beroende i `*.csproj` filen under `PackageReference` noden. Exempel:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Skapa en [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) -fil i projektmappen och ange `packageSources` avsnitten och `packageSourceCredentials` för din NuGet-feed. `packageSources` Avsnittet innehåller din feed-URL, som måste vara offentligt tillgänglig. `packageSourceCredentials` Är autentiseringsuppgifterna för att komma åt feeden. Exempel:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Uppdatera Dockerfiles för att kopiera `NuGet.Config` filen till avbildningen. Exempel:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> I Windows, `NuGet.Config`, `Nuget.Config`, och `nuget.config` alla fungerar som giltiga fil namn. I Linux är bara `NuGet.Config` ett giltigt fil namn för den här filen. Eftersom Azure dev Spaces använder Docker och Linux måste den här filen namnges `NuGet.Config`. Du kan åtgärda namngivningen manuellt eller genom att `dotnet restore --configfile nuget.config`köra.


Om du använder git bör du inte ha autentiseringsuppgifterna för NuGet-flödet i versions kontrollen. Lägg `NuGet.Config`till i för`NuGet.Config`ditt projekt så att filen inte läggs till i versions kontrollen. `.gitignore` Azure dev Spaces behöver den här filen under build-processen för behållar avbildningen, men som standard uppfyller den `.gitignore` de `.dockerignore` regler som definierats i och under synkroniseringen. Om du vill ändra standard och låta Azure dev Spaces synkronisera `NuGet.Config` filen uppdaterar du `azds.yaml` filen:

```yaml
build:
useGitIgnore: true
ignore:
- “!NuGet.Config”
```

Om du inte använder git kan du hoppa över det här steget.

Nästa gången du kör `azds up` eller träffar `F5` i Visual Studio Code eller Visual Studio kommer Azure `NuGet.Config` dev Spaces att synkronisera filen Använd den för att installera paket beroenden.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [NuGet och hur det fungerar](https://docs.microsoft.com/nuget/what-is-nuget).