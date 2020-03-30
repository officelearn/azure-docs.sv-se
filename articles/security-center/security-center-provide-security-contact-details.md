---
title: Ange information om säkerhetskontakter i Azure Security Center | Microsoft-dokument
description: Det här dokumentet visar hur du anger säkerhetskontaktinformation i Azure Security Center.
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
ms.openlocfilehash: 64a9600a3014f7e85a7f924d38882bfadaf631db
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387826"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Ange information om säkerhetskontakter i Azure Security Center
Azure Security Center rekommenderar att du uppger kontaktuppgifter för säkerhetsrelaterade frågor relaterade till din Azure-prenumeration om du inte redan har gjort det. Den här informationen används av Microsoft för att kontakta dig om Microsoft Security Response Center (MRSC) upptäcker att en obehörig part har kommit åt dina kunddata. MSRC utför utvald säkerhetsövervakning av Azure-nätverket och infrastrukturen och tar emot hotinformation samt klagomål om missbruk från tredje part.

Ett e-postmeddelande skickas på den första dagliga förekomsten av en avisering och endast för varningar med hög angelägenhetsgrad. E-postinställningar kan bara konfigureras för prenumerationsprinciper. Resursgrupper inom en prenumeration ärver inställningarna. Aviseringar är endast tillgängliga på standardnivån i Azure Security Center.

E-postmeddelanden med avisering skickas:
- Till en enda e-postmottagare per typ av avisering per dag  
- Högst tre e-postmeddelanden skickas till en enskild mottagare under en enda dag
- Varje e-postmeddelande innehåller en enda avisering, inte en sammanställning av aviseringar
- Endast för aviseringar med hög allvarlighetsgrad

> [!TIP]
> För aviseringar med andra allvarlighetsgrader skapar du en [automatisering av arbetsflödet](workflow-automation.md) för att använda en logikapp som skickar e-postmeddelanden till relevant personal.
 
Till exempel om ett e-postmeddelande redan har skickats för att meddela dig om en RDP-attack, får du inte ett annat e-postmeddelande om en RDP-attack under samma dag, även om en ny avisering utlöses. 

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är inte en steg-för-steg-guide.

## <a name="set-up-email-notifications-for-alerts"></a>Konfigurera e-postmeddelanden för aviseringar<a name="email"></a>

1. Öppna sidan **E-postmeddelanden:**

    - För aviseringar öppnar du **Inställningar för prissättning &**, väljer relevant prenumeration och väljer **E-postmeddelanden**.

    - Om du implementerar en rekommendation **väljer**du Azure-prenumerationen för att tillhandahålla kontaktinformation för kontaktuppgifter under **rekommendationer.** Detta öppnar **e-postmeddelanden**.

   ![Ange säkerhetskontaktinformation][2]

1. Ange e-postadressen eller adresserna för säkerhetskontakt som avgränsas med kommatecken. Det finns ingen gräns för hur många e-postadresser du kan ange.

1. Om du vill ta emot e-postmeddelanden om varningar med hög allvarlighetsgrad aktiverar du alternativet **Skicka e-postmeddelanden till mig om aviseringar**. För andra allvarlighetsgrader använder du en Logic App som förklaras i [automatisering av arbetsflöden](workflow-automation.md).

1. Du kan skicka e-postmeddelanden till prenumerationsägare (klassisk tjänstadministratör och medadministratörer samt rollen RBAC-ägare i prenumerationsomfattningen).

1. Om du vill använda säkerhetskontaktinformationen på din prenumeration väljer du **Spara**.

## <a name="see-also"></a>Se även
I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* Övervakning av [säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar hälsotillståndet för dina Azure-resurser.
* [Hantera och svara på säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Läs om hur du hanterar och svarar på säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
