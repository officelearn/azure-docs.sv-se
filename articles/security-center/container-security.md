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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 9886f41b25e7c1b34f9805a2c16b716c642ae90e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76936297"
---
# <a name="container-security-in-security-center"></a>Behållar säkerhet i Security Center

Azure Security Center är en Azure-inbyggd lösning för behållar säkerhet. Security Center är också det optimala fönstret med glas miljö för att skydda dina moln arbets belastningar, virtuella datorer, servrar och behållare.

Den här artikeln beskriver hur du kan förbättra, övervaka och upprätthålla säkerheten för dina behållare och deras appar. Du lär dig hur Security Center hjälper till med dessa kärn aspekter av behållar säkerhet:

* Sårbarhets hantering
* Härdning av behållarens miljö
* Körnings skydd

[fliken behållar säkerhet för ![Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

## <a name="vulnerability-management---scanning-container-images-preview"></a>Sårbarhets hantering – Skanna behållar avbildningar (för hands version)
Kontrol lera att du är på Security Center standard nivå (se [prissättning](/azure/security-center/security-center-pricing)) för att övervaka ARM-baserade Azure Container Registry. Aktivera sedan de valfria paketen för behållar register. När en ny avbildning överförs, Security Center skannar avbildningen med hjälp av en skanner från den branschledande sårbara sårbarheten hos leverantören, Qualys.

När problem påträffas – av Qualys eller Security Center – får du ett meddelande i Security Center instrument panel. För varje sårbarhet ger Security Center rekommendationer som kan åtgärdas, tillsammans med en allvarlighets grad och vägledning för hur du kan åtgärda problemet. Information om Security Centers rekommendationer finns i [referens listan för rekommendationer](recommendations-reference.md).

## <a name="environment-hardening"></a>Miljö härdning

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Kontinuerlig övervakning av Docker-konfigurationen
Azure Security Center identifierar ohanterade behållare som finns på virtuella IaaS Linux-datorer eller andra Linux-datorer som kör Docker-behållare. Security Center kontinuerligt bedömer konfigurationen av dessa behållare. Den jämför sedan dem med [Center for Internet Security (CIS) Docker benchmark](https://www.cisecurity.org/benchmark/docker/)). 

Security Center innehåller hela ruleset av CIS Docker-benchmark och varnar dig om dina behållare inte uppfyller någon av kontrollerna. När det hittar felkonfigurationer genererar Security Center säkerhets rekommendationer. Använd **sidan rekommendationer** för att se rekommendationer och åtgärda problem. Du kan också se rekommendationerna på fliken **behållare** som visar alla virtuella datorer som distribueras med Docker. När du utforskar säkerhets problemen på en virtuell dator innehåller Security Center ytterligare information om behållarna på datorn. Sådan information omfattar Docker-versionen och antalet avbildningar som körs på värden. Mer information om rekommendationerna finns [här](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-protection). 

>[!NOTE]
> Dessa CIS-benchmark-kontroller kan inte köras på AKS-hanterade instanser eller Databricks-hanterade virtuella datorer.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Kontinuerlig övervakning av dina Kubernetes-kluster (för hands version)
Security Center fungerar tillsammans med Azure Kubernetes service (AKS), Microsofts hanterade behållar Orchestration-tjänst för utveckling, distribution och hantering av program i behållare.

AKS tillhandahåller säkerhets kontroller och insyn i säkerhets position i dina kluster. Security Center använder dessa funktioner för att:
* Övervaka kontinuerligt konfigurationen av dina AKS-kluster
* Skapa säkerhets rekommendationer som är justerade med bransch standarder

Information om Security Centers rekommendationer finns i [säkerhet för virtuella datorer](security-center-virtual-machine-protection.md).

## <a name="run-time-protection---real-time-threat-detection"></a>Kör tids skydd – real tids identifiering av hot

Security Center tillhandahåller hot identifiering i real tid för dina behållares miljöer och genererar aviseringar för misstänkta aktiviteter. Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina containrar.

Vi identifierar hot på kluster nivån värd och AKS. Fullständig information finns i [hot identifiering för Azure-behållare](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-).

## <a name="to-view-the-security-posture-of-your-container-related-resources"></a>Så här visar du säkerhets position för dina behållar resurser
1.  Öppna Security Center sidan **compute & Apps** .
2.  Klicka på fliken **behållare** . Position för dina AKS-kluster, ACR-register och virtuella datorer som kör Docker visas.

## <a name="next-steps"></a>Nästa steg

Mer information om behållar säkerhet i Azure Security Center finns i:
* Information om [integrering med Azure Kubernetes-tjänsten](azure-kubernetes-service-integration.md)

* Information om [integreringen med Azure Container Registry](azure-container-registry-integration.md)