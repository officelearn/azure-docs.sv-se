---
title: Behållar säkerhet i Azure Security Center | Microsoft Docs
description: Läs mer om Azure Security Center behållar säkerhetsfunktioner.
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
ms.openlocfilehash: cd9907d041562d23bc487de319a17d36b68db6f3
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77916125"
---
# <a name="container-security-in-security-center"></a>Behållar säkerhet i Security Center

Azure Security Center är en Azure-inbyggd lösning för behållar säkerhet. Security Center är också det optimala fönstret med glas miljö för att skydda dina moln arbets belastningar, virtuella datorer, servrar och behållare.

Den här artikeln beskriver hur Security Center hjälper dig att förbättra, övervaka och upprätthålla säkerheten för dina behållare och deras appar. Du lär dig hur Security Center hjälper till med dessa kärn aspekter av behållar säkerhet:

* Sårbarhets hantering
* Härdning av behållarens miljö
* Körnings skydd

[fliken behållar säkerhet för ![Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Instruktioner för hur du använder dessa funktioner finns i [övervaka säkerheten för dina behållare](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images-preview"></a>Sårbarhets hantering – Skanna behållar avbildningar (för hands version)
Kontrol lera att du är på Security Center standard nivå (se [prissättning](/azure/security-center/security-center-pricing)) för att övervaka ARM-baserade Azure Container Registry. Aktivera sedan de valfria paketen för behållar register. När en ny avbildning överförs, Security Center skannar avbildningen med hjälp av en skanner från den branschledande sårbara sårbarheten hos leverantören, Qualys.

När problem påträffas – av Qualys eller Security Center – får du ett meddelande i Security Center instrument panel. För varje sårbarhet ger Security Center rekommendationer som kan åtgärdas, tillsammans med en allvarlighets grad och vägledning för hur du kan åtgärda problemet. Mer information om Security Centers rekommendationer för behållare finns i [referens listan för rekommendationer](recommendations-reference.md#recs-containers).

## <a name="environment-hardening"></a>Miljö härdning

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Kontinuerlig övervakning av Docker-konfigurationen
Azure Security Center identifierar ohanterade behållare som finns på virtuella IaaS Linux-datorer eller andra Linux-datorer som kör Docker-behållare. Security Center kontinuerligt bedömer konfigurationen av dessa behållare. Den jämför sedan dem med [Center for Internet Security (CIS) Docker-benchmark](https://www.cisecurity.org/benchmark/docker/).

Security Center innehåller hela ruleset av CIS Docker-benchmark och varnar dig om dina behållare inte uppfyller någon av kontrollerna. När det hittar felkonfigurationer genererar Security Center säkerhets rekommendationer. Använd **sidan rekommendationer** för att se rekommendationer och åtgärda problem. Du kan också se rekommendationerna på fliken **behållare** som visar alla virtuella datorer som distribueras med Docker. 

Mer information om relevanta Security Center rekommendationer som kan visas för den här funktionen finns i [avsnittet container](recommendations-reference.md#recs-containers) i referens tabellen för rekommendationer.

När du utforskar säkerhets problemen för en virtuell dator innehåller Security Center ytterligare information om behållarna på datorn. Sådan information omfattar Docker-versionen och antalet avbildningar som körs på värden. 

>[!NOTE]
> Dessa CIS-benchmark-kontroller kan inte köras på AKS-hanterade instanser eller Databricks-hanterade virtuella datorer.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Kontinuerlig övervakning av dina Kubernetes-kluster (för hands version)
Security Center fungerar tillsammans med Azure Kubernetes service (AKS), Microsofts hanterade behållar Orchestration-tjänst för utveckling, distribution och hantering av program i behållare.

AKS tillhandahåller säkerhets kontroller och insyn i säkerhets position i dina kluster. Security Center använder dessa funktioner för att:
* Övervaka kontinuerligt konfigurationen av dina AKS-kluster
* Skapa säkerhets rekommendationer som är justerade med bransch standarder

Mer information om relevanta Security Center rekommendationer som kan visas för den här funktionen finns i [avsnittet container](recommendations-reference.md#recs-containers) i referens tabellen för rekommendationer.

## <a name="run-time-protection---real-time-threat-protection"></a>Kör tids skydd – real tids skydd av hot

Security Center tillhandahåller hot skydd i real tid för dina behållares miljöer och genererar aviseringar för misstänkta aktiviteter. Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina containrar.

Vi identifierar hot på kluster nivån värd och AKS. Fullständig information finns i [hot skydd för Azure-behållare](threat-protection.md#azure-containers).


## <a name="container-security-faq"></a>Vanliga frågor om container säkerhet

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Vilka typer av avbildningar kan Azure Security Center skanning?
Security Center söker igenom Linux OS-baserade avbildningar som ger shell-åtkomst. 

Qualys-skannern har inte stöd för Super minimalist-bilder som [Docker Scratch](https://hub.docker.com/_/scratch/) images, eller "Distroless"-avbildningar som bara innehåller ditt program och dess körnings beroenden (utan paket hanteraren, Shell eller OS).

### <a name="how-does-we-scan-azure-security-center-scan-an-image"></a>Hur skannar vi Azure Security Center skanna en avbildning?
Avbildningen extraheras från registret. Den körs sedan i en isolerad sandbox med Qualys-skannern som extraherar en lista över kända sårbarheter.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Hur ofta Azure Security Center Skanna mina bilder?
Avbildnings genomsökningar utlöses vid varje push-överföring.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Kan jag få Sök resultatet via REST API?
Ja. Resultaten är under [Underbedömningar REST API](/rest/api/securitycenter/subassessments/list/). Dessutom kan du använda Azure Resource Graph (ARG), Kusto API för alla resurser: en fråga kan hämta en speciell sökning.
 

## <a name="next-steps"></a>Nästa steg

Mer information om behållar säkerhet i Azure Security Center finns i följande relaterade artiklar:

* Information om hur du visar säkerhets position för dina behållar resurser finns i avsnittet behållare i [skydda dina datorer och program](security-center-virtual-machine-protection.md#containers).

* Information om [integrering med Azure Kubernetes-tjänsten](azure-kubernetes-service-integration.md)

* Information om [integreringen med Azure Container Registry](azure-container-registry-integration.md)