---
title: Använda Terraform med Azure
description: Introduktion för att använda Terraform för att versionshantera och distribuera Azure-infrastruktur.
services: terraform
ms.service: terraform
keywords: terraform, devops, översikt, planera, tillämpa, automatisera
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/31/2018
ms.openlocfilehash: dd340238f8a70c1dd0cfc172976bef6b1ad282b1
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665637"
---
# <a name="terraform-with-azure"></a>Terraform med Azure

[Hashicorp Terraform](https://www.terraform.io/) är ett verktyg med öppen källkod för etablering och hantering av molninfrastruktur. Den kodifierar infrastruktur i konfigurationsfiler som beskriver topologin för molnresurser, till exempel virtuella datorer, lagringskonton och nätverksgränssnitt. Terraforms kommandoradsgränssnitt (CLI) innehåller en enkel mekanism för att distribuera och versionshantera konfigurationsfilerna till Azure eller andra molntjänster som stöds.

Den här artikeln beskriver fördelarna med att använda Terraform för att hantera Azure-infrastruktur.

## <a name="automate-infrastructure-management"></a>Automatisera infrastrukturhantering.

Med Terraforms mallbaserade konfigurationsfiler kan du definiera, etablera och konfigurera Azure-resurser på ett upprepningsbart och förutsebart sätt. Det finns flera fördelar med att automatisera infrastruktur:

- Minskad risk för mänskliga fel vid distribution och hantering av infrastruktur.
- Distribution av samma mall flera gånger för att skapa identiska miljöer för utveckling, testning och produktion.
- Minskad kostnad för utvecklings- och testmiljöer genom att skapa dem på begäran.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>Förståelse av ändringar i infrastruktur innan de tillämpas 

Som en resurs blir topologin komplex och det kan vara svårt att förstå innebörden och effekten av infrastrukturändringar.

Terraform tillhandahåller ett kommandoradsgränssnitt (CLI) som gör att användare kan validera och förhandsgranska ändringar i infrastrukturen innan de distribueras. Att förhandsgranska infrastrukturändringar på ett säkert och produktivt sätt har flera fördelar:
- Gruppmedlemmar kan samarbeta effektivare genom att snabbt förstå föreslagna ändringar och deras påverkan.
- Oönskade ändringar kan fångas upp tidigt i utvecklingsprocessen


## <a name="deploy-infrastructure-to-multiple-clouds"></a>Distribuera infrastruktur till flera molntjänster

Terraform är ett populärt verktyg för scenarier med flera molntjänster, där liknande infrastruktur distribueras till Azure och ytterligare molnleverantörer eller lokala datacenter. Det gör att utvecklare kan använda samma verktyg och konfigurationsfiler för att hantera infrastruktur hos flera molnleverantörer.

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Terraform och dess fördelar föreslår vi nästa steg:

- Kom igång genom att [installera Terraform och konfigurera det för att använda Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure).
- [Skapa en virtuell Azure-dator med Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm)
- Utforska [modulen Azure Resource Manager för Terraform](https://www.terraform.io/docs/providers/azurerm/) 