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
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 05dd3b292f90964c6af21890aaeafab9849a09ed
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242985"
---
# <a name="download-azure-stack-tools-from-github"></a>Ladda ned Azure Stack-verktyg från GitHub

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

**Verktyg för AzureStack** är en [GitHub-lagringsplatsen](https://github.com/Azure/AzureStack-Tools) som är värd för PowerShell-moduler för att hantera och distribuera resurser till Azure Stack. Om du planerar att upprätta en VPN-anslutning, kan du hämta dessa PowerShell-moduler till Azure Stack Development Kit eller till en extern Windows-baserad klient. För att få dessa verktyg kan du klona GitHub-databasen eller ladda ned den **AzureStack verktyg** mapp genom att köra följande skript:

```PowerShell
# Change directory to the root directory. 
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>Funktioner som tillhandahålls av moduler

Den **AzureStack verktyg** lagringsplatsen innehåller PowerShell-moduler som har stöd för följande funktioner för Azure Stack:  

| Funktioner | Beskrivning | Vem kan använda den här modulen? |
| --- | --- | --- |
| [Molnfunktioner](user/azure-stack-validate-templates.md) | Använd den här modulen för att få molnfunktioner för ett moln. Genom att använda den här modulen kan hämta du exempelvis molnfunktioner, till exempel API-versionen och Azure Resource Manager-resurser. Du kan också visa VM-tillägg för Azure Stack och Azure-moln med hjälp av den här modulen. | Molnoperatörer och användare |
| [Resource Manager-princip för Azure Stack](user/azure-stack-policy-module.md) | Använd den här modulen för att konfigurera en Azure-prenumeration eller en Azure-resursgrupp med samma versionshantering och tjänstens tillgänglighet som Azure Stack. | Molnoperatörer och användare |
| [Registrera med Azure](azure-stack-register.md) | Använd den här modulen för att registrera din development kit-instans med Azure. Efter registreringen kan du hämta marketplace-objekt från Azure och använda dem i Azure Stack. | Molnoperatörer |
| [Azure Stack-distribution](azure-stack-run-powershell-script.md) | Använd den här modulen för att förbereda Azure Stack-värddatorn att distribuera och distribuera med hjälp av Azure Stack virtuell hårddisk (VHD)-avbildningen. | Molnoperatörer|
| [Ansluta till Azure Stack](azure-stack-connect-powershell.md) | Använd den här modulen för att konfigurera VPN-anslutning till Azure Stack. | Molnoperatörer och användare |
| [Mall för systemhälsoverifierare](user/azure-stack-validate-templates.md) | Använd den här modulen för att kontrollera om en befintlig eller en ny mall kan distribueras till Azure Stack. | Molnoperatörer och användare|


## <a name="next-steps"></a>Nästa steg
* [Konfigurera PowerShell-miljö för Azure Stack-användare](user/azure-stack-powershell-configure-user.md)   
* [Ansluta till Azure Stack Development Kit via en VPN-anslutning](azure-stack-connect-azure-stack.md)  
