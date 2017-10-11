---
title: "Azure Active Directory Reporting vanliga frågor och svar | Microsoft Docs"
description: "Azure Active Directory reporting vanliga frågor och svar."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: accf292f70bf0eafdefc00c3feeaf8e346605401
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="azure-active-directory-reporting-faq"></a>Azure Active Directory reporting vanliga frågor och svar

Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om Azure Active Directory-rapportering.  
Mer information finns i [Azure Active Directory reporting](active-directory-reporting-azure-portal.md). 

**F: Vad är datalagring för aktivitetsloggar (granskning och inloggningar) i Azure portal?** 

**S:** vi erbjuder 7 dagars data för kunden ledigt och genom att växla till en Azure AD Premium 1 eller 2 för Premium-licens, du kan komma åt data i upp till 30 dagar. Mer information om kvarhållning finns [rapportkvarhållningsregler i Azure Active Directory](active-directory-reporting-retention.md)

--- 

**F: hur lång tid tar det förrän aktivitetsdata visas när jag har slutfört min uppgiften?**

**S:** granskningsloggarna för aktivitet har en fördröjning mellan 15 minuter till en timme. Inloggningsaktivitet loggar har en fördröjning mellan 15 minuter för de flesta poster och upp till två timmar för några poster.

---

**F: måste vara en global administratör för att se aktiviteten loggar i Azure-portalen eller hämta data via API?**

**S:** Nej. Du kan antingen vara en **säkerhet Reader**, **säkerhet Admin** eller en **Global administratör** att se data i Azure-portalen eller genom att öppna den via API-rapportering.

---

**F: kan jag få logginformation för Office 365 aktiviteten via Azure portal?**

**S:** även om aktiviteten för Office 365 och Azure AD aktivitet loggar resursen mycket directory-resurser om du vill att en fullständig överblick över aktivitetsloggar Office 365 bör du gå till Office 365 Admin Center få logginformation för Office 365-aktivitet.

---


**F: vilken API: er används för att få information om Office 365 aktivitetsloggar?**

**S:** använda Office 365 Management-API: er för att komma åt den [Office 365 aktivitet loggar via ett API](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**F: hur många poster som jag kan hämta från Azure-portalen?**

**S:** upp till 120 K poster kan hämtas från Azure-portalen. Poster sorteras efter *senaste* och som standard får de senaste 120 K posterna. 

---

**F: hur många poster kan fråga via aktiviteter API?**

**S:** du kan fråga efter upp till 1 miljon poster (om du inte använder operatorn top som sorterar posten efter de senaste). Om du använder operatorn ”top”, kan du fråga upp till 500 kB poster. Du kan hitta Exempelfrågor för hur du använder API: et här [här](active-directory-reporting-api-getting-started.md).

---

**F: hur får jag en premium-licens?**

**S:** finns [komma igång med Azure Active Directory Premium](active-directory-get-started-premium.md) för ett svar på frågan.

---

**F: hur snart bör se aktiviteter data när en premium-licens?**

**S:** om du redan har data som aktiviteter som en kostnadsfri licens, så du kan se samma data. Om du inte har några data, tar en eller två dagar.

---

**F: kan jag se senaste månaden data när en Azure AD premium-licens?**

**S:** om du har nyligen bytt till en Premium-version (inklusive en utvärderingsversion), kan du se data upp till 7 dagar från början. När data ackumuleras visas upp till 30 dagar.

---

**F: det finns en risk händelse i Identity Protection men jag ser inte motsvarande inloggning i alla inloggningar. Förväntas detta?**

**S:** Ja, identitetsskydd utvärderar risk för alla flöden för autentisering om om vara interaktiva eller icke-interaktivt. Alla inloggningar endast rapporten innehåller dock bara de interaktiva inloggningarna.

---

**F: hur kan jag hämta rapporten ”användare som flaggats för risk” i Azure-portalen?**

**S:** alternativet för att hämta *användare som har flaggats för risk* rapporten läggs snart.

---

**F: hur vet jag varför en inloggning eller en användare som har flaggats riskfyllda i Azure portal?**

**S:** Premium edition kunder kan läsa mer om de underliggande riskhändelser genom att klicka på användare i ”användare som flaggats för risk” eller genom att klicka på de ”riskfyllda inloggningarna”. Ledigt och grundläggande edition kunder få se vilka användare och inloggningar utan underliggande händelseinformation för risk.

---

**F: hur beräknas IP-adresser i inloggningar och riskfyllda inloggningar rapporten?**

**S:** IP-adresser utfärdas så att det finns ingen slutgiltiga anslutning mellan en IP-adress och där datorn med adressen finns fysiskt. Detta är komplicerade av faktorer, till exempel mobila providers och VPN-anslutningar utfärda IP-adresser från central pooler ofta mycket långt där klientenheten verkligen används. Ovanstående är, konverterar IP-adress till en fysisk plats en bästa prestanda baserat på spårningar, registerdata, omvänd sökningar och annan information. 

---
