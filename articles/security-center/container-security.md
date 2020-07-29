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
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: c01ed6dbbd6e1f7febfb99df11d2ee67cb1e5465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800619"
---
# <a name="container-security-in-security-center"></a>Behållar säkerhet i Security Center

Azure Security Center är en Azure-inbyggd lösning för att skydda dina behållare. Security Center kan skydda följande behållare resurs typer:



|Resurs |Name  |Information  |
|:---------:|---------|---------|
|![Container värd](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)|Behållar värdar (virtuella datorer som kör Docker)|Security Center söker igenom dina Docker-konfigurationer och ger dig insyn i felkonfigurationer genom att tillhandahålla en lista över alla misslyckade regler som har utvärderats. Security Center innehåller rikt linjer som hjälper dig att snabbt lösa problemen och spara tid. Security Center utvärderar Docker-konfigurationer och ger dig det senaste tillståndet kontinuerligt.|
|![Kubernetes-tjänst](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)|Azure Kubernetes service-kluster (AKS)|Få djupare insyn i dina AKS-noder, moln trafik och säkerhets kontroller med [Security Center det valfria AKS-paketet](azure-kubernetes-service-integration.md) för användare på standard nivå.|
|![Containerregister](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)|Azure Container Registry-register (ACR)|Få djupare insyn i säkerhets riskerna för avbildningarna i dina ARM-baserade ACR-register med [Security Center det valfria ACR-paketet](azure-kubernetes-service-integration.md) för användare på standard nivå.|
||||


Den här artikeln beskriver hur du kan använda dessa paket för att förbättra, övervaka och upprätthålla säkerheten för dina behållare och deras appar. Du lär dig hur Security Center hjälper till med dessa kärn aspekter av behållar säkerhet:

- [Sårbarhets hantering – Skanna behållar avbildningar](#vulnerability-management---scanning-container-images)
- [Miljö härdning-kontinuerlig övervakning av Docker-konfigurationen och Kubernetes-kluster](#environment-hardening)
- [Kör tids skydd – real tids identifiering av hot](#run-time-protection---real-time-threat-detection)

[![Azure Security Center fliken behållar säkerhet](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Instruktioner för hur du använder dessa funktioner finns i [övervaka säkerheten för dina behållare](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images"></a>Sårbarhets hantering – Skanna behållar avbildningar
Kontrol lera att du är på Security Center standard nivå (se [prissättning](/azure/security-center/security-center-pricing)) för att övervaka ARM-baserade Azure Container Registry. Aktivera sedan de valfria paketen för behållar register. När en ny avbildning överförs, Security Center skannar avbildningen med hjälp av en skanner från den branschledande sårbara sårbarheten hos leverantören, Qualys.

När problem påträffas – av Qualys eller Security Center – får du ett meddelande i Security Center instrument panel. För varje sårbarhet ger Security Center rekommendationer som kan åtgärdas, tillsammans med en allvarlighets grad och vägledning för hur du kan åtgärda problemet. Mer information om Security Centers rekommendationer för behållare finns i [referens listan för rekommendationer](recommendations-reference.md#recs-containers).

Security Center filtrerar och klassificerar resultat från skannern. När en bild är felfri, Security Center Markera den som sådan. Security Center skapar endast säkerhets rekommendationer för avbildningar som har problem att lösa. Genom att meddela om det uppstår problem kan Security Center minska risken för oönskade informations aviseringar.

## <a name="environment-hardening"></a>Miljö härdning

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Kontinuerlig övervakning av Docker-konfigurationen
Azure Security Center identifierar ohanterade behållare som finns på virtuella IaaS Linux-datorer eller andra Linux-datorer som kör Docker-behållare. Security Center kontinuerligt bedömer konfigurationen av dessa behållare. Den jämför sedan dem med [Center for Internet Security (CIS) Docker-benchmark](https://www.cisecurity.org/benchmark/docker/).

Security Center innehåller hela ruleset av CIS Docker-benchmark och varnar dig om dina behållare inte uppfyller någon av kontrollerna. När det hittar felkonfigurationer genererar Security Center säkerhets rekommendationer. Använd **sidan rekommendationer** för att se rekommendationer och åtgärda problem. Du kan också se rekommendationerna på fliken **behållare** som visar alla virtuella datorer som distribueras med Docker. 

Mer information om relevanta Security Center rekommendationer som kan visas för den här funktionen finns i [avsnittet container](recommendations-reference.md#recs-containers) i referens tabellen för rekommendationer.

När du utforskar säkerhets problemen för en virtuell dator innehåller Security Center ytterligare information om behållarna på datorn. Sådan information omfattar Docker-versionen och antalet avbildningar som körs på värden. 

>[!NOTE]
> Dessa CIS-benchmark-kontroller kan inte köras på AKS-hanterade instanser eller Databricks-hanterade virtuella datorer.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Kontinuerlig övervakning av dina Kubernetes-kluster
Security Center fungerar tillsammans med Azure Kubernetes service (AKS), Microsofts hanterade behållar Orchestration-tjänst för utveckling, distribution och hantering av program i behållare.

AKS tillhandahåller säkerhets kontroller och insyn i säkerhets position i dina kluster. Security Center använder dessa funktioner för att:
* Övervaka kontinuerligt konfigurationen av dina AKS-kluster
* Skapa säkerhets rekommendationer som är justerade med bransch standarder

Mer information om relevanta Security Center rekommendationer som kan visas för den här funktionen finns i [avsnittet container](recommendations-reference.md#recs-containers) i referens tabellen för rekommendationer.

## <a name="run-time-protection---real-time-threat-detection"></a>Kör tids skydd – real tids identifiering av hot

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]




## <a name="next-steps"></a>Nästa steg

I den här översikten har du lärt dig om kärn elementen i behållar säkerhet i Azure Security Center. Fortsätt till [hur du övervakar säkerheten för dina behållare](monitor-container-security.md).
> [!div class="nextstepaction"]
> [Övervaka säkerheten för dina behållare](monitor-container-security.md)