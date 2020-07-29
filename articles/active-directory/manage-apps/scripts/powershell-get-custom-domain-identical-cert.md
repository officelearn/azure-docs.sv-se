---
title: PowerShell-exempel – programproxy-appar med identiska certifikat
description: PowerShell-exempel som visar alla Azure Active Directory (Azure AD) Application Proxy-program som publiceras med samma certifikat.
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
ms.openlocfilehash: ef654226d041a7602c03e1ce4670d2f8eea0c2e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75482015"
---
# <a name="get-all-azure-ad-proxy-application-apps-that-are-published-with-the-identical-certificate"></a>Hämta alla appar för Azure AD-proxy som publiceras med samma certifikat

Detta exempel på PowerShell-skript visar alla Azure Active Directory (Azure AD) Application Proxy-program som publiceras med samma certifikat.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Det här exemplet kräver [AzureAD v2 PowerShell för Graph module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) eller [AzureAD v2 PowerShell för för hands versionen](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) av (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-identical-cert.ps1 "Get all Azure AD Proxy application apps published with the identical certificate")]

## <a name="script-explanation"></a>Förklaring av skript

| Kommando | Obs! |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Hämtar ett huvud namn för tjänsten. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Hämtar ett Azure AD-program. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Hämtar ett program konfigurerat för Application Proxy i Azure AD. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [Översikt över Azure AD PowerShell-modulen](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Andra PowerShell-exempel för Application Proxy finns i [Azure AD PowerShell-exempel för azure AD-programproxy](../application-proxy-powershell-samples.md).