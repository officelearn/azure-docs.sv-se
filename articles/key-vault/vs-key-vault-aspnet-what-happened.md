---
title: Ändringar som gjorts i en ASP.NET-projekt när maskerats till Azure Key Vault | Microsoft Docs
description: Beskriver vad som händer med din ASP.NET-projekt när du ansluter toKey valvet med hjälp av Visual Studio-anslutna tjänster.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a74e4e10681f6b91e028067d8985408b0745dcd2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42058224"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>Vad hände med mitt ASP.NET-projekt (Visual Studio Key Vault-ansluten service)?

> [!div class="op_single_selector"]
> - [Komma igång](vs-key-vault-aspnet-get-started.md)
> - [Vad hände](vs-key-vault-aspnet-what-happened.md)

Den här artikeln identifierar exakt ändringar som görs i en ASP.NET-projekt när du lägger till den [Key Vault ansluten service med Visual Studio](vs-key-vault-add-connected-service.md).

Information om hur du arbetar med anslutna tjänsten finns i [komma igång](vs-key-vault-aspnet-get-started.md).

## <a name="added-references"></a>Har lagts till referenser

Påverkar projektreferenserna filen *.NET och `packages.config` (NuGet referenser).

| Typ | Referens |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>Filer som har lagts till

- ConnectedService.json har lagts till som registrerar information om Connected Service-providern, version och en länk till dokumentationen.

## <a name="project-file-changes"></a>Projektet filändringar

- Lagt till anslutna tjänster ItemGroup och ConnectedServices.json-fil.
- Referenser till .NET-sammansättningar som beskrivs i den [har lagts till referenser](#added-references) avsnittet.

## <a name="webconfig-or-appconfig-changes"></a>Web.config eller app.config ändringar

- Lägga till följande konfiguration:

    ```xml
    <appSettings>
       <add key="vaultName" value="<your Key Vault name>" />
       <add key="vaultUri" value="<the URI to your Key Vault in Azure>" />
    </appSettings>
    ```

## <a name="changes-on-azure"></a>Ändringar i Azure

- Skapade resursgruppen (eller använda en befintlig).
- Skapat ett Nyckelvalv i den angivna resursgruppen.

