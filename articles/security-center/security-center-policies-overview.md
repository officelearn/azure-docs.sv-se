---
title: Säkerhetsprincipinställningar för Azure Security Center | Microsoft Docs
description: Konfigurera inställningar för säkerhetsprincip Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 1439696c35b56a5c65a976fe4e5991dec1c1833e
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028632"
---
# <a name="security-policy-settings"></a>Inställningar för säkerhetsprincip
Den här artikeln innehåller en översikt över säkerhet principinställningar i Security Center.

## <a name="what-are-security-policies"></a>Vad är säkerhetsprinciper?
En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav. Du kan definiera principer för dina Azure-prenumerationer och anpassa dem till din typ av arbetsbelastning eller känslighet för dina data i Azure Security Center. Program som använder reglerade data, till exempel personligt identifierbar information kan till exempel kräva en högre säkerhetsnivå än andra arbetsbelastningar.

Du kan ange följande under säkerhetsprincip:

- **Datainsamling**: fastställer agentetablering och [datainsamling](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) inställningar.
- **Säkerhetsprincip**: avgör vilket kontrollerar Security Center ska övervaka och rekommenderar. Du kan redigera den [säkerhetsprincip](security-center-policies.md) i Security Center. Du kan också använda [Azure Policy](security-center-azure-policy.md) för att skapa nya definitioner, definiera ytterligare principer och tilldela principer över hanteringsgrupper. 
- **E-postmeddelanden**: fastställer säkerhetskontakter, och [e-postavisering](security-center-provide-security-contact-details.md) inställningar.
- **Prisnivån**: definierar kostnadsfritt eller standardpris [prissättningsval](security-center-pricing.md). Vilken nivå du väljer avgör vilka Security Center-funktioner som blir tillgängliga för resurser i området. Du kan ange en nivå för prenumerationer, resursgrupper och arbetsytor.

> [!NOTE]
> Du kan ange alla dessa per prenumeration. Du kan ange endast datainsamling och prisnivå för arbetsytor. Du kan ange endast prisnivå för resursgrupper.
>


## <a name="who-can-edit-security-policies"></a>Vem kan redigera säkerhetsprinciper?
Security Center använder rollbaserad åtkomstkontroll (RBAC), som erbjuder inbyggda roller som kan tilldelas till användare, grupper och tjänster i Azure. När användarna öppnar Security Center, visas endast information som är relaterad till resurser som de har åtkomst till. Vilket innebär att användare har tilldelats rollen *ägare*, *deltagare*, eller *läsare* i gruppen prenumeration eller resursgrupp som en resurs hör till. Förutom dessa roller finns två specifika roller i Security Center:

- **Säkerhetsläsare**: har visa rättigheter till Security Center, som innehåller rekommendationer, aviseringar, principer och hälsa, men de kan inte göra ändringar.
- **Säkerhetsadministratör**: har samma visa rättigheter som *säkerhetsläsare*, och de kan också uppdatera säkerhetsprinciper och stänga rekommendationer och aviseringar.


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig om säkerhetsprinciper i Azure Security Center. Mer information om Azure Security Center finns i följande artiklar:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md): Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md): Lär dig hur rekommendationerna i Security Center hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
- [Datasäkerhet i Azure Security Center](security-center-data-security.md): Lär dig hur Security Center hanterar och skyddar data.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Få svar på vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Nyheter i Azure-säkerhet och information.
