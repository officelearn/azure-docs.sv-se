---
title: Azure-program lösningsmall erbjuder publiceringsguide | Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera en lösningsmall på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084863"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Azure-program: Lösningsmallen erbjuder publiceringskrav

I den här artikeln beskrivs kraven för erbjudandetypen för lösningsmallen, vilket är ett sätt att publicera ett Azure-programerbjudande på Azure Marketplace. Erbjudandetypen för lösningsmall kräver en [Azure Resource Manager-mall (ARM-mall)](../azure-resource-manager/templates/overview.md) för att automatiskt distribuera din lösningsinfrastruktur.

Använd erbjudandetypen för Azure-programlösningsmallen när följande villkor krävs:

- Din lösning kräver ytterligare distribution och konfigurationsautomatisering utöver en enda virtuell dator, till exempel en kombination av virtuella datorer, nätverk och lagringsresurser.
- Din kund kommer att hantera lösningen själva.

Uppmaningen som en användare ser för den här erbjudandetypen är "Hämta den nu".

## <a name="requirements-for-solution-template-offers"></a>Krav på lösningsmallerbjudanden

| **Krav** | **Detaljer**  |
| ---------------  | -----------  |
|Fakturering och mätning    |  Erbjudanden med lösningsmallar är inte transact-erbjudanden, men kan användas för att distribuera betalda VM-erbjudanden som faktureras via Microsofts kommersiella marknadsplats. De resurser som lösningens ARM-mall distribuerar etableras i kundens Azure-prenumeration. Pay-as-you-go (PAYGO) virtuella datorer kommer att överföras med kunden via Microsoft, faktureras via kundens Azure-prenumeration.<br/> När det gäller bring-your-own-license (BYOL), medan Microsoft fakturerar infrastrukturkostnader som uppstår i kundprenumerationen, kommer du att överföra dina licensavgifter för programvara till kunden direkt.   |
|Azure-kompatibel virtuell hårddisk (VHD)  |   Virtuella datorer måste byggas på Windows eller Linux. Mer information finns i: <ul> <li>[Skapa ett Azure-programerbjudande](./partner-center-portal/create-new-azure-apps-offer.md)(för Windows-hårddiskar)</li><li>[Linux-distributioner som godkänts på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (för Virtuella Linux-hårddiskar).</li></ul> |
| Attribution för kundanvändning | Aktivera attribution för kundanvändning krävs på alla lösningsmallar som publiceras på Azure Marketplace. Mer information om attribution för kundanvändning och hur du aktiverar den finns i [Azure-partnerns kundanvändningsattribution](./azure-partner-customer-usage-attribution.md).  |
| Använd Managed Disks | [Hanterade diskar](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) är standardalternativet för beständiga diskar med virtuella IaaS-datorer i Azure. Du måste använda hanterade diskar i lösningsmallar. <br> <br> 1. Följ [vägledningen](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) och [exemplen](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) för att använda hanterade diskar i Azure ARM-mallarna för att uppdatera dina lösningsmallar. <br> <br> 2. Följ instruktionerna nedan för att importera den underliggande virtuella hårddisken för hanterade diskar till ett lagringskonto för att publicera den virtuella hårddisken som en avbildning på Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har gjort det [kan du läsa](https://azuremarketplace.microsoft.com/sell) mer om Azure Marketplace.
- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra erbjudandet.
- [Skapa ett Azure-programerbjudande](./partner-center-portal/create-new-azure-apps-offer.md) för mer information.
