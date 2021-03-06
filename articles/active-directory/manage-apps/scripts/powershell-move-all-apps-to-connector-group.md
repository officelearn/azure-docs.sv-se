---
title: PowerShell-exempel – flytta Application Proxy-appar till en annan grupp
description: PowerShell-exemplet Azure Active Directory (Azure AD) Application Proxy som används för att flytta alla program som för närvarande är kopplade till en anslutnings grupp till en annan kopplings grupp.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: a2215b3de6c6ad1286570dbbf168d93a7ed62b94
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861586"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>Flytta alla appar som är kopplade till en anslutnings grupp till en annan anslutnings grupp

Det här exemplet på PowerShell-skript flyttar alla Azure Active Directory (Azure AD) Application Proxy-program som för närvarande är kopplade till en anslutnings grupp till en annan kopplings grupp.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Det här exemplet kräver [AzureAD v2 PowerShell för Graph module](/powershell/azure/active-directory/install-adv2) (AzureAD) eller [AzureAD v2 PowerShell för för hands versionen](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) av (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>Förklaring av skript

| Kommando | Kommentarer |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | Hämtar ett huvud namn för tjänsten. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Hämtar ett Azure AD-program. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | Hämtar en lista över alla kopplings grupper, eller om den anges, information om den angivna anslutnings gruppen. |
| [Set-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup) | Tilldelar den angivna kopplings gruppen till ett angivet program.|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [Översikt över Azure AD PowerShell-modulen](/powershell/azure/active-directory/overview).

Andra PowerShell-exempel för Application Proxy finns i [Azure AD PowerShell-exempel för azure AD-programproxy](../application-proxy-powershell-samples.md).
