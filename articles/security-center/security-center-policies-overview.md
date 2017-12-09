---
title: "Introduktion till säkerhetsprinciper i Azure Security Center | Microsoft Docs"
description: "Läs mer om Azure Security Center säkerhetsprinciper och viktiga funktioner."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 60cc65bb94e05da1c0b7ee20930c0530f46e71ec
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="security-policies-overview"></a>Översikt över principer för säkerhet
Den här artikeln innehåller en översikt över säkerhetsprinciper i Security Center.

## <a name="what-are-security-policies"></a>Vad är säkerhetsprinciper?
En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav. Du kan definiera principer för dina Azure-prenumerationer och anpassa dem till din typ av arbetsbelastning eller känslighet för dina data i Azure Security Center. Program som använder reglerade data, till exempel personligt identifierbar information kan till exempel kräva en högre säkerhetsnivå än andra arbetsbelastningar. 

Security Center-principer innehåller följande komponenter:

- **Datainsamling**: Anger agent etablering och [datainsamling](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) inställningar.
- **Säkerhetsprincip**: avgör som styr Security Center Övervakare och rekommenderas. Du kan redigera den [säkerhetsprincip](https://docs.microsoft.com/en-us/azure/security-center/security-center-policies) i Security Center. Du kan också använda [Azure princip](security-center-azure-policy.md) (i begränsad förhandsvisning) definiera ytterligare principer för att skapa nya definitioner, och tilldela principer för av hanteringsgrupper.
- **E-postmeddelanden**: Anger säkerhet kontakter och [meddelanden via e-](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) inställningar.
- **Prisnivån**: definierar ledigt eller standard [priser markeringen](https://docs.microsoft.com/azure/security-center/security-center-pricing). Den nivå som du väljer avgör vilka Security Center-funktioner är tillgängliga för resurser i sitt omfång. Du kan ange en nivå för prenumerationer och resursgrupper arbetsytor. 


## <a name="who-can-edit-security-policies"></a>Vem kan redigera säkerhetsprinciper?
Security Center använder rollbaserad åtkomstkontroll (RBAC), vilket ger inbyggda roller som kan tilldelas användare, grupper och tjänster i Azure. När användarna öppnar Security Center, visas endast information som är relaterad till resurser som de har åtkomst till. Vilket innebär att användare inte har tilldelats rollen för *ägare*, *deltagare*, eller *reader* till prenumerationen eller resursen gruppen som en resurs tillhör. Förutom dessa roller finns två specifika roller i Security Center:

- **Säkerhet reader**: har visa rättigheter till Security Center, som innehåller rekommendationer, aviseringar, princip och hälsa, men de kan inte göra ändringar.
- **Säkerhet admin**: har samma visa rättigheter som *säkerhet reader*, och de kan också uppdatera säkerhetsprincipen och stänga rekommendationer och aviseringar.


## <a name="next-steps"></a>Nästa steg
I den här artikeln får du lära dig om säkerhetsprinciper i Azure Security Center. Mer information om Azure Security Center finns i följande artiklar:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md): Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md): Lär dig hur Security Center hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
- [Datasäkerhet i Azure Security Center](security-center-data-security.md): Lär dig hur Security Center hanterar och skyddar data.
* [Vanliga frågor om Azure Security Center](security-center-faq.md): få svar på vanliga frågor om hur du använder tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Azure-Säkerhetsnyheter och information.


