---
title: Container Security i Azure Security Center | Microsoft-dokument
description: Läs mer om Azure Security Centers säkerhetsfunktioner för behållare.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: d46e2a9820ec0c45d197f135428f1ace712b2fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125146"
---
# <a name="container-security-in-security-center"></a>Containersäkerhet i Security Center

Azure Security Center är den Azure-inbyggda lösningen för behållarsäkerhet. Security Center är också den optimala enskilda glasrutan för säkerheten för dina molnarbetsbelastningar, virtuella datorer, servrar och behållare.

I den här artikeln beskrivs hur Security Center hjälper dig att förbättra, övervaka och upprätthålla säkerheten för dina behållare och deras appar. Du får lära dig hur Security Center hjälper till med dessa centrala aspekter av containersäkerhet:

* Hantering av sårbarhet
* Härdning av behållarens miljö
* Körtidsskydd

[![Säkerhetsfliken för behållare i Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Instruktioner om hur du använder dessa funktioner finns i [Övervaka säkerheten för dina behållare](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images"></a>Sårbarhetshantering - skanning av behållaravbildningar
Om du vill övervaka ditt ARM-baserade Azure Container-register kontrollerar du att du är på Security Centers standardnivå (se [priser](/azure/security-center/security-center-pricing)). Aktivera sedan paketet Container Register. När en ny bild trycks igenom söker Security Center igenom bilden med hjälp av en skanner från den branschledande leverantören av sårbarhetsskanning, Qualys.

När problem hittas – av Qualys eller Security Center – får du ett meddelande på instrumentpanelen för Säkerhetscenter. För alla säkerhetsproblem ger Security Center användbara rekommendationer, tillsammans med en allvarlighetsgrad och vägledning för hur du åtgärdar problemet. Mer information om Security Centers rekommendationer för behållare finns i [referenslistan över rekommendationer](recommendations-reference.md#recs-containers).

Security Center filtrerar och klassificerar resultat från skannern. När en bild är felfri markerar Security Center den som sådan. Security Center genererar endast säkerhetsrekommendationer för avbildningar som har problem som ska lösas. Genom att bara meddela när det finns problem minskar Security Center risken för oönskade informationsaviseringar.

## <a name="environment-hardening"></a>Miljöhärdning

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Kontinuerlig övervakning av Docker-konfigurationen
Azure Security Center identifierar ohanterade behållare som finns på virtuella IaaS Linux-datorer eller andra Linux-datorer som kör Docker-behållare. Security Center utvärderar kontinuerligt konfigurationerna av dessa behållare. Det jämför dem sedan med [Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

Security Center innehåller hela regeluppsättningen för CIS Docker Benchmark och varnar dig om dina behållare inte uppfyller någon av kontrollerna. När säkerhetscenter hittar felkonfigurationer genererar det säkerhetsrekommendationer. Använd **rekommendationer sidan** för att visa rekommendationer och åtgärda problem. Du ser också rekommendationerna på fliken **Behållare** som visar alla virtuella datorer som distribueras med Docker. 

Mer information om relevanta rekommendationer för Säkerhetscenter som kan visas för den här funktionen finns i [behållaravsnittet](recommendations-reference.md#recs-containers) i referenstabellen för rekommendationer.

När du utforskar säkerhetsproblemen i en virtuell dator ger Security Center ytterligare information om behållarna på datorn. Sådan information omfattar Docker-versionen och antalet avbildningar som körs på värden. 

>[!NOTE]
> Dessa CIS-benchmarkkontroller körs inte på AKS-hanterade instanser eller databricks-hanterade virtuella datorer.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Kontinuerlig övervakning av kubernetes-kluster
Security Center samarbetar med Azure Kubernetes Service (AKS), Microsofts tjänst för hantering av behållare för utveckling, distribution och hantering av behållarprogram.

AKS ger säkerhetskontroller och insyn i säkerhetspositionen för dina kluster. Security Center använder dessa funktioner för att:
* Övervaka konfigurationen av AKS-kluster hela tiden
* Generera säkerhetsrekommendationer i linje med branschstandarder

Mer information om relevanta rekommendationer för Säkerhetscenter som kan visas för den här funktionen finns i [behållaravsnittet](recommendations-reference.md#recs-containers) i referenstabellen för rekommendationer.

## <a name="run-time-protection---real-time-threat-detection"></a>Körningsskydd - Hotidentifiering i realtid

Security Center tillhandahåller hotidentifiering i realtid för dina behållarmiljöer och genererar aviseringar för misstänkta aktiviteter. Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina containrar.

Vi identifierar hot på värd- och AKS-klusternivå. Fullständig information finns i [hotidentifiering för Azure-behållare](threat-protection.md#azure-containers).


## <a name="container-security-faq"></a>Vanliga frågor om containersäkerhet

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Vilka typer av avbildningar kan Azure Security Center skanna?
Security Center söker igenom Linux OS-baserade avbildningar som ger shell-åtkomst. 

Qualys-skannern stöder inte superminimalistiska bilder som [Docker scratch-bilder](https://hub.docker.com/_/scratch/) eller "Distroless"-bilder som bara innehåller ditt program och dess körningsberoenden utan pakethanterare, skal eller operativsystem.

### <a name="how-does-azure-security-center-scan-an-image"></a>Hur skannar Azure Security Center en avbildning?
Avbildningen hämtas från registret. Den körs sedan i en isolerad sandlåda med Qualys-skannern som extraherar en lista över kända sårbarheter.

Security Center filtrerar och klassificerar resultat från skannern. När en bild är felfri markerar Security Center den som sådan. Security Center genererar endast säkerhetsrekommendationer för avbildningar som har problem som ska lösas. Genom att bara meddela när det finns problem minskar Security Center risken för oönskade informationsaviseringar.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Hur ofta skannar Azure Security Center mina avbildningar?
Bildskanningar utlöses på varje push.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Kan jag få skanningsresultaten via REST API?
Ja. Resultaten är under [Sub-Bedömningar Rest API](/rest/api/securitycenter/subassessments/list/). Du kan också använda Azure Resource Graph (ARG), det Kusto-liknande API:et för alla dina resurser: en fråga kan hämta en viss genomsökning.
 

## <a name="next-steps"></a>Nästa steg

Mer information om behållarsäkerhet i Azure Security Center finns i följande relaterade artiklar:

* Information om hur du visar säkerhetspositionen för behållarrelaterade resurser finns i avsnittet behållare [i Skydda dina datorer och program](security-center-virtual-machine-protection.md#containers).

* Information om [integreringen med Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* Information om [integreringen med Azure Container Registry](azure-container-registry-integration.md)