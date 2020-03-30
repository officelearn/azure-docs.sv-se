---
title: PowerShell-exempel - Lista alla programproxykopplingsgrupper
description: PowerShell-exempel som visar alla Azure Active Directory (Azure AD) Application Proxy-anslutningsgrupper och kopplingar i din katalog.
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
ms.openlocfilehash: 53af7f3f9049edc23ed4226f43e7fe67398c29f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482119"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Hämta alla kopplingsgrupper och kopplingar i katalogen

I det här PowerShell-skriptexempelet visas alla Azure Active Directory (Azure AD) Application Proxy-anslutningsgrupper och kopplingar i katalogen.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Det här exemplet kräver [AzureAD V2 PowerShell for Graph-modulen](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) eller [AzureAD V2 PowerShell for Graph-modulförhandsgranskningsversionen](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Förklaring av skript

| Kommando | Anteckningar |
|---|---|
| [Hämta AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Hämtar en lista över alla kopplingsgrupper, eller om det anges, information om den angivna kopplingsgruppen. |
| [Få-AzureADApplicationProxyConnectorGroupMedlemmar](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers?view=azureadps-2.0) | Hämtar alla Application Proxy-kopplingar som är associerade med varje kopplingsgrupp.|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [översikt över Azure AD PowerShell-modul](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Andra PowerShell-exempel för programproxy finns i [Azure AD PowerShell-exempel för Azure AD Application Proxy](../application-proxy-powershell-samples.md).