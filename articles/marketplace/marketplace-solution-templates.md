---
title: Publicerings guide för Azure Applications-lösning | Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera en lösnings mall på Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 9/25/2019
ms.author: ellacroi
ms.openlocfilehash: 934a5e050e190c9a1f90bb3a22c2d1323a3ccecf
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808290"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure-program: lösnings mal len erbjudande publicerings guide

Solution templates är ett av de viktigaste sätten att publicera en lösning på Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Använd typ av lösnings mal len Azure app: för lösnings mal len när din lösning kräver ytterligare distribution och konfigurations automatisering utöver en enskild virtuell dator. Du kan automatisera etableringen av en eller flera virtuella datorer med hjälp av Azure Apps: Solution templates. Du kan också etablera nätverks-och lagrings resurser. Azure Apps: erbjudande typ för Solution templates ger Automation-förmåner för enskilda virtuella datorer och hela IaaS-baserade lösningar.

Dessa Solution templates är inte transaktions erbjudanden, men kan användas för att distribuera betalda VM-erbjudanden debiteras via Microsofts kommersiella marknads plats. Anropet till åtgärden som en användare ser är "Hämta nu".


## <a name="requirements-for-solution-templates"></a>Krav för Solution templates

| **Krav** | **Detaljer**  |
| ---------------  | -----------  |
|Fakturering och mätning    |  Resurserna kommer att tillhandahållas i kundens Azure-prenumeration. De virtuella datorerna betala per användning (PAYGO) kommer att meddelas med kunden via Microsoft, debiteras via kundens Azure-prenumeration (PAYGO).  <br/> I händelse av att du har en egen licens (BYOL), medan Microsoft fakturerar infrastruktur kostnader i kund prenumerationen, kommer du att licensiera dina licens avgifter till kunden direkt.   |
|Azure-kompatibel virtuell hård disk (VHD)  |   Virtuella datorer måste byggas på Windows eller Linux.  Mer information [finns i skapa en Azure-kompatibel virtuell hård disk](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Kund användnings behörighet | Aktivering av kund användnings behörighet krävs för alla Solution-mallar som publicerats på Azure Marketplace. Mer information om kund användnings behörighet och hur du aktiverar det finns i [Azure-partner kund användnings behörighet](./azure-partner-customer-usage-attribution.md).  |
| Använd Managed Disks | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) är standard alternativet för beständiga diskar med virtuella IaaS-datorer i Azure. Du måste använda Managed Disks i Solution templates. <br> <br> 1. Följ [vägledningen](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) och [exemplen](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) för att använda Managed disks i Azure arm-mallarna för att uppdatera dina Solution templates. <br> <br> 2. Följ instruktionerna nedan om du vill importera den underliggande virtuella hård disken för Managed Disks till ett lagrings konto för att publicera den virtuella hård disken som en avbildning på Marketplace: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Nästa steg
[Registrera](https://azuremarketplace.microsoft.com/sell) dig på Marketplace om du inte redan gjort det.

Om du har registrerat och skapat ett nytt erbjudande eller arbetar med ett befintligt, loggar du in på [Cloud Partner Portal](https://cloudpartner.azure.com) för att skapa eller slutföra ditt erbjudande.
