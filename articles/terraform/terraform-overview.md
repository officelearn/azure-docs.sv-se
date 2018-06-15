---
title: Med Azure Terraform
description: Introduktion till Terraform till version och distribuera Azure-infrastrukturen.
ms.service: virtual-machines-linux
keywords: terraform, devops, översikt, planera, tillämpa, automatisera
author: binderjoe
ms.author: jbinder
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 667752d8830cdac5e2338fd3ed7904917123be94
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
ms.locfileid: "26364478"
---
# <a name="terraform-with-azure"></a>Terraform med Azure

[Hashicorp Terraform](https://www.terraform.io/) är ett verktyg för öppen källkod för att etablera och hantera moln-infrastruktur. Den kodifieras infrastrukturen i konfigurationsfiler som beskriver topologin för molnresurser, till exempel virtuella datorer, lagringskonton och nätverk gränssnitt. Terraforms kommandoradsgränssnittet (CLI) ger en enkel metod för att distribuera och version konfigurationsfilerna till Azure eller andra stöds molnet.

Den här artikeln beskriver fördelarna med att använda Terraform för att hantera Azure-infrastrukturen.

## <a name="automate-infrastructure-management"></a>Automatisera infrastrukturhantering av.

Terraforms mallbaserade configuration-filer kan du definiera, etablera och konfigurera Azure-resurser på ett repeterbara och förutsägbart sätt. Automatiskt infrastrukturen har flera fördelar:

- Minskar risken för mänskliga fel vid distribution och hantering av infrastrukturen.
- Distribuerar samma mall flera gånger för att skapa identiska utveckling, test- och miljöer.
- Minskar kostnaden för utvecklings- och testmiljöer genom att skapa dem på begäran.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>Förstå infrastrukturändringar innan de tillämpas 

Som en resurs topologi blir komplexa förstå innebörden och effekten av ändringar i infrastrukturen kan vara svårt.

Terraform ger kommandoradsgränssnittet (CLI) som användarna kan validera och förhandsgranska ändringar i infrastrukturen innan de distribueras. Förhandsgranska ändringar i infrastrukturen i ett säkerhetsskåp, har produktiva sätt flera fördelar:
- Gruppmedlemmarna kan samarbeta effektivare snabbt förstå föreslagna ändringar och deras effekt.
- Oväntade ändringar kan fångas tidigt i utvecklingsprocessen


## <a name="deploy-infrastructure-to-multiple-clouds"></a>Distribuera infrastrukturen till flera moln

Terraform är ett populärt verktyget alternativ för flera scenarier, där liknande infrastruktur har distribuerats till Azure och ytterligare molntjänstleverantörer eller lokala datacenter. Det gör att utvecklare kan använda samma verktyg och konfigurationsfiler för att hantera infrastrukturen på flera molntjänstleverantörer.

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Terraform och dess fördelar finns här föreslagna nästa steg:

- Kom igång genom att [installera Terraform och konfigurera den för att använda Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure).
- [Skapa en virtuell Azure-dator med hjälp av Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm)
- Utforska den [Azure Resource Manager-modulen för Terraform](https://www.terraform.io/docs/providers/azurerm/) 