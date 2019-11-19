---
title: Använda Terraform med Azure
description: Introduktion för att använda Terraform för att versionshantera och distribuera Azure-infrastruktur.
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: d1754594c651206a0d4d15e659e2926557f9e912
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159001"
---
# <a name="terraform-with-azure"></a>Terraform med Azure

[Hashicorp Terraform](https://www.terraform.io/) är ett verktyg med öppen källkod för etablering och hantering av molninfrastruktur. IT-codifies infrastruktur i konfigurationsfiler som beskriver moln resursernas topologi. De här resurserna är virtuella datorer, lagrings konton och nätverks gränssnitt. Terraform CLI innehåller en enkel mekanism för att distribuera och version av konfigurationsfilerna till Azure.

Den här artikeln beskriver fördelarna med att använda Terraform för att hantera Azure-infrastruktur.

## <a name="automate-infrastructure-management"></a>Automatisera infrastrukturhantering.

Med Terraforms mallbaserade konfigurationsfiler kan du definiera, etablera och konfigurera Azure-resurser på ett upprepningsbart och förutsebart sätt. Det finns flera fördelar med att automatisera infrastruktur:

- Minskad risk för mänskliga fel vid distribution och hantering av infrastruktur.
- Distribution av samma mall flera gånger för att skapa identiska miljöer för utveckling, testning och produktion.
- Minskad kostnad för utvecklings- och testmiljöer genom att skapa dem på begäran.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Förstå infrastruktur ändringar innan de tillämpas

Som en resurs blir topologin komplex och det kan vara svårt att förstå innebörden och effekten av infrastrukturändringar.

Terraform CLI gör det möjligt för användare att verifiera och förhandsgranska infrastruktur ändringar innan programmet. Att för hands Visa infrastruktur ändringar på ett säkert sätt har flera fördelar:
- Gruppmedlemmar kan samarbeta effektivare genom att snabbt förstå föreslagna ändringar och deras påverkan.
- Oönskade ändringar kan fångas upp tidigt i utvecklingsprocessen

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Distribuera infrastruktur till flera molntjänster

Terraform är skickliga vid distribution av en infrastruktur över flera moln leverantörer. Det gör det möjligt för utvecklare att använda konsekventa verktyg för att hantera varje infrastruktur definition.

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över Terraform och dess fördelar föreslår vi nästa steg:

- Kom igång genom att [installera Terraform och konfigurera det för att använda Azure](/azure/virtual-machines/linux/terraform-install-configure).
- [Skapa en virtuell Azure-dator med Terraform](/azure/virtual-machines/linux/terraform-create-complete-vm)
- Utforska [modulen Azure Resource Manager för Terraform](https://www.terraform.io/docs/providers/azurerm/) 