---
title: Så här installerar du IoT Edge på Kubernetes | Microsoft-dokument
description: Lär dig hur du installerar IoT Edge på Kubernetes med hjälp av en lokal klustermiljö för utveckling
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471293"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Så här installerar du IoT Edge på Kubernetes (förhandsversion)

IoT Edge kan integreras med Kubernetes med det som ett flexibelt, högtillgänge med infrastrukturlager. Här passar det här stödet i en IoT Edge-lösning på hög nivå:

![k8s intro](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>En bra mental modell för denna integration är att tänka på Kubernetes som en annan driftsmiljö IoT Edge applikationer kan köras på förutom Linux och Windows.

## <a name="architecture"></a>Arkitektur 
På Kubernetes tillhandahåller IoT Edge *CRD (Custom Resource Definition)* för distributioner av kantarbetsbelastningar. IoT Edge Agent tar på sig rollen som en *CRD-styrenhet* som stämmer av önskat tillstånd som hanteras i molnet med det lokala klustertillståndet.

Modulens livslängd hanteras av Kubernetes scheduler, som upprätthåller modultillgänglighet och väljer sin placering. IoT Edge hanterar kantprogramplattformen som körs överst och stämmer kontinuerligt av önskat tillstånd som anges i IoT Hub med tillståndet på kantklustret. Programmodellen är fortfarande den välbekanta modellen baserad på IoT Edge-moduler och vägar. IoT Edge Agent-styrenheten utför *automatisk* översättning IoT Edge programmodell till Kubernetes inbyggda konstruktioner som poddar, distributioner, tjänster etc.

Här är ett arkitekturdiagram på hög nivå:

![kubernetes båge](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Varje komponent i kantdistributionen är begränsad till ett Kubernetes-namnområde som är specifikt för enheten, vilket gör det möjligt att dela samma klusterresurser mellan flera kantenheter och deras distributioner.

>[!NOTE]
>IoT Edge på Kubernetes är i [offentlig förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="tutorials-and-references"></a>Självstudier och referenser 

Se [IoT Edge på Kubernetes förhandsgranska docs mini-site](https://aka.ms/edgek8sdoc) för mer information, inklusive djupgående självstudier och referenser.
