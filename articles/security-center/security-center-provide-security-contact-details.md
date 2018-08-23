---
title: Ange säkerhetskontaktuppgifter i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur du kan lägga till kontaktuppgifter i Azure Security Center.
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
ms.date: 08/20/2018
ms.author: terrylan
ms.openlocfilehash: c268538cf401b29f68210c6d017c2eac55e88939
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42055910"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Ange säkerhetskontaktuppgifter i Azure Security Center
Azure Security Center rekommenderar att du anger kontaktuppgifter för din Azure-prenumeration om du inte redan har gjort. Den här informationen används av Microsoft för att kontakta dig om Microsoft Security Response Center (MRSC) upptäcker att en obehörig part har kommit åt dina kunddata. MSRC utför väljer säkerhetsövervakning för Azure-nätverk och infrastruktur och tar emot threat intelligence och missbruk klagomål från tredje part.

Ett e-postmeddelande skickas på den första dagliga förekomsten av en avisering och endast för varningar med hög angelägenhetsgrad. E-postinställningar kan bara konfigureras för prenumerationsprinciper. Resursgrupper inom en prenumeration ärver inställningarna.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. Under **rekommendationer**väljer **ange säkerhetskontaktinformation**.
   ![Ange säkerhetskontakt][1]
2. Välj den Azure-prenumeration anger du kontaktinformation på.
3. Då öppnas **e-postmeddelanden**.

   ![Ange säkerhetskontaktinformation][2]

   * Ange security e-postadress eller adresser separerade med kommatecken. Det finns inte en gräns för hur många e-postadresser som du kan ange.
   * Ange en säkerhet internationella telefonnummer för kontakt.
   * Aktivera alternativet för att ta emot e-post om varningar med hög angelägenhetsgrad, **Skicka mig e-postmeddelanden om aviseringar**.
   * I framtiden kommer du har möjlighet att skicka e-postmeddelanden till prenumerationsägarna. Det här alternativet är för närvarande nedtonat.
   * Välj **spara** att tillämpa security kontaktuppgifter till din prenumeration.

## <a name="see-also"></a>Se också
I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – senaste nytt i Azure-säkerhet och information.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
