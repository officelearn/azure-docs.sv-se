---
title: Service Fabric hanterade kluster (förhands granskning)
description: Service Fabric hanterade kluster är en utveckling av resurs modellen för Azure Service Fabric-kluster som effektiviserar distribution och kluster hantering.
ms.topic: overview
ms.date: 09/28/2020
ms.openlocfilehash: 3d26a92126491662d5c51b3c4e8900ffa547f830
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91410497"
---
# <a name="service-fabric-managed-clusters-preview"></a>Service Fabric hanterade kluster (förhands granskning)

Service Fabric hanterade kluster är en utveckling av resurs modellen för Azure Service Fabric-kluster som effektiviserar distributionen och kluster hanterings upplevelsen.

Med mallen för Azure Resource Model (ARM) för traditionella Service Fabric-kluster måste du definiera en kluster resurs tillsammans med ett antal stöd resurser, som alla måste vara "kabelanslutna" (vid distribution och under hela livs cykeln för klustret) för att klustret och dina tjänster ska fungera korrekt. Inkapslings modellen för Service Fabric hanterade kluster består däremot av en enda *Service Fabric hanterad kluster* resurs. Alla underliggande resurser för klustret sammanställs och hanteras av Azure för din räkning.

**Service Fabric traditionell kluster modell** 
 ![ Service Fabric traditionell kluster modell][sf-composition]

**Service Fabric hanterad kluster modell** 
 ![ Service Fabric inkapslad kluster modell][sf-encapsulation]

När det gäller storlek och komplexitet är ARM-mallen för ett Service Fabric hanterat kluster cirka 100 rader med JSON och vissa 1000-rader som krävs för att definiera ett typiskt Service Fabric-kluster:

| Service Fabric-resurser | Service Fabric hanterade kluster resurser |
|----------|-----------|
| Service Fabric-kluster | Service Fabric hanterat kluster |
| Skal uppsättning för virtuell dator (er) | |
| Lastbalanserare | |
| Offentlig IP-adress | |
| Lagringskonto(n) | |
| Virtuellt nätverk | |

Service Fabric hanterade kluster ger ett antal fördelar jämfört med traditionella kluster:

**Förenklad kluster distribution och hantering**
- Distribuera och hantera en enda Azure-resurs
- Certifikat hantering och autorotation
- Förenklade skalnings åtgärder

**Förhindra drifts fel**
- Förhindra konfiguration av matchnings fel med underliggande resurser
- Blockera osäkra åtgärder (till exempel att ta bort en Seed-nod)

**Bästa praxis som standard**
- Förenklade inställningar för tillförlitlighet och hållbarhet

Det kostar inget extra att Service Fabric hanterade kluster utöver kostnaden för de underliggande resurser som krävs för klustret.

## <a name="service-fabric-managed-cluster-skus"></a>Service Fabric hanterade kluster SKU: er

Service Fabric hanterade kluster finns i både Basic-och standard-SKU: er.

| Funktion | Basic | Standard |
| ------- | ----- | -------- |
| Nätverks resurs (SKU för [Load Balancer](../load-balancer/skus.md), [offentlig IP](../virtual-network/public-ip-addresses.md)) | Basic | Standard |
| Antal för min nod (VM-instans) | 3 | 5 |
| Maximalt antal noder per nodtyp | 100 | 100 |
| Maximalt antal nodtyper | 1 | 20 |
| Lägga till/ta bort nodtyper | Nej | Ja |
| Zonredundans | Nej | Ja |

## <a name="service-fabric-managed-cluster-feature-roadmap"></a>Översikt över Service Fabric hanterade kluster funktioner
Eftersom det här är en tidig för hands version av Service Fabric hanterade kluster finns det några luckor att tänka på. Dessa funktioner kommer att bli tillgängliga i framtida versioner. 

* Publicera program till kluster direkt från Visual Studio
* Hanterade identiteter 
* ARM-programdistributioner 
* Tillgänglighetszoner 
* Omvänd proxy 
* Automatisk skalning 
* Uppdaterar NSG-regler 
* Automatiska operativ system uppgraderingar

## <a name="next-steps"></a>Nästa steg

Kom igång med Service Fabric hanterade kluster genom att prova snabb starten:

> [!div class="nextstepaction"]
> [Skapa ett Service Fabric hanterat kluster (förhands granskning)](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png