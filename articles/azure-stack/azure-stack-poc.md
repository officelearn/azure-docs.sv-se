---
title: Vad är Azure Stack? | Microsoft Docs
description: Med Azure Stack kan du köra Azure-tjänster i ditt datacenter.
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
ms.date: 02/25/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: 65894ccd9514bce1d429b336f8bd5e6674048e65
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820271"
---
# <a name="what-is-azure-stack"></a>Vad är Azure Stack?

Microsoft Azure Stack är en hybridmolnplattform som gör det möjligt att leverera Azure-tjänster i ditt datacenter. Den här plattformen är utformad för att stödja dina växande affärsbehov. Azure Stack kan aktivera nya scenarier för dina moderna program, till exempel kant- och frånkopplade miljöer, eller uppfylla specifika krav på säkerhet och efterlevnad.

Azure Stack erbjuds i två distributionsalternativ, utifrån dina behov.


## <a name="azure-stack-development-kit"></a>Azure Stack Development Kit

Microsoft [Azure Stack Development Kit (ASDK)](./asdk/asdk-what-is.md) är en ennodsdistribution av Azure Stack. Du kan använda den för att utvärdera Azure Stack och för att lära dig mer om plattformen.  Du kan också använda ASDK som en miljö för att skapa appar med hjälp av API: er och verktyg som är förenligt med Azure.

>[!Note]
>ASDK är inte avsedd att användas som en produktionsmiljö.

ASDK har följande begränsningar:

* ASDK är associerad med en enda Azure Active Directory (AD Azure) eller en identitetsprovider för Active Directory Federation Services (AD FS). Du kan skapa flera användare i den här katalogen och tilldela prenumerationer till varje användare.
* Eftersom Azure Stack-komponenterna distribueras på en enda värddator, finns det begränsad fysiska resurser för klientens resurser. Den här konfigurationen är inte avsedd att skala eller prestanda.
* Scenarier för nätverk är begränsade på grund av värddator och NIC-distributionskrav.

## <a name="azure-stack-integrated-systems"></a>Azure Stack-integrerade system
Azure Stack-integrerade system erbjuds via ett partnerskap mellan Microsoft och [maskinvarupartners](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), och ger en lösning som innebär såväl innovation i molnet som enkel och smidig databehandling. Eftersom Azure Stack erbjuds som ett integrerat maskinvaru- och programvarusystem får du den flexibilitet och kontroll du behöver och även möjligheten att förnya från molnet. Azure Stack-integrerat system i intervallet från 4 – 16 noder och stöds gemensamt av maskinvara partner och Microsoft.  Med Azure Stack-integrerade system kan du skapa nya scenarier och distribuera nya lösningar för dina produktionsarbetsbelastningar.

## <a name="next-steps"></a>Nästa steg

[Huvudfunktioner och koncept](azure-stack-key-features.md)

[Azure Stack: En utökning av Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
