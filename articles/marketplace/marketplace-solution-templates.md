---
title: Publicerings guide för Azure Applications-lösning | Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera en lösnings mall på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084863"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Azure-program: lösnings mal len erbjuder publicerings krav

I den här artikeln beskrivs kraven för typen av lösnings mal len, vilket är ett sätt att publicera ett erbjudande om Azure-program på Azure Marketplace. I lösnings mallens erbjudande typ krävs en [Azure Resource Manager-mall (arm-mall)](../azure-resource-manager/templates/overview.md) för att automatiskt distribuera lösnings infrastrukturen.

Använd typen av mall för Azure-programlösningen när följande villkor är uppfyllda:

- Lösningen kräver ytterligare distributions-och konfigurations automatisering utöver en enskild virtuell dator, till exempel en kombination av virtuella datorer, nätverk och lagrings resurser.
- Din kund kommer att hantera själva lösningen.

Anropet till åtgärden som en användare ser för den här erbjudande typen är "Hämta nu".

## <a name="requirements-for-solution-template-offers"></a>Krav för lösnings mal len erbjuder

| **Krav** | **Information**  |
| ---------------  | -----------  |
|Fakturering och mätning    |  Lösnings mal len erbjuder inte några Transact-erbjudanden, men kan användas för att distribuera betalda VM-erbjudanden debiteras via Microsofts kommersiella marknads plats. De resurser som lösningens ARM-mall distribuerar kommer att tillhandahållas i kundens Azure-prenumeration. De virtuella datorerna betala per användning (PAYGO) kommer att meddelas med kunden via Microsoft, debiteras via kundens Azure-prenumeration.<br/> I händelse av att du har en egen licens (BYOL), medan Microsoft fakturerar infrastruktur kostnader i kund prenumerationen, kommer du att licensiera dina licens avgifter till kunden direkt.   |
|Azure-kompatibel virtuell hård disk (VHD)  |   Virtuella datorer måste byggas på Windows eller Linux. Mer information finns i: <ul> <li>[Skapa ett erbjudande för Azure-program](./partner-center-portal/create-new-azure-apps-offer.md)(för Windows-VHD: er)</li><li>[Linux-distributioner](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) som har godkänts på Azure (för Linux-VHD: er).</li></ul> |
| Kund användnings behörighet | Aktivering av kund användnings behörighet krävs för alla Solution-mallar som publicerats på Azure Marketplace. Mer information om kund användnings behörighet och hur du aktiverar det finns i [Azure-partner kund användnings behörighet](./azure-partner-customer-usage-attribution.md).  |
| Använd Managed Disks | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) är standard alternativet för beständiga diskar med virtuella IaaS-datorer i Azure. Du måste använda Managed Disks i Solution templates. <br> <br> 1. Följ [vägledningen](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) och [exemplen](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) för att använda Managed disks i Azure arm-mallarna för att uppdatera dina Solution templates. <br> <br> 2. Följ instruktionerna nedan om du vill importera den underliggande virtuella hård disken för Managed Disks till ett lagrings konto för att publicera den virtuella hård disken som en avbildning på Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har gjort det, [Läs mer](https://azuremarketplace.microsoft.com/sell) om Azure Marketplace.
- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra ditt erbjudande.
- [Skapa ett Azure Application-erbjudande](./partner-center-portal/create-new-azure-apps-offer.md) för mer information.
