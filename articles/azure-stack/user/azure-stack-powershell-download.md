---
title: Hämta Azure Stack-verktyg från GitHub | Microsoft Docs
description: Lär dig hur du hämtar verktyg som krävs för att arbeta med Azure-stacken.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 28F360AD-789A-488D-965F-FC6E6CCF3329
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: a5bc23ee6f986da80630371bafcd8ec80dde3577
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="download-azure-stack-tools-from-github"></a>Hämta Azure Stack-verktyg från GitHub

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

AzureStack verktyg är en GitHub-lagringsplats som är värd för PowerShell-moduler som du kan använda för att hantera och distribuera resurser till Azure-stacken.

## <a name="download-targets"></a>Hämta mål

Du kan hämta och använda dessa PowerShell-moduler i Azure-stacken Development Kit eller till en extern Windows-klient som använder en VPN-anslutning.

## <a name="how-to-get-the-tools"></a>Hur du hämtar verktyg

Om du vill hämta verktygen klona AzureStack verktyg GitHub-lagringsplatsen eller hämta mappen AzureStack verktyg genom att köra följande skript:

```PowerShell
# Change directory to the root directory
cd \

# Download the tools archive
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

AzureStack verktyg databasen innehåller PowerShell-moduler som har stöd för följande funktioner för Azure-Stack:

| Funktioner | Beskrivning | vem som kan använda den här modulen? |
| --- | --- | --- |
| [Molnfunktioner](azure-stack-validate-templates.md) | Använd den här modulen för att få molnfunktioner för ett moln. Exempelvis kan du molnfunktioner, till exempel API-versionen, Azure Resource Manager-resurser, etc. VM-tillägg för Azure-stacken och Azure-moln med hjälp av den här modulen. | Molnet administratörer och användare. |
| [Resource Manager-princip för Azure-stacken](azure-stack-policy-module.md) | Använd den här modulen för att konfigurera en Azure-prenumeration eller ett Azure-resursgrupp med samma version och tjänsten tillgänglighet som Azure-stacken. | Molnet administratörer och användare |
| [Ansluta till Azure-stacken](azure-stack-connect-azure-stack.md) | Använd den här modulen för att ansluta till en Azure-Stack-instans med hjälp av PowerShell och konfigurera VPN-anslutning till Azure-stacken. | Molnet administratörer och användare |
| [Mall för systemhälsoverifierare](azure-stack-validate-templates.md) | Använd den här modulen för att kontrollera om en befintlig eller en ny mall kan distribueras till Azure-stacken. | Molnet administratörer och användare |

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Azure Stack användarens PowerShell-miljö](azure-stack-powershell-configure-user.md)
* [Ansluta till Azure-stacken Development Kit via en VPN-anslutning](azure-stack-connect-azure-stack.md)
