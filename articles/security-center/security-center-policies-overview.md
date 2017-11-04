---
title: "Introduktion till säkerhetsprinciper i Azure Security Center | Microsoft Docs"
description: "Läs mer om Azure Security Center säkerhetsprinciper och de viktigaste funktionerna."
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
ms.openlocfilehash: 95ef2099cb16bcfd550ce2799428f1a16031f535
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2017
---
# <a name="security-policies-overview"></a>Översikt över principer för säkerhet
Det här dokumentet innehåller en översikt över säkerhetsprinciper i Security Center.

## <a name="what-are-security-policies"></a>Vad är säkerhetsprinciper?
En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav. I Security Center kan du definiera principer för dina Azure-prenumerationer, som kan skräddarsys efter arbetsbelastningstyp eller datakänslighet. Till exempel program som använder reglerade data som personligt identifierbar information kan kräva högre säkerhet än andra arbetsbelastningar. 

Security Center-principer innehåller följande komponenter:

- Datainsamling: agenten etablering och [datainsamling](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) inställningar.
- Säkerhetsprincip: avgör vilka kontroller övervakas och rekommenderas av Security Center (redigera den [säkerhetsprincip](https://docs.microsoft.com/en-us/azure/security-center/security-center-policies) med Security Center eller [Azure princip](security-center-azure-policy.md), i begränsad Förhandsgranska för att skapa en ny definitioner, definiera ytterligare principer och tilldela principer för hantering av grupper).
- E-postaviseringar: säkerhet kontakter och [meddelanden via e-](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) inställningar.
- Prisnivån: lediga eller standard [priser val](https://docs.microsoft.com/azure/security-center/security-center-pricing), som bestämmer vilka Security Center-funktioner är tillgängliga för resurser i sitt omfång (kan anges för prenumerationer och resursgrupper arbetsytor). 


## <a name="who-can-edit-security-policies"></a>Vem kan redigera säkerhetsprinciper?
Security Center använder rollbaserad åtkomstkontroll (RBAC), vilket ger inbyggda roller som kan tilldelas användare, grupper och tjänster i Azure. När en användare öppnar Security Center ser de bara information om de resurser som de har åtkomst till. Detta betyder att användaren tilldelas rollen som ägare, deltagare eller läsare för den prenumeration eller resursgrupp som en resurs hör till. Förutom dessa roller finns två specifika roller i Security Center:

- Säkerhet reader: användare som tillhör den här rollen kan visa rättigheter till Security Center, som innehåller rekommendationer, aviseringar, princip och hälsa, men kommer inte kunna göra ändringar.
- Säkerhet admin: samma som säkerhet reader, men det kan också uppdatera säkerhetsprinciper, stänga rekommendationer och aviseringar.


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig om säkerhetsprinciper i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
- [Datasäkerhet i Azure Security Center](security-center-data-security.md) – Lär dig hur data hanteras och garanteras i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Azure security nyheter och information.


