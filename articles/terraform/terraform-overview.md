---
title: Använda Terraform med Azure
description: Introduktion för att använda Terraform för att versionshantera och distribuera Azure-infrastruktur.
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 05b92fdf8c0a0f84d2f29b4aa7479850b2721441
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472170"
---
# <a name="terraform-with-azure"></a>Terraform med Azure

[Hashicorp Terraform](https://www.terraform.io/) är ett verktyg med öppen källkod för etablering och hantering av molninfrastruktur. Den kodifierar infrastruktur i konfigurationsfiler som beskriver topologin för molnresurser. Dessa resurser omfattar virtuella datorer, lagringskonton och nätverksgränssnitt. Terraform CLI tillhandahåller en enkel mekanism för att distribuera och version av konfigurationsfilerna till Azure.

Den här artikeln beskriver fördelarna med att använda Terraform för att hantera Azure-infrastruktur.

## <a name="automate-infrastructure-management"></a>Automatisera infrastrukturhantering.

Med Terraforms mallbaserade konfigurationsfiler kan du definiera, etablera och konfigurera Azure-resurser på ett upprepningsbart och förutsebart sätt. Det finns flera fördelar med att automatisera infrastruktur:

- Minskad risk för mänskliga fel vid distribution och hantering av infrastruktur.
- Distribution av samma mall flera gånger för att skapa identiska miljöer för utveckling, testning och produktion.
- Minskad kostnad för utvecklings- och testmiljöer genom att skapa dem på begäran.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Förstå infrastrukturändringar innan de tillämpas

Som en resurs blir topologin komplex och det kan vara svårt att förstå innebörden och effekten av infrastrukturändringar.

Terraform CLI gör det möjligt för användare att validera och förhandsgranska infrastrukturändringar före program. Att förhandsgranska infrastrukturändringar på ett säkert sätt har flera fördelar:
- Gruppmedlemmar kan samarbeta effektivare genom att snabbt förstå föreslagna ändringar och deras påverkan.
- Oönskade ändringar kan fångas upp tidigt i utvecklingsprocessen

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Distribuera infrastruktur till flera molntjänster

Terraform är skickliga på att distribuera en infrastruktur över flera molnleverantörer. Det gör det möjligt för utvecklare att använda konsekventa verktyg för att hantera varje infrastrukturdefinition.

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Terraform och dess fördelar föreslår vi nästa steg:

- Kom igång genom att [installera Terraform och konfigurera det för att använda Azure](terraform-install-configure.md).
- [Skapa en virtuell Azure-dator med Terraform](terraform-create-complete-vm.md)
- Utforska [modulen Azure Resource Manager för Terraform](https://www.terraform.io/docs/providers/azurerm/) 