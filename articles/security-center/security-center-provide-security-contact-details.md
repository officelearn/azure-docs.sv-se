---
title: Ange information om säkerhets kontakt i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur du anger säkerhets kontakt uppgifter i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: fabccb7a3f49442a009fd69ee6d3b831760751d2
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201018"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Ange säkerhets kontakt uppgifter i Azure Security Center
Azure Security Center rekommenderar att du anger säkerhets kontakt information för din Azure-prenumeration om du inte redan har gjort det. Den här informationen används av Microsoft för att kontakta dig om Microsoft Security Response Center (MRSC) upptäcker att en obehörig part har kommit åt dina kunddata. MSRC utför en urvals säkerhets övervakning av Azure-nätverket och-infrastrukturen och tar emot Hot information och klagomål från missbruk från tredje part.

Ett e-postmeddelande skickas på den första dagliga förekomsten av en avisering och endast för varningar med hög angelägenhetsgrad. E-postinställningar kan bara konfigureras för prenumerationsprinciper. Resursgrupper inom en prenumeration ärver inställningarna. 

E-postmeddelanden med avisering skickas:
- Endast för aviseringar med hög allvarlighetsgrad
- Till en enda e-postmottagare per typ av avisering per dag  
- Högst 3 e-postmeddelanden skickas till en enda mottagare på samma dag
- Varje e-postmeddelande innehåller en enda avisering, inte en sammanställning av aviseringar
 
Till exempel om ett e-postmeddelande redan har skickats för att meddela dig om en RDP-attack, får du inte ett annat e-postmeddelande om en RDP-attack under samma dag, även om en ny avisering utlöses. 

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.

## Konfigurera e-postmeddelanden för aviseringar<a name="email"></a>

1. Välj **pris & inställningar**i portalen.
1. Klicka på prenumerationen.
1. Klicka på **e-postaviseringar**.

> [!NOTE]
> Om du implementerar en rekommendation väljer du **Ange säkerhets kontakt information**under **rekommendationer**, väljer den Azure-prenumeration som du vill ge kontakt information på. Detta öppnar **e-postaviseringar**.

   ![Ange säkerhetskontaktuppgifter][2]

   * Ange e-postadressen eller adresserna för säkerhets kontakten avgränsade med kommatecken. Det finns inte någon gräns för antalet e-postadresser som du kan ange.
   * Ange ett internationellt telefonnummer för säkerhets kontakt.
   * Om du vill få e-postmeddelanden om aviseringar med hög allvarlighets grad aktiverar du alternativet **skicka mig e-postmeddelanden om aviseringar**.
   * I framtiden kan du välja att skicka e-postmeddelanden till prenumerations ägare. Det här alternativet är för tillfället nedtonat.
   * Välj **Spara** om du vill använda säkerhets kontakt informationen för din prenumeration.

## <a name="see-also"></a>Se också
I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/) – hämta de senaste nyheterna och informationen om Azure-säkerhet.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
