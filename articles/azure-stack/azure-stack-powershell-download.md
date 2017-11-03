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
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: c0a4f337c055f4b62d986e2a3c3ce7b962aceae9
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2017
---
# <a name="download-azure-stack-tools-from-github"></a>Hämta Azure Stack-verktyg från GitHub

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

**AzureStack verktyg** är en GitHub-lagringsplats som är värd för PowerShell-moduler för att hantera och distribuera resurser till Azure-stacken. Om du planerar att upprätta en VPN-anslutning, kan du hämta dessa PowerShell-moduler i Azure-stacken Development Kit eller till en extern klient för Windows-baserade. Om du vill hämta verktygen klona GitHub-lagringsplatsen eller hämta den **AzureStack verktyg** mapp. 

Om du vill klona databasen hämta [Git för Windows](https://git-scm.com/download/win), öppna en kommandotolk och kör följande skript:

```PowerShell
# Change directory to the root directory. 
cd \

# Clone the repository.
git clone https://github.com/Azure/AzureStack-Tools.git --recursive

# Change to the tools directory.
cd AzureStack-Tools
```

För att hämta verktygsmappen, kör du följande skript:

```PowerShell
# Change directory to the root directory. 
cd \

# Download the tools archive.
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

## <a name="functionality-provided-by-the-modules"></a>Funktionalitet som tillhandahålls av moduler

Den **AzureStack verktyg** databasen innehåller PowerShell-moduler som har stöd för följande funktioner för Azure-Stack:  

| Funktioner | Beskrivning | vem som kan använda den här modulen? |
| --- | --- | --- |
| [Molnfunktioner](user/azure-stack-validate-templates.md) | Använd den här modulen för att få molnfunktioner för ett moln. Till exempel med hjälp av den här modulen kan du molnfunktioner, till exempel API-versionen och Azure Resource Manager-resurser. Du kan också hämta VM-tillägg för Azure-stacken och Azure-moln med hjälp av den här modulen. | Molnoperatörer och användare |
| [Azure Stack beräkning administration](azure-stack-add-vm-image.md) | Använd den här modulen för att lägga till eller ta bort en VM-avbildning från marketplace för Azure-stacken. | Molnoperatörer |
| [Azure Stack infrastruktur för administration](https://github.com/Azure/AzureStack-Tools/blob/master/Infrastructure/README.md) | Använd den här modulen för att hantera Azure-stacken infrastruktur för virtuella datorer, aviseringar, uppdateringar och så vidare. |  Molnoperatörer|
| [Resource Manager-princip för Azure-stacken](user/azure-stack-policy-module.md) | Använd den här modulen för att konfigurera en Azure-prenumeration eller ett Azure-resursgrupp med samma version och tjänsten tillgänglighet som Azure-stacken. | Molnoperatörer och användare |
| [Registrera med Azure](azure-stack-register.md) | Använd den här modulen för att registrera din development kit instans med Azure. Du kan hämta marketplace-objekt från Azure och använda dem i Azure-stacken efter registrering. | Molnoperatörer |
| [Azure Stack-distribution](azure-stack-run-powershell-script.md) | Använd den här modulen för att förbereda Azure Stack värddatorn att distribuera och omdistribuera med hjälp av Azure-stacken virtuell hårddisk (VHD)-bild. | Molnoperatörer|
| [Ansluta till Azure-stacken](azure-stack-connect-powershell.md) | Använd den här modulen för att ansluta till en Azure-Stack-instans med hjälp av PowerShell och konfigurera VPN-anslutning till Azure-stacken. | Molnoperatörer och användare |
| [Administrationen av Azure Stack](azure-stack-create-offer.md) | Använd den här modulen för att skapa ett erbjudande för standard-klient med obegränsad kvoter på beräkning, Azure Storage, nätverk och Key Vault-tjänster.   | Molnoperatörer|
| [Mall för systemhälsoverifierare](user/azure-stack-validate-templates.md) | Använd den här modulen för att kontrollera om en befintlig eller en ny mall kan distribueras till Azure-stacken. | Molnoperatörer och användare|


## <a name="next-steps"></a>Nästa steg
* [Konfigurera Azure Stack användarens PowerShell-miljö](user/azure-stack-powershell-configure-user.md)   
* [Ansluta till Azure-stacken Development Kit via en VPN-anslutning](azure-stack-connect-azure-stack.md)  
