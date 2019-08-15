---
title: Uppdatera OS-version i Azure Security Center | Microsoft Docs
description: Den här artikeln visar hur du implementerar uppdaterings **versionen**av Azure Security Center rekommendationer.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: a17bc3cf8c8f1ffbb2a91e17944bfc1c203f439d
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "60905866"
---
# <a name="update-os-version-in-azure-security-center"></a>Uppdatera OS-version i Azure Security Center
För Virtual Machines (VM) i Cloud Services, rekommenderar Azure Security Center att operativ systemet (OS) uppdateras om det finns en senare version tillgänglig.  Endast webb-och arbets roller för moln tjänster som körs i produktions platser övervakas.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
> 
> 

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. På bladet **rekommendationer** väljer du **Uppdatera OS-version**.
   ![Uppdatera OS-versionen][1]
2. Då öppnas bladet **Uppdatera operativ system versionen**. Uppdatera operativ system versionen genom att följa stegen i det här bladet.

## <a name="see-also"></a>Se också
I den här artikeln visar vi hur du implementerar Security Center rekommendationer "Update OS-version". Mer information om moln tjänster och uppdatering av OS-versionen för en moln tjänst finns i:

* [Översikt över Cloud Services](../cloud-services/cloud-services-choose-me.md)
* [Så här uppdaterar du en moln tjänst](../cloud-services/cloud-services-update-azure-service.md)
* [Så här konfigurerar du Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/) – hämta de senaste nyheterna och informationen om Azure-säkerhet.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
