---
title: "Hämta Azure Stack-verktyg från GitHub | Microsoft Docs"
description: "Lär dig hur du hämtar verktyg som krävs för att arbeta med Azure-stacken."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: 69ca030d1b7601df424fa9446e1d194a3f6bd50a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="download-azure-stack-tools-from-github"></a>Hämta Azure Stack-verktyg från GitHub

AzureStack verktyg är en GitHub-lagringsplats som är värd för PowerShell-moduler som du kan använda för att hantera och distribuera resurser till Azure-stacken. Du kan hämta och använda dessa PowerShell-moduler i Azure-stacken Development Kit eller till en extern klient för windows-baserade om du planerar att upprätta en VPN-anslutning. Om du vill hämta verktygen klona GitHub-lagringsplatsen eller hämta mappen AzureStack verktyg. 

Om du vill klona databasen hämta [Git](https://git-scm.com/download/win) för Windows, öppna Kommandotolken och kör följande skript:

```PowerShell
# Change directory to the root directory 
cd \

# clone the repository
git clone https://github.com/Azure/AzureStack-Tools.git --recursive

# Change to the tools directory
cd AzureStack-Tools
```

För att hämta verktygsmappen, kör du följande skript:

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
