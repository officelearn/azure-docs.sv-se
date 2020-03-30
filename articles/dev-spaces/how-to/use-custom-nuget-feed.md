---
title: Använda en anpassad NuGet-feed
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Använd en anpassad NuGet-feed för att komma åt och använda NuGet-paket i ett Azure Dev Space.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325729"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Använda en anpassad NuGet-feed med Azure Dev Spaces

En NuGet-feed är ett bekvämt sätt att inkludera paketkällor i ett projekt. Azure Dev Spaces måste komma åt den här feeden för att beroenden ska kunna installeras korrekt i Docker-behållaren.

## <a name="set-up-a-nuget-feed"></a>Konfigurera en NuGet-feed

Lägg till en [paketreferens](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) för `*.csproj` ditt `PackageReference` beroende i filen under noden. Ett exempel:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Skapa en [NuGet.Config-fil](https://docs.microsoft.com/nuget/reference/nuget-config-file) i projektmappen och ange avsnitten `packageSources` och `packageSourceCredentials` för NuGet-flödet. Avsnittet `packageSources` innehåller din feed-url, som måste vara tillgänglig från AKS-klustret. Autentiseringsuppgifterna `packageSourceCredentials` för åtkomst till flödet. Ett exempel:

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

Uppdatera Dockerfiles för `NuGet.Config` att kopiera filen till bilden. Ett exempel:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> I Windows `NuGet.Config` `Nuget.Config`fungerar `nuget.config` , och alla som giltiga filnamn. På Linux `NuGet.Config` är endast ett giltigt filnamn för den här filen. Eftersom Azure Dev Spaces använder Docker och `NuGet.Config`Linux måste den här filen heta . Du kan åtgärda namngivningen manuellt `dotnet restore --configfile nuget.config`eller genom att köra .


Om du använder Git bör du inte ha autentiseringsuppgifterna för NuGet-feeden i versionskontroll. Lägg `NuGet.Config` till `.gitignore` för projektet så `NuGet.Config` att filen inte läggs till i versionskontrollen. Azure Dev Spaces behöver den här filen under processen för att skapa behållaravbildning, men som standard respekterar den de regler som definierats i `.gitignore` och `.dockerignore` under synkroniseringen. Om du vill ändra standardinställningen och `NuGet.Config` låta Azure `azds.yaml` Dev Spaces synkronisera filen uppdaterar du filen:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Om du inte använder Git kan du hoppa över det här steget.

Nästa gång du `azds up` kör `F5` eller träffar i Visual Studio Code eller `NuGet.Config` Visual Studio synkroniserar Azure Dev Spaces filen som använder den för att installera paketberoenden.

## <a name="next-steps"></a>Nästa steg

Läs mer om [NuGet och hur det fungerar](https://docs.microsoft.com/nuget/what-is-nuget).