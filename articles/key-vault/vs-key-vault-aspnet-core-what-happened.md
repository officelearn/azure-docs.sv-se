---
title: Ändringar som gjorts i en ASP.NET Core-projektet när maskerats till Azure Key Vault | Microsoft Docs
description: Beskriver vad som händer med din ASP.NET Core-projekt när du ansluter toKey valvet med hjälp av Visual Studio-anslutna tjänster.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: b7cbe55fa3a524965e0ebc16c5ff350a60d6e440
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055838"
---
# <a name="what-happened-to-my-aspnet-core-project-visual-studio-key-vault-connected-service"></a>Vad hände med mitt ASP.NET Core-projekt (Visual Studio Key Vault-ansluten service)?

> [!div class="op_single_selector"]
> - [Komma igång](vs-key-vault-aspnet-core-get-started.md)
> - [Vad hände](vs-key-vault-aspnet-core-what-happened.md)

Den här artikeln identifierar exakt ändringar som görs i en ASP.NET-projekt när du lägger till den [Key Vault ansluten service med Visual Studio](vs-key-vault-add-connected-service.md).

Information om hur du arbetar med anslutna tjänsten finns i [komma igång](vs-key-vault-aspnet-core-get-started.md).

## <a name="added-references"></a>Har lagts till referenser

Påverkar projektreferenserna filen *.NET och NuGet-paketet refererar till.

| Typ | Referens |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

## <a name="added-files"></a>Filer som har lagts till

- ConnectedService.json har lagts till som registrerar information om Connected Service-providern, version och en länk i dokumentationen.

## <a name="project-file-changes"></a>Projektet filändringar

- Lagt till anslutna tjänster ItemGroup och ConnectedServices.json-fil.

## <a name="launchsettingsjson-changes"></a>launchsettings.JSON ändringar

- Lägga till följande variabel miljö poster i både IIS Express-profilen och den profil som motsvarar ditt webb-projektnamn:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

## <a name="changes-on-azure"></a>Ändringar i Azure

- Skapade resursgruppen (eller använda en befintlig).
- Skapat ett Nyckelvalv i den angivna resursgruppen.

