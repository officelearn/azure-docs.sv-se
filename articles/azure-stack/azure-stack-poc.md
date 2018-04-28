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
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: d54f392eddfcca99e7fe0b037b9efd0a4e90433c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-stack"></a>Vad är Azure Stack?

Microsoft Azure-stacken är en hybrid cloud plattform som gör att du kan leverera Azure-tjänster från din organisations datacenter.  Azure-stacken är utformat för att aktivera nya scenarier för moderna program i viktiga scenarier som kant och frånkopplade miljöer eller möte specifika efterlevnads- och säkerhetskrav.  Azure-stacken erbjuds i två distributionsalternativ för dina behov.

## <a name="azure-stack-integrated-systems"></a>Integrerade Azure Stack-system
Azure-stacken integrerade system erbjuds via ett partnerskap av Microsoft och [maskinvarupartners](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), skapar en lösning som erbjuder molntjänster takt innovation belastningsutjämnade med enkel hantering.  Eftersom Azure Stack erbjuds som ett integrerat system av maskinvara och programvara, erbjuds rätt mängd flexibilitet och kontroll, samtidigt som fortfarande använder innovation från molnet.  Azure Stack integrerat system i intervallet från 4 – 12 noder och stöds gemensamt av maskinvaran partner och Microsoft.  Använd Azure-stacken integrerat system för att aktivera nya scenarier för din produktionsarbetsbelastningar.    

## <a name="azure-stack-development-kit"></a>Azure Stack Development Kit
Microsoft [Azure Stack Development Kit (ASDK)](.\asdk\asdk-what-is.md) är en nod distribution av Azure-stacken, som du kan använda för att utvärdera och lär dig mer om Azure-stacken.  Du kan också använda ASDK som en utvecklare-miljö där du kan utveckla med API: er och verktygsuppsättning som är konsekventa med Azure. ASDK är inte avsedd att användas som en produktionsmiljö.

ASDK har följande begränsningar:
* ASDK är associerad med en enda Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) identitetsleverantör. Du kan skapa flera användare i den här katalogen och tilldela varje användare prenumerationer.
* Med alla komponenter har distribuerats på samma dator, finns det begränsad fysiska resurser tillgängliga för klienternas resurser. Den här konfigurationen är inte avsedd för utvärdering av skala eller prestanda.
* Scenarier för nätverk är begränsade på grund av en enda värd-NIC-krav.  

## <a name="next-steps"></a>Nästa steg
[Huvudfunktioner och koncept](azure-stack-key-features.md)

[Azure-stacken: Ett tillägg i Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)

