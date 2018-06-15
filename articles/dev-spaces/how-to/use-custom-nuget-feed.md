---
title: Hur du använder en anpassad NuGet feeds i Azure Dev blanksteg | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: article
description: Använda en anpassad NuGet feed för att komma åt och använda NuGet-paket i ett Azure Dev utrymme.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
manager: ghogen
ms.openlocfilehash: 3badd15bcfd09c97b43744a20c5df05f4ff57e84
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199117"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Använd en anpassad NuGet mata in ett utrymme för Azure-utveckling

En NuGet-feed är ett bekvämt sätt att inkludera paket källor i ett projekt. Azure Dev blanksteg måste kunna komma åt feeden för beroenden måste vara korrekt installerat i Docker-behållaren.

## <a name="set-up-a-nuget-feed"></a>Ställ in en NuGet-feed

Att ställa in en NuGet-feed:
1. Lägg till en [paketet referens](https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files) i den `*.csproj` filen den `PackageReference` nod.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. Skapa en [NuGet.Config](https://docs.microsoft.com/en-us/nuget/reference/nuget-config-file) filen i projektmappen.
     * Använd den `packageSources` avsnittet för att referera till din NuGet feed plats. Viktigt: NuGet-feed måste vara allmänt tillgänglig.
     * Använd den `packageSourceCredentials` avsnittet för att konfigurera användarnamn och lösenord för autentiseringsuppgifter. 

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

3. Om du använder källkodskontroll:
    - Referens `NuGet.Config` i din `.gitignore` filen så att du inte oavsiktligt sparar autentiseringsuppgifter till lagringsplatsen för källa.
    - Öppna den `azds.yaml` filen i projektet och leta upp den `build` avsnittet och infoga följande fragment för att säkerställa att den `NuGet.Config` filen kommer att synkroniseras till Azure, så att den används under behållaren build avbildningen. (Normalt Azure Dev blanksteg synkroniseras inte filer som matchar `.gitignore` och `.dockerignore` regler.)

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>Nästa steg

När du har slutfört stegen ovan, nästa gång du kör `azds up` (eller tryck på `F5` i VSCode eller Visual Studio), Azure Dev blanksteg synkroniserar den `NuGet.Config` filen till Azure, som sedan används av `dotnet restore` installera paketet beroenden i behållaren.

