---
title: Azure-stacken integrerat system anslutning modeller | Microsoft Docs
description: "Kontrollera distributionen planeringsbeslut för flera noder Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: ff56ced9f1d43780097cd65e58b492daaba9b61d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Azure-stacken integrerat system anslutning modeller
Om du är intresserad av en Azure-stacken integrerat system, måste du förstå [flera datacenter integration överväganden](azure-stack-datacenter-integration.md) för distribution av Azure-stacken att avgöra hur systemet passar i ditt datacenter. Du måste dessutom bestämma exakt hur du ska integrera Azure Stack i molnmiljön hybrid. Den här artikeln innehåller en översikt över dessa viktiga beslut, inklusive Azure-anslutningen, identitet store och fakturering modellen beslut.

Om du vill köpa ett integrerat system hjälper maskinvaruleverantören OEM-tillverkaren (OEM) hjälper dig att en stor del av planeringsprocessen i detalj. De kan också utföra verklig distribution.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Välj en anslutning för Azure-stacken distributionsmodell
Du kan välja att distribuera Azure-stacken ansluten till internet (och Azure) eller kopplas från. För att få största möjliga nytta av Azure-stacken, inklusive hybridscenarion mellan Azure-stacken och Azure, vill du distribuera ansluten till Azure. Detta val definierar vilka alternativ som är tillgängliga för din identitet store (Azure Active Directory eller Active Directory Federation Services) och fakturering modell (betala som används baserat fakturerings- eller kapacitet-baserade fakturering) som sammanfattas i följande diagram och tabell: 

![Azure Stack-distribution och fakturering scenarier](media/azure-stack-deployment-decisions/azure-stack-scenarios.png)   
  
> [!IMPORTANT]
> Detta är en viktiga beslut! Att välja Active Directory Federation Services (AD FS) eller Azure Active Directory (AD Azure) är en enstaka beslut som du måste göra vid tidpunkten för distribution. Du kan ändra detta senare utan att distribuera hela systemet igen.  


|Alternativ|Ansluten till Azure|Frånkopplad från Azure|
|-----|-----|-----|
|Azure AD|![Stöds](media/azure-stack-deployment-decisions/check.png)| |
|AD FS|![Stöds](media/azure-stack-deployment-decisions/check.png)|![Stöds](media/azure-stack-deployment-decisions/check.png)|
|Förbrukningsbaserad fakturering|![Stöds](media/azure-stack-deployment-decisions/check.png)| |
|Kapacitet-baserade fakturering|![Stöds](media/azure-stack-deployment-decisions/check.png)|![Stöds](media/azure-stack-deployment-decisions/check.png)|
|Ladda ned uppdateringspaket direkt till Azure-stacken|![Stöds](media/azure-stack-deployment-decisions/check.png)|  |

När du har valt Azure anslutning modellen som ska användas för distribution av Azure-stacken är måste ytterligare, anslutning beroende beslut göras för Identitetslagret och faktureringsadress. 

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Azure anslutna Azure-stacken distributionsbeslut](azure-stack-connected-deployment.md)
- Lär dig mer om [Azure kopplas från Azure-stacken distributionsbeslut](azure-stack-disconnected-deployment.md)
