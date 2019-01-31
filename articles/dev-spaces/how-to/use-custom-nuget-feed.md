---
title: Hur du använder en anpassad NuGet flöde i Azure Dev blanksteg | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: article
description: Använd en anpassad NuGet flödet för att komma åt och använda NuGet-paket i ett adressutrymme för utveckling av Azure.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
manager: ghogen
ms.openlocfilehash: 21a70100fe186e176dfe8eb7c247d83a5d4705bd
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466412"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Använd en anpassad NuGet flöde i ett adressutrymme för Azure-utveckling

En NuGet-feed är ett enkelt sätt att inkludera paketkällorna i ett projekt. Azure Dev blanksteg måste kunna komma åt den här feeden för beroenden måste vara korrekt installerat i Docker-behållare.

## <a name="set-up-a-nuget-feed"></a>Konfigurera ett NuGet-flöde

Så här skapar ett NuGet feed:
1. Lägg till en [paketera referens](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) i den `*.csproj` filen under den `PackageReference` noden.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. Skapa en [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) filen i projektmappen.
     * Använd den `packageSources` avsnitt för att referera till din NuGet feed plats. Viktigt: NuGet-feed måste vara tillgänglig för allmänheten.
     * Använd den `packageSourceCredentials` avsnitt för att konfigurera autentiseringsuppgifter för användarnamn och lösenord. 

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
    - Referens `NuGet.Config` i din `.gitignore` -fil så att du inte av misstag sparar autentiseringsuppgifter till centrallagret.
    - Öppna den `azds.yaml` i ditt projekt och leta upp den `build` avsnittet och infoga följande kodfragment för att säkerställa att den `NuGet.Config` filen kommer att synkroniseras till Azure så att den har använts under skapandeprocessen för behållaren bild. (Som standard Azure Dev blanksteg synkroniserar inte filer som matchar `.gitignore` och `.dockerignore` regler.)

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>Nästa steg

När du har slutfört stegen ovan, nästa gång du kör `azds up` (eller tryck på `F5` i VSCode eller Visual Studio), Azure Dev blanksteg ska synkronisera den `NuGet.Config` filen till Azure, som sedan används av `dotnet restore` att installera paketet beroenden i behållaren.

