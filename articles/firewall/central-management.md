---
title: Central hantering av Azure Firewall
description: Läs mer om central hantering av Azure Firewall Manager
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444555"
---
# <a name="azure-firewall-central-management"></a>Central hantering av Azure Firewall

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Om du hanterar flera brandväggar vet du att kontinuerligt ändrade brandväggsregler gör det svårt att hålla dem synkroniserade. Centrala IT-team behöver ett sätt att definiera grundläggande brandväggsprinciper och genomdriva dem över flera affärsenheter. Samtidigt vill DevOps-team skapa sina egna lokala brandväggsprinciper för bättre flexibilitet.

Förhandsversionen av Azure Firewall Manager kan hjälpa dig att lösa dessa problem.


## <a name="azure-firewall-manager-preview"></a>Förhandsversion av Azure Firewall Manager

Förhandsversionen av Azure Firewall Manager är en nätverkssäkerhetshanteringstjänst som tillhandahåller central säkerhetsprincip och väghantering för molnbaserade säkerhets perimeterer. Det gör det enkelt för ENTERPRISE IT-team att centralt definiera regler för nätverks- och programnivå för trafikfiltrering över flera Azure-brandväggsinstanser. Du kan spana över olika Azure-regioner och prenumerationer i hubb- och ekrararkitekturer för trafikstyrning och skydd. Det ger också DevOps bättre flexibilitet med härledda lokala brandväggssäkerhetsprinciper som implementeras i olika organisationer.

### <a name="firewall-policy"></a>Brandväggsprincip

En brandväggsprincip är en Azure-resurs som innehåller INSTÄLLNINGAR FÖR NAT, nätverk och programregel och inställningar för hotinformation. Det är en global resurs som kan användas i flera Azure-brandväggsinstanser i *säkra virtuella hubbar* och *virtuella hubbnätverk.* Nya principer kan skapas från grunden eller ärvs från befintliga principer. Arv gör det möjligt för DevOps att skapa lokala brandväggsprinciper ovanpå den organisationsuppdragade basprincipen. Principer fungerar mellan regioner och prenumerationer.
 
Du kan skapa brandväggsprincip och associationer med Azure Firewall Manager. Du kan dock också skapa och hantera en princip med REST API, mallar, Azure PowerShell och CLI. När du har skapat en princip kan du associera den med en brandvägg i en virtuell WAN-hubb som gör den till en *säker virtuell hubb* och/eller en brandvägg i ett virtuellt nätverk som gör den *till Hub Virtual Network*.

### <a name="pricing"></a>Prissättning

Principer faktureras baserat på brandväggsassociationer. En policy med noll eller en brandväggsförening är gratis. En princip med flera brandväggsassociationer faktureras med en fast hastighet. Mer information finns i [Prissättningen för Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="azure-firewall-management-partners"></a>Azure-partner för brandväggshantering

Följande ledande tredjepartslösningar stöder azure-brandväggens centrala hantering med standard-AZURE REST-API:er. Var och en av dessa lösningar har sina egna unika egenskaper och egenskaper:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda Cloud Säkerhet Guardian](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Nästa steg

Mer information om förhandsversionen av Azure Firewall Manager finns i [Vad är förhandsversionen av Azure Firewall Manager?](../firewall-manager/overview.md)