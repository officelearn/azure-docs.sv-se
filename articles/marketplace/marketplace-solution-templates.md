---
title: Publicerings guide för mall för Azure Applications-lösningar – Azure Marketplace
description: I den här artikeln beskrivs kraven för att publicera Solution-mallar på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: f62b3478c5c711423913b5918886b43b79ac691d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858326"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Guide för publicerings guide för Azure Applications-lösningar

I den här artikeln beskrivs kraven för att publicera lösnings mal len, vilket är ett sätt att publicera Azure-programerbjudanden på Azure Marketplace. I lösnings mallens erbjudande typ krävs en [Azure Resource Manager-mall (arm-mall)](../azure-resource-manager/templates/overview.md) för att automatiskt distribuera lösnings infrastrukturen.

Använd mallen för Azure Application *Solution-mallen* under följande omständigheter:

- Lösningen kräver ytterligare distributions-och konfigurations automatisering utöver en enskild virtuell dator (VM), till exempel en kombination av virtuella datorer, nätverk och lagrings resurser.
- Dina kunder kommer att hantera själva lösningen.

Anropet till den åtgärd som en kund ser för den här erbjudande typen är *nu*.

## <a name="requirements-for-solution-template-offers"></a>Krav för lösnings mal len erbjuder

| **Krav** | **Information**  |
| ---------------  | -----------  |
|Fakturering och mätning    |  Lösnings mal len erbjuder inte transaktions erbjudanden, men de kan användas för att distribuera betalda VM-erbjudanden som debiteras via Microsofts kommersiella marknads plats. De resurser som lösningens ARM-mall distribuerar konfigureras i kundens Azure-prenumeration. Virtuella datorer som du betalar per användning samverkar med kunden via Microsoft och faktureras via kundens Azure-prenumeration.<br/> För att få en BYOL-fakturering, även om Microsoft fakturerar infrastruktur kostnader som uppstår i kund prenumerationen, kan du använda licens avgifterna för program varan med kunden direkt.   |
|Azure-kompatibel virtuell hård disk (VHD)  |   Virtuella datorer måste byggas på Windows eller Linux. Mer information finns i: <ul> <li>[Skapa ett erbjudande för Azure-program](./partner-center-portal/create-new-azure-apps-offer.md) (för Windows-VHD: er).</li><li>[Linux-distributioner](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) som har godkänts på Azure (för Linux-VHD: er).</li></ul> |
| Spårning av kundanvändning | Aktivering av kund användnings behörighet krävs för alla Solution-mallar som publicerats på Azure Marketplace. Mer information om kund användnings behörighet och hur du aktiverar det finns i [Azure-partner kund användnings behörighet](./azure-partner-customer-usage-attribution.md).  |
| Använd Managed disks | [Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) är standard alternativet för bestående diskar av infrastruktur som en tjänst (IaaS) virtuella datorer i Azure. Du måste använda Managed disks i Solution templates. <ul><li>Om du vill uppdatera dina Solution templates följer du rikt linjerna i [använda hanterade diskar i Azure Resource Manager mallar](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments)och använder de angivna [exemplen](https://github.com/Azure/azure-quickstart-templates).<br><br> </li><li>Om du vill publicera den virtuella hård disken som en avbildning på Azure Marketplace importerar du den underliggande virtuella hård disken för de hanterade diskarna till ett lagrings konto med någon av följande metoder:<ul><li>[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [Azure CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul></ul> |

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, lär du dig hur du kan [Utöka din moln verksamhet med Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Registrera dig och börja arbeta i Partner Center:

- [Logga in på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa eller slutföra ditt erbjudande.
- Mer information finns i [skapa ett erbjudande för Azure-program](./partner-center-portal/create-new-azure-apps-offer.md) .
