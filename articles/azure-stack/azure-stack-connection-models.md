---
title: Integrerade Azure Stack-system anslutning modeller | Microsoft Docs
description: Kontrollera distributionen planeringsbeslut för flera noder Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 9760e6b9cdcd6f03f4377277f3426189b1fe0a61
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182025"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Integrerade Azure Stack-system anslutning modeller
Om du är intresserad av att köpa ett integrerat Azure Stack-system, måste du förstå [flera datacenter integration överväganden](azure-stack-datacenter-integration.md) för Azure Stack-distributioner att fastställa hur systemet ryms i ditt datacenter. Dessutom måste du bestämma hur du ska integrera Azure Stack i din hybridmiljö i molnet. Den här artikeln innehåller en översikt över dessa viktiga besluten, inklusive Azure-anslutningen, identitetsarkiv, och fakturering modellen beslut.

Om du vill köpa ett integrerat system hjälper din maskinvaruleverantör för OEM-tillverkare (original equipment manufacturer) hjälper dig att mycket av planeringsprocessen i detalj. De kan också utföra den faktiska distributionen.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Välj en anslutning för Azure Stack distributionsmodell
Du kan välja att distribuera Azure Stack är ansluten till internet (och till Azure) eller frånkopplad. För att få utnyttja från Azure Stack, inklusive hybridscenarier mellan Azure Stack och Azure, kan du distribuera är ansluten till Azure. Det här alternativet definierar vilka alternativ som är tillgängliga för din Identitetslagret (Azure Active Directory eller Active Directory Federation Services) och faktureringsmodell (betalar du användning-baserade fakturerings- eller kapacitetsbaserad fakturering) som sammanfattas i följande diagram och tabell: 

![Azure Stack-distributioner och fakturering scenarier](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> Det här är en viktig beslutspunkten! Att välja Active Directory Federation Services (AD FS) eller Azure Active Directory (Azure AD) är ett enstaka beslut som du måste göra vid tidpunkten för distribution. Du kan inte ändra detta senare utan att distribuera hela systemet.  


|Alternativ|Är ansluten till Azure|Frånkopplad från Azure|
|-----|-----|-----|
|Azure AD|![Stöds](media/azure-stack-connection-models/check.png)| |
|AD FS|![Stöds](media/azure-stack-connection-models/check.png)|![Stöds](media/azure-stack-connection-models/check.png)|
|Konsumtionsbaserad fakturering|![Stöds](media/azure-stack-connection-models/check.png)| |
|Kapacitetsbaserad fakturering|![Stöds](media/azure-stack-connection-models/check.png)|![Stöds](media/azure-stack-connection-models/check.png)|
|Ladda ned uppdateringspaket direkt till Azure Stack|![Stöds](media/azure-stack-connection-models/check.png)|  |

När du har bestämt dig på Azure-anslutningen modellen som ska användas för Azure Stack-distributioner kan måste ytterligare, anslutning-beroende beslut göras för Identitetslagret och fakturera. 

## <a name="next-steps"></a>Nästa steg

[Azure anslutna Azure Stack-distributionsbeslut](azure-stack-connected-deployment.md)

[Azure frånkopplade Azure Stack-distributionsbeslut](azure-stack-disconnected-deployment.md)
