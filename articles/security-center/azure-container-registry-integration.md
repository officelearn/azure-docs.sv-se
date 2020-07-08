---
title: Azure Security Center och Azure Container Registry
description: Lär dig mer om Azure Security Centers integrering med Azure Container Registry
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: f3ef633ff0271d74eea7320faadf17685976d3b6
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970475"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Azure Container Registry integration med Security Center

Azure Container Registry (ACR) är en hanterad privat Docker-registerpost som lagrar och hanterar behållar avbildningar för Azure-distributioner i ett centralt register. Den är baserad på Docker-registret 2,0 med öppen källkod.

Om du är på Azure Security Center standard nivån kan du lägga till behållar register paketet. Den här valfria funktionen ger djupare insyn i säkerhets riskerna för avbildningarna i dina ARM-baserade register. Aktivera eller inaktivera paketet på prenumerations nivå för att ta med alla register i en prenumeration. Den här funktionen debiteras per avbildning, som visas på [sidan med priser](security-center-pricing.md). Genom att aktivera paket för behållar register ser du till att Security Center är redo att skanna bilder som skickas till registret. 


## <a name="availability"></a>Tillgänglighet

- Versions tillstånd: **allmän tillgänglighet**
- Nödvändiga roller: rollen **säkerhet läsare** och [Azure Container Registry läsare](https://docs.microsoft.com/azure/container-registry/container-registry-roles)
- Moln 
    - ✔ Kommersiella moln
    - ✘ US regering-moln
    - ✘ Kina, myndigheter, andra gov-moln


## <a name="when-are-images-scanned"></a>När skannas bilder?

När en bild skickas till ditt register, Security Center skannar automatiskt avbildningen. Om du vill utlösa en genomsökning av en avbildning kan du skicka den till din lagrings plats.

När genomsökningen är klar (vanligt vis efter cirka 10 minuter, men kan vara upp till 40 minuter), är avgöranden tillgängliga som Security Center rekommendationer som detta:

[![Exempel på Azure Security Center rekommendation om sårbarheter som identifierats i en Azure Container Registry (ACR) värd avbildning](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Fördelar med integrering

Security Center identifierar ARM-baserade ACR-register i din prenumeration och ger sömlös:

* **Azure – ursprunglig sårbarhets sökning** för alla publicerade Linux-avbildningar. Security Center skannar avbildningen med hjälp av en skanner från den branschledande sårbara sårbarheten hos leverantören, Qualys. Den här interna lösningen integreras sömlöst som standard.

* **Säkerhets rekommendationer** för Linux-avbildningar med kända sårbarheter. Security Center innehåller information om varje rapporterat sårbarhets-och allvarlighets GRADS klassificering. Dessutom ger den vägledning för hur du kan åtgärda de enskilda säkerhets risker som finns på varje avbildning som skickas till registret.

![Azure Security Center och Azure Container Registry (ACR) Översikt på hög nivå](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="acr-with-security-center-faq"></a>ACR med Security Center vanliga frågor och svar

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Vilka typer av avbildningar kan Azure Security Center skanning?
Security Center söker igenom Linux OS-baserade avbildningar som ger shell-åtkomst. 

Qualys-skannern har inte stöd för Super minimalist-bilder som [Docker Scratch](https://hub.docker.com/_/scratch/) images, eller "Distroless"-bilder som bara innehåller ditt program och dess körnings beroenden utan paket hanteraren, Shell eller OS.

### <a name="how-does-azure-security-center-scan-an-image"></a>Hur skannar Azure Security Center en avbildning?
Avbildningen hämtas från registret. Den körs sedan i en isolerad sandbox med Qualys-skannern som extraherar en lista över kända sårbarheter.

Security Center filtrerar och klassificerar resultat från skannern. När en bild är felfri, Security Center Markera den som sådan. Security Center skapar endast säkerhets rekommendationer för avbildningar som har problem att lösa. Genom att meddela om det uppstår problem kan Security Center minska risken för oönskade informations aviseringar.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Hur ofta Azure Security Center Skanna mina bilder?
Avbildnings genomsökningar utlöses vid varje push-överföring.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Kan jag få Sök resultatet via REST API?
Ja. Resultaten är under [Underbedömningar REST API](/rest/api/securitycenter/subassessments/list/). Du kan också använda Azure Resource Graph (ARG), Kusto API för alla resurser: en fråga kan hämta en speciell sökning.
 



## <a name="next-steps"></a>Nästa steg

Mer information om Security Center behållar säkerhetsfunktioner finns i:

* [Azure Security Center-och behållar säkerhet](container-security.md)

* [Integration med Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Skydd av virtuella datorer](security-center-virtual-machine-protection.md) – beskriver Security Center rekommendationer
