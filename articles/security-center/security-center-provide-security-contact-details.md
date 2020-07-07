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
ms.openlocfilehash: 08ad761e81909e6ab23c7c07f5ce05865136bc47
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204108"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Ange säkerhets kontakt uppgifter i Azure Security Center
Azure Security Center rekommenderar att du uppger kontaktuppgifter för säkerhetsrelaterade frågor relaterade till din Azure-prenumeration om du inte redan har gjort det. Den här informationen används av Microsoft för att kontakta dig om Microsoft Security Response Center (MRSC) upptäcker att en obehörig part har kommit åt dina kunddata. MSRC utför utvald säkerhetsövervakning av Azure-nätverket och infrastrukturen och tar emot hotinformation samt klagomål om missbruk från tredje part.

Ett e-postmeddelande skickas på den första dagliga förekomsten av en avisering och endast för varningar med hög angelägenhetsgrad. E-postinställningar kan bara konfigureras för prenumerationsprinciper. Resursgrupper inom en prenumeration ärver inställningarna. Aviseringar är bara tillgängliga på standard-nivån för Azure Security Center.

E-postmeddelanden med avisering skickas:
- Till en enda e-postmottagare per typ av avisering per dag  
- Högst 3 e-postmeddelanden skickas till en enda mottagare på samma dag
- Varje e-postmeddelande innehåller en enda avisering, inte en sammanställning av aviseringar
- Endast för aviseringar med hög allvarlighetsgrad

> [!TIP]
> För aviseringar med andra allvarlighets nivåer skapar du en [arbets flödes automatisering](workflow-automation.md) som använder en Logic app som skickar e-post till relevant personal.
 
Till exempel om ett e-postmeddelande redan har skickats för att meddela dig om en RDP-attack, får du inte ett annat e-postmeddelande om en RDP-attack under samma dag, även om en ny avisering utlöses. 

> [!IMPORTANT]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är inte en steg-för-steg-guide.

## <a name="set-up-email-notifications-for-alerts"></a>Konfigurera e-postmeddelanden för aviseringar<a name="email"></a>

1. Öppna sidan **e-postaviseringar** som användare med rollen säkerhets administratör eller prenumerations ägare.

    - För aviseringar öppnar du **pris & inställningar**, väljer den relevanta prenumerationen och väljer **e-postaviseringar**.

    - Om du implementerar en rekommendation väljer du **Ange säkerhets kontakt information**under **rekommendationer**, väljer den Azure-prenumeration som du vill ge kontakt information på. Detta öppnar **e-postaviseringar**.

   ![Ange säkerhetskontaktinformation][2]

1. Ange e-postadressen eller adresserna för säkerhets kontakten avgränsade med kommatecken. Det finns ingen gräns för antalet e-postadresser som du kan ange.

1. Om du vill få e-postmeddelanden om aviseringar med hög allvarlighets grad aktiverar du alternativet **skicka mig e-postmeddelanden om aviseringar**. För andra allvarlighets nivåer använder du en Logic-app enligt beskrivningen i [arbets flödes automatisering](workflow-automation.md).

1. Du kan skicka e-postmeddelanden till prenumerations ägare (klassisk tjänst administratör och medadministratörer, plus rollen RBAC-ägare i prenumerations omfånget).

1. Om du vill använda säkerhets kontakt information för din prenumeration väljer du **Spara**.

## <a name="see-also"></a>Se även
I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – lär dig hur du hanterar och åtgärdar säkerhets aviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
