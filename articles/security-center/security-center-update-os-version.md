---
title: OS-Uppdateringsversion i Azure Security Center | Microsoft Docs
description: Den här artikeln visar hur du implementerar Azure Security Center-rekommendationen **uppdatera OS-version**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 9cd89d861a758d51788dc4180af10882f0144977
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52317372"
---
# <a name="update-os-version-in-azure-security-center"></a>Uppdatera OS-version i Azure Security Center
För virtuella datorer (VM) i cloud services rekommenderar Azure Security Center att operativsystemet (OS) uppdateras om det finns en nyare version tillgänglig.  Endast molntjänster web och worker-roller som körs i produktionsmiljön fack övervakas.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
> 
> 

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **uppdatera OS-version**.
   ![Uppdatera OS-versionen][1]
2. Då öppnas bladet **uppdatera OS-version**. Följ stegen i det här bladet för att uppdatera versionen av Operativsystemet.

## <a name="see-also"></a>Se också
Den här artikeln visar dig hur du implementerar Security Center-rekommendationen ”uppdatera OS-version”. Mer information om molntjänster och uppdatera OS-version för en molntjänst finns:

* [Översikt över cloud Services](../cloud-services/cloud-services-choose-me.md)
* [Så här uppdaterar du en tjänst i molnet](../cloud-services/cloud-services-update-azure-service.md)
* [Så här konfigurerar du Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-azure-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) – senaste nytt i Azure-säkerhet och information.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
