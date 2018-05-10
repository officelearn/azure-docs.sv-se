---
title: Ändringar som gjorts i en ASP.NET-projekt när av du Azure Key Vault | Microsoft Docs
description: Beskriver vad som händer med ASP.NET-projekt när du ansluter toKey valvet med hjälp av Visual Studio anslutna tjänster.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a43f893c7ee87ffb02179c06ea5786715547e93a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>Vad hände med ASP.NET-projekt (Visual Studio Key Vault ansluten service)?

> [!div class="op_single_selector"]
> - [Komma igång](vs-key-vault-aspnet-get-started.md)
> - [Vad hände](vs-key-vault-aspnet-what-happened.md)

Den här artikeln identifierar exakt ändringar som görs i en ASP.NET-projekt när du lägger till den [Key Vault anslutna service med Visual Studio](vs-key-vault-add-connected-service.md).

Information om hur du arbetar med anslutna tjänsten finns [komma igång](vs-key-vault-aspnet-get-started.md).

## <a name="added-references"></a>Tillagd referenserna

Påverkar projektreferenser filen *.NET och `packages.config` (NuGet referenser).

| Typ | Referens |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>Filerna som läggs till

- ConnectedService.json lagts till, som registrerar information om den anslutna Service providern, version och en länk till dokumentationen.

## <a name="project-file-changes"></a>Projektet filändringar

- Lägga till filen anslutna tjänster ItemGroup och ConnectedServices.json.
- Referenser till .NET-sammansättningar som beskrivs i den [lagts till referenser](#added-references) avsnitt.

## <a name="webconfig-or-appconfig-changes"></a>ändringar i Web.config eller app.config

- Lägga till följande konfiguration:

    ```xml
    <appSettings>
       <add key="vaultName" value="<your Key Vault name>" />
       <add key="vaultUri" value="<the URI to your Key Vault in Azure>" />
    </appSettings>
    ```

## <a name="changes-on-azure"></a>Ändringar i Azure

- Skapa en resursgrupp (eller använda ett befintligt).
- Skapa ett Nyckelvalv i den angivna resursgruppen.

