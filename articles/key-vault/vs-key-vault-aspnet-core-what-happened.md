---
title: Ändringar som gjorts i en ASP.NET Core projektet när av du Azure Key Vault | Microsoft Docs
description: Beskriver vad som händer i projektet ASP.NET Core när du ansluter toKey valvet med hjälp av Visual Studio anslutna tjänster.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 8b6c590344db2997c1a987da14cabba76cdca83b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787376"
---
# <a name="what-happened-to-my-aspnet-core-project-visual-studio-key-vault-connected-service"></a>Vad hände med ASP.NET Core projektet (Visual Studio Key Vault ansluten service)?

> [!div class="op_single_selector"]
> - [Komma igång](vs-key-vault-aspnet-core-get-started.md)
> - [Vad hände](vs-key-vault-aspnet-core-what-happened.md)

Den här artikeln identifierar exakt ändringar som görs i en ASP.NET-projekt när du lägger till den [Key Vault anslutna service med Visual Studio](vs-key-vault-add-connected-service.md).

Information om hur du arbetar med anslutna tjänsten finns [komma igång](vs-key-vault-aspnet-core-get-started.md).

## <a name="added-references"></a>Tillagd referenserna

Påverkar projektreferenser filen *.NET och NuGet-paketet refererar till.

| Typ | Referens |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

## <a name="added-files"></a>Filerna som läggs till

- ConnectedService.json lagts till, som registrerar information om anslutna tjänsteleverantören, version och en länk i dokumentationen.

## <a name="project-file-changes"></a>Projektet filändringar

- Lägga till filen anslutna tjänster ItemGroup och ConnectedServices.json.

## <a name="launchsettingsjson-changes"></a>launchsettings.JSON ändringar

- Läggs följande miljö variabeln poster till både IIS Express-profil och den profil som matchar din Webbprojektnamn:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

## <a name="changes-on-azure"></a>Ändringar i Azure

- Skapa en resursgrupp (eller använda ett befintligt).
- Skapa ett Nyckelvalv i den angivna resursgruppen.

