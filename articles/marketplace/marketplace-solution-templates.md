---
title: Publiceringsguide för Azure Applications Solution-mall | Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera en lösningsmall på Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 9/25/2019
ms.author: dsindona
ms.openlocfilehash: c84436015ad37b57f6603551f1d328ac76181836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288741"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Publiceringsguide för Azure-program: Publiceringsguide för lösningsmaller

Lösningsmallar är ett av de viktigaste sätten att publicera en lösning på Marketplace. Använd den här guiden för att förstå kraven för det här erbjudandet. 

Använd Azure-appen: lösningsmallens erbjudandetyp när din lösning kräver ytterligare distribution och konfigurationsautomatisering utöver en enda virtuell dator. Du kan automatisera etableringen av en eller flera virtuella datorer med Hjälp av Azure-appar: lösningsmallar. Du kan också etablera nätverks- och lagringsresurser. Azure-appar: lösningsmallar erbjuder typ ger automatiseringsfördelar för enskilda virtuella datorer och hela IaaS-baserade lösningar.

Dessa lösningsmallar är inte transaktionserbjudanden, men kan användas för att distribuera betalda VM-erbjudanden som faktureras via Microsofts kommersiella marknadsplats. Uppmaningen som en användare ser är "Hämta den nu".


## <a name="requirements-for-solution-templates"></a>Krav för lösningsmallar

| **Krav** | **Detaljer**  |
| ---------------  | -----------  |
|Fakturering och mätning    |  Resurserna etableras i kundens Azure-prenumeration. Pay-as-you-go (PAYGO) virtuella datorer kommer att överföras med kunden via Microsoft, faktureras via kundens Azure-prenumeration (PAYGO).  <br/> När det gäller bring-your-own-license (BYOL), medan Microsoft fakturerar infrastrukturkostnader som uppstår i kundprenumerationen, kommer du att överföra dina licensavgifter för programvara till kunden direkt.   |
|Azure-kompatibel virtuell hårddisk (VHD)  |   Virtuella datorer måste byggas på Windows eller Linux.  Mer information [finns i Skapa en Azure-kompatibel virtuell hårddisk](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Attribution för kundanvändning | Aktivera attribution för kundanvändning krävs på alla lösningsmallar som publiceras på Azure Marketplace. Mer information om attribution för kundanvändning och hur du aktiverar den finns i [Azure-partnerns kundanvändningsattribution](./azure-partner-customer-usage-attribution.md).  |
| Använd Managed Disks | [Hanterade diskar](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) är standardalternativet för beständiga diskar med virtuella IaaS-datorer i Azure. Du måste använda hanterade diskar i lösningsmallar. <br> <br> 1. Följ [vägledningen](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) och [exemplen](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) för att använda hanterade diskar i Azure ARM-mallarna för att uppdatera dina lösningsmallar. <br> <br> 2. Följ instruktionerna nedan för att importera den underliggande virtuella hårddisken för hanterade diskar till ett lagringskonto för att publicera den virtuella hårddisken som en avbildning på Marketplace: <br> <ul> <li> [Powershell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Nästa steg
Om du inte redan har gjort [det, registrera dig](https://azuremarketplace.microsoft.com/sell) på marknaden.

Om du är registrerad och skapar ett nytt erbjudande eller arbetar på ett befintligt, loggar du in på [Cloud Partner Portal](https://cloudpartner.azure.com) för att skapa eller slutföra ditt erbjudande.
