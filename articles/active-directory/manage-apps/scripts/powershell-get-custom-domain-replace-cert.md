---
title: PowerShell-exempel - Ersätt certifikat i programproxyappar
description: PowerShell-exempel som massutbytar ersätter ett certifikat i Azure Active Directory (Azure AD) Application Proxy-program.
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
ms.openlocfilehash: 91aafe00856f37e9d7e6b3babafecd2cbdf5f856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482002"
---
# <a name="get-all-application-proxy-applications-published-with-the-identical-certificate-and-replace-it"></a>Få alla Application Proxy-program publicerade med samma certifikat och ersätta det

Med det här PowerShell-skriptexemplet kan du ersätta certifikatet i grupp för alla Azure Active Directory -programproxyprogram (Azure AD) som publiceras med identiska certifikat.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Det här exemplet kräver [AzureAD V2 PowerShell for Graph-modulen](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) eller [AzureAD V2 PowerShell for Graph-modulförhandsgranskningsversionen](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-replace-cert.ps1 "Get all Application Proxy applications published with the identical certificate and replace it")]

## <a name="script-explanation"></a>Förklaring av skript

| Kommando | Anteckningar |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Får en tjänst huvudman. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Hämtar ett Azure AD-program. |
|[Hämta AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Hämtar ett program som konfigurerats för programproxy i Azure AD. |
|[Set-AzureADApplicationProxyApplicationCustomDomainCertificate](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationcustomdomaincertificate?view=azureadps-2.0) | Tilldelar ett certifikat till ett program som konfigurerats för programproxy i Azure AD. Det här kommandot överför certifikatet och gör att programmet kan använda anpassade domäner. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [översikt över Azure AD PowerShell-modul](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Andra PowerShell-exempel för programproxy finns i [Azure AD PowerShell-exempel för Azure AD Application Proxy](../application-proxy-powershell-samples.md).