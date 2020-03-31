---
title: PowerShell-exempel - Lista programproxy-appar med jokertecken
description: PowerShell-exempel som visar alla Azure Active Directory (Azure AD) Application Proxy-program som använder jokertecken.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8149e9230de3e8b6c18d150c81ea67b1d861746c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482028"
---
# <a name="get-all-application-proxy-apps-using-wildcard-publishing"></a>Hämta alla Programproxy-appar med jokerteckenpublicering

I det här PowerShell-skriptexempelet visas alla Azure Active Directory-programproxyprogram (Azure AD) som använder jokerteckenpublicering.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Det här exemplet kräver [AzureAD V2 PowerShell for Graph-modulen](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) eller [AzureAD V2 PowerShell for Graph-modulförhandsgranskningsversionen](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-wildcard-apps.ps1 "Get all Application Proxy apps using wildcards")]

## <a name="script-explanation"></a>Förklaring av skript

| Kommando | Anteckningar |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Får en tjänst huvudman. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Hämtar ett Azure AD-program. |
|[Hämta AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Hämtar ett program som konfigurerats för programproxy i Azure AD. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [översikt över Azure AD PowerShell-modul](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Andra PowerShell-exempel för programproxy finns i [Azure AD PowerShell-exempel för Azure AD Application Proxy](../application-proxy-powershell-samples.md).