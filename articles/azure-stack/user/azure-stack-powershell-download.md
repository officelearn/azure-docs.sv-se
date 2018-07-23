---
title: Ladda ned Azure Stack-verktyg från GitHub | Microsoft Docs
description: Lär dig hur du hämtar verktyg som krävs för att arbeta med Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: mabrigg
ms.openlocfilehash: 9c1e4abe50b06db58a4ca05a99e1ae4a531b2294
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187459"
---
# <a name="download-azure-stack-tools-from-github"></a>Ladda ned Azure Stack-verktyg från GitHub

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

AzureStack-verktyg är en GitHub-lagringsplats som är värd för PowerShell-moduler som du kan använda för att hantera och distribuera resurser till Azure Stack.

## <a name="download-targets"></a>Ladda ned mål

Du kan hämta och använda dessa PowerShell-moduler i Azure Stack Development Kit eller till en extern Windows-klient som använder en VPN-anslutning.

## <a name="how-to-get-the-tools"></a>Hämta verktygen

För att få dessa verktyg kan klona AzureStack-verktyg för GitHub-databasen eller ladda ned mappen AzureStack verktyg genom att köra följande skript:

```PowerShell
# Change directory to the root directory
cd \

# Download the tools archive
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>Funktioner som tillhandahålls av moduler

AzureStack-Tools-lagringsplatsen innehåller PowerShell-moduler som har stöd för följande funktioner för Azure Stack:

| Funktioner | Beskrivning | Vem kan använda den här modulen? |
| --- | --- | --- |
| [Molnfunktioner](https://github.com/Azure/AzureStack-Tools/tree/master/CloudCapabilities) | Använd den här modulen för att få molnfunktioner för ett moln. Till exempel får du cloud-funktioner, till exempel API-versionen, Azure Resource Manager-resurser, VM-tillägg osv för Azure Stack och Azure-moln med hjälp av den här modulen. | Cloud-administratörer och användare. |
| [Resource Manager-princip för Azure Stack](azure-stack-policy-module.md) | Använd den här modulen för att konfigurera en Azure-prenumeration eller en Azure-resursgrupp med samma versionshantering och tjänstens tillgänglighet som Azure Stack. | Cloud-administratörer och användare |
| [Ansluta till Azure Stack](azure-stack-connect-azure-stack.md) | Använd den här modulen för att ansluta till en instans för Azure Stack via PowerShell och konfigurera VPN-anslutning till Azure Stack. | Cloud-administratörer och användare |
| [Mall för systemhälsoverifierare](azure-stack-validate-templates.md) | Använd den här modulen för att kontrollera om en befintlig eller en ny mall kan distribueras till Azure Stack. | Cloud-administratörer och användare |

## <a name="next-steps"></a>Nästa steg

- [Konfigurera PowerShell-miljö för Azure Stack-användare](azure-stack-powershell-configure-user.md)
- [Ansluta till Azure Stack Development Kit via en VPN-anslutning](azure-stack-connect-azure-stack.md)
