---
title: OS-Uppdateringsversion i Azure Security Center | Microsoft Docs
description: "Den här artikeln visar hur du implementerar rekommenderar Azure Security Center ** Update OS-version **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
ms.openlocfilehash: ce0d178914907750e5da59f223a4b1e04b9bb6fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="update-os-version-in-azure-security-center"></a>Uppdatera OS-version i Azure Security Center
För virtuella datorer (VM) i molntjänster rekommenderar Azure Security Center att operativsystem (OS) uppdateras om det finns en nyare version.  Endast cloud services webb- och arbetsroller roller som körs i produktion fack övervakas.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
> 
> 

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **uppdatering OS-version**.
   ![Uppdatera OS-versionen][1]
2. Gör det öppnas bladet **uppdatering OS-version**. Följ stegen i det här bladet för att uppdatera versionen av Operativsystemet.

## <a name="see-also"></a>Se även
Den här artikeln visades hur du implementerar Security Center-rekommendationen ”uppdatera OS-version”. Om du vill veta mer om molntjänster och uppdatera OS-version för en tjänst i molnet, se:

* [Översikt över cloud Services](../cloud-services/cloud-services-choose-me.md)
* [Så här uppdaterar du en tjänst i molnet](../cloud-services/cloud-services-update-azure-service.md)
* [Så här konfigurerar du molntjänster](../cloud-services/cloud-services-how-to-configure-portal.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) --Azure security nyheter och information.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
