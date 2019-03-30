---
title: Azure Security Center-inställningar | Microsoft Docs
description: Konfigurera inställningar för Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: ec674641991a1b5a1e0ca92c133be235dd91dfae
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666502"
---
# <a name="security-center-settings"></a>Security Center-inställningar
Den här artikeln innehåller en översikt över inställningar i Security Center.

Följande inställningar kan nås under säkerhetsprincip:

- **Datainsamling**: Fastställer agentetablering och [datainsamling](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) inställningar.
- **Säkerhetsprincip**: Anger vilket kontrollerar Security Center ska övervaka och rekommenderar. Du kan redigera den [säkerhetsprincip](tutorial-security-policy.md) i Security Center. Du kan också använda [Azure Policy](tutorial-security-policy.md) för att skapa nya definitioner, definiera ytterligare principer och tilldela principer över hanteringsgrupper. 
- **E-postmeddelanden**: Fastställer säkerhetskontakter, och [e-postavisering](security-center-provide-security-contact-details.md) inställningar.
- **Prisnivå**: Definierar kostnadsfritt eller standardpris [prissättningsval](security-center-pricing.md). Vilken nivå du väljer avgör vilka Security Center-funktioner som blir tillgängliga för resurser i området. Du kan ange en nivå för prenumerationer och arbetsytor.

> [!NOTE]
> Du kan ange alla dessa per prenumeration. Du kan ange endast datainsamling och prisnivå för arbetsytor.
>


## <a name="who-can-edit-security-policies"></a>Vem kan redigera säkerhetsprinciper?
Security Center använder rollbaserad åtkomstkontroll (RBAC), som erbjuder inbyggda roller som kan tilldelas till användare, grupper och tjänster i Azure. När användarna öppnar Security Center, visas endast information som är relaterad till resurser som de har åtkomst till. Vilket innebär att användare har tilldelats rollen *ägare*, *deltagare*, eller *läsare* till prenumerationen som resursen hör till. Förutom dessa roller finns två specifika roller i Security Center:

- **Säkerhetsläsare**: Har läsbehörigheter till Security Center, som innehåller rekommendationer, aviseringar, principer och hälsa, men de kan inte göra ändringar.
- **Säkerhetsadministratör**: Ha samma visa rättigheter som *säkerhetsläsare*, och de kan också uppdatera säkerhetsprinciper och stänga rekommendationer och aviseringar.


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig om säkerhetsprinciper i Azure Security Center. Mer information om Azure Security Center finns i följande artiklar:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md): Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md): Lär dig hur rekommendationerna i Security Center hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Datasäkerhet i Azure Security Center](security-center-data-security.md): Lär dig hur Security Center hanterar och skyddar data.
* [Vanliga frågor om Azure Security Center](security-center-faq.md): Få svar på vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/): Få de senaste nyheterna i Azure-säkerhet och information.
