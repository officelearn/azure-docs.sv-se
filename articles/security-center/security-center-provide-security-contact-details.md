---
title: Ange säkerhet kontaktinformation i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskrivs hur du ange säkerhet kontaktinformation i Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: terrylan
ms.openlocfilehash: 726b59c45e2eb18eebe28a180db23336ae141408
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866529"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Ange säkerhet kontaktinformation i Azure Security Center
Azure Security Center kommer rekommenderar att du förser säkerhet kontaktinformation för din Azure-prenumeration om du inte redan har gjort. Den här informationen används av Microsoft för att kontakta dig om Microsoft Security Response Center (MRSC) upptäcker att en obehörig part har kommit åt dina kunddata. MSRC utför väljer säkerhetsövervakning av Azure-nätverk och infrastruktur och tar emot hot intelligence och missbruk klagomål från tredje part.

Ett e-postmeddelande skickas på den första dagliga förekomsten av en avisering och endast för varningar med hög angelägenhetsgrad. E-postinställningar kan bara konfigureras för prenumerationsprinciper. Resursgrupper inom en prenumeration ärver inställningarna.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. Under **rekommendationer**väljer **ange säkerhet kontaktinformation**.
   ![Ger säkerhet kontakt][1]
2. Välj den Azure-prenumerationer anger du kontaktinformation på.
3. Då öppnas **säkerhetsprincip - e-postmeddelanden**.

   ![Ange säkerhetskontaktinformation][2]

   * Ange säkerhet kontakta e-postadressen eller adresser som avgränsas med kommatecken. Det finns inte en gräns för antalet e-postadresser som du kan ange.
   * Ange en säkerhet kontakta internationella telefonnummer.
   * Aktivera alternativet för att ta emot e-post om varningar med hög angelägenhetsgrad **Skicka mig e-postmeddelanden om aviseringar**.
   * I framtiden, har du möjlighet att skicka e-postmeddelanden till prenumerationen ägare. Det här alternativet är för närvarande nedtonade.
   * Välj **spara** ska gälla säkerhet kontaktinformation för din prenumeration.

## <a name="see-also"></a>Se även
I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) --Azure security nyheter och information.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
