---
title: Vad är Azure Stack? | Microsoft Docs
description: Azure-stacken kan du köra Azure-tjänster i ditt datacenter.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/04/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 848df59b01cc0c03b9a5f3dae2644d469c8651bb
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234894"
---
# <a name="what-is-azure-stack"></a>Vad är Azure Stack?

Microsoft Azure-stacken är en hybrid cloud plattform som gör att du kan leverera Azure-tjänster i ditt datacenter. Den här plattformen är utformad för att stödja dina växande affärsbehov. Azure-stacken kan aktivera nya scenarier för dina moderna program, till exempel kant och frånkopplade miljöer eller uppfyller specifika krav för säkerhet och efterlevnad.

Azure-stacken erbjuds i två distributionsalternativ för dina behov.

## <a name="azure-stack-integrated-systems"></a>Integrerade Azure Stack-system
Azure-stacken integrerade system erbjuds via ett partnerskap av Microsoft och [maskinvarupartners](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), skapa en lösning som erbjuder molntjänster takt innovation och datorfunktioner management enkelhet. Eftersom Azure Stack erbjuds som en integrerad maskinvara och programvarusystem har flexibilitet och kontroll som du behöver, tillsammans med möjligheten att förnya från molnet. Azure Stack integrerat system i intervallet från 4 – 12 noder och stöds gemensamt av maskinvaran partner och Microsoft.  Använd Azure-stacken integrerat system för att skapa nya scenarier och distribuera nya lösningar för din produktionsarbetsbelastningar.

## <a name="azure-stack-development-kit"></a>Azure Stack Development Kit

Microsoft [Azure Stack Development Kit (ASDK)](.\asdk\asdk-what-is.md) är en nod distribution av Azure-stacken, som du kan använda för att utvärdera och lär dig mer om Azure-stacken.  Du kan också använda ASDK som en utvecklare miljö för att skapa appar som använder API: er och verktyg som är kompatibel med Azure.

>[!Note]
>ASDK är inte avsedd att användas som en produktionsmiljö.

ASDK har följande begränsningar:

* ASDK är associerad med en enda Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) identitetsleverantör. Du kan skapa flera användare i den här katalogen och tilldela varje användare prenumerationer.
* Eftersom Azure Stack-komponenter har distribuerats på en värddator finns begränsad fysiska resurser tillgängliga för klienternas resurser. Den här konfigurationen är inte avsedd att skala eller prestanda.
* Scenarier för nätverk är begränsade på grund av en värd och NIC-distributionskrav.

## <a name="next-steps"></a>Nästa steg

- [Huvudfunktioner och koncept](azure-stack-key-features.md)
- [Azure-stacken: Ett tillägg i Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
