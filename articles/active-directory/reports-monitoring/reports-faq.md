---
title: Azure Active Directory-rapportering vanliga frågor och svar | Microsoft Docs
description: Azure Active Directory-rapportering vanliga frågor och svar.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f1683321e23eff82e73dc9bb44941fc390633b8c
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42055911"
---
# <a name="azure-active-directory-reporting-faq"></a>Azure Active Directory-rapportering vanliga frågor och svar

Den här artikeln innehåller svar på vanliga frågor och svar om Azure Active Directory (Azure AD) reporting. Läs mer i informationen om [Azure Active Directory-rapportering](overview-reports.md). 

## <a name="getting-started"></a>Komma igång 

**F: Jag använder den https://graph.windows.net/&lt; klientnamn&gt;/reports/ endpoint API: er till pull Azure AD-gransknings- och integrerad programanvändning rapporterar i vår rapporteringssystem programmässigt. Vad ska jag växla till?**

**S:** Leta upp den [API-referensdokumentation](https://developer.microsoft.com/graph/) att se hur du kan använda den nya API: er för att komma åt [aktivitetsrapporter](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal). Den här slutpunkten har två rapporter (gransknings- och inloggningar) som tillhandahåller de data som du fick i den gamla API-slutpunkten. Den här nya slutpunkten har också en inloggningar rapport med Azure AD Premium-licens som du kan använda för att hämta appanvändning, användning av enhet och logga in användarinformation.

--- 

**F: Jag använder den https://graph.windows.net/&lt; klientnamn&gt;/reports/ endpoint API: er för att hämta Azure AD-säkerhetsrapporter (vissa typer av identifieringar, till exempel läckta autentiseringsuppgifter eller inloggningar från anonyma IP-adresser) till våra reporting system programmässigt. Vad ska jag växla till?**

**S:** du kan använda den [Identity Protection riskhändelser API](../identity-protection/graph-get-started.md) till säkerhetsidentifieringar för åtkomst via Microsoft Graph. Detta nya format ger större flexibilitet i hur du kan fråga efter data med avancerade filter, valda fält och mycket mer och standardiserar riskhändelser till en typ för enklare integrering till siem-servrar och andra verktyg för insamling av data. Eftersom data är i ett annat format, kan du ersätta en ny fråga för dina gamla frågor. Dock [nya API: et använder Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), vilket är Microsoft-standarden för dessa API: er som O365 eller Azure AD. Så att arbetet som krävs kan antingen utöka dina aktuella investeringar i MS Graph eller hjälp kan du börja dig vid övergången till den här nya standard-plattformen.

--- 

**F: hur får jag en premiumlicens?**

**S:** Se [komma igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) efter ett svar på den här frågan.

---

**F: hur snart Se bör granska data när du har fått en premiumlicens?**

**S:** om du redan har data för aktiviteter som en kostnadsfri licens så du kan se samma data. Om du inte har några data, kan det ta en eller två dagar.

---

**F: kan jag se förra månadens data när du har skaffat en Azure AD premium-licens?**

**S:** om du har nyligen har bytt till en Premium-version (inklusive en utvärderingsversion), kan du se data upp till 7 dagar från början. När en publiceringskonfiguration ackumuleras data, visas upp till 30 dagar.

---

**F: måste jag vara global administratör för att se aktiviteten inloggningar till Azure-portalen eller hämta data via API: et?**

**S:** Nej. Du måste vara en **Säkerhetsläsare**, ett **säkerhetsadministratör**, eller en **Global administratör** att hämta rapportdata i Azure portal eller via API: et.

---


## <a name="activity-logs"></a>Aktivitetsloggar


**F: Vad är datakvarhållning aktivitetsloggar (gransknings- och inloggningar) i Azure-portalen?** 

**S:** Se [för hur länge är insamlade data lagras?](reference-reports-data-retention.md#q-for-how-long-is-the-collected-data-stored) efter ett svar på den här frågan.

--- 

**F: hur lång tid tar det innan jag kan se alla aktivitetsdata när jag har slutfört min uppgift?**

**S:** granskningsloggar för aktivitet har en svarstid som sträcker sig från 15 minuter till en timme. Logga in aktivitetsloggar kan ta mellan 15 minuter till upp till 2 timmar för vissa poster.

---


**F: kan jag få information från aktivitetsloggen i Office 365 via Azure portal?**

**S:** även om aktiviteten i Office 365 och Azure AD delar en massa directory-resurser, om du vill att en fullständig överblick över aktivitetsloggarna Office 365 bör du gå till Office 365 Admin Center för att hämta information från aktivitetsloggen i Office 365.

---


**F: vilka API: er ska jag använda för att få information om Office 365-aktivitetsloggar?**

**S:** använda Office 365 Management-API: er för att komma åt den [Office 365 aktivitetsloggar via ett API](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**F: hur många poster som jag kan hämta från Azure-portalen?**

**S:** du kan hämta upp till 5 000 poster från Azure-portalen. Posterna sorteras efter *senaste* och som standard, du får de senaste 5 000 poster.

---

## <a name="risky-sign-ins"></a>Riskfyllda inloggningar

**F: det finns en riskhändelse i Identity Protection men jag ser inte motsvarande inloggning i alla inloggningar. Förväntas detta?**

**S:** Ja, Identity Protection utvärderar risken för alla autentiseringsflöden om interaktivt eller icke-interaktivt. Alla inloggningar endast rapporten innehåller dock endast de interaktiva inloggningarna.

---

**F: hur kan jag hämta rapporten ”användare som har flaggats för risk” i Azure-portalen?**

**S:** alternativ för att hämta *användare som har flaggats för risk* rapporten kommer snart.

---

**F: hur kan jag se varför en inloggning eller en användare har flaggats riskfyllda i Azure-portalen?**

**S:** Premium edition-kunder kan läsa mer om de underliggande riskhändelser genom att klicka på användaren i ”användare som har flaggats för risk” eller genom att klicka på ”riskfyllda inloggningar”. Kostnadsfri och Basic edition-kunder komma att se vilka användare och inloggningar utan att den underliggande informationen om identitetsriskhändelser.

---

**F: hur beräknas IP-adresser i inloggningar och rapporten över riskfyllda inloggningar?**

**S:** IP-adresser utfärdas så att det finns ingen slutgiltiga koppling mellan en IP-adress och var datorn med den här adressen är fysiskt. Det är komplicerat av faktorer, till exempel mobila leverantörer och VPN-anslutningar utfärda IP-adresser från central pooler ofta mycket är långt från där klientenheten faktiskt används. Med ovanstående kan är konvertera IP-adress till en fysisk plats bästa förmåga baserat på spårningar, registerdata, omvänd sökningar och annan information. 

---

**F: Vad gör riskhändelsen ”logga in med identifierad ytterligare risk” en obestämd?**

**S:** för att ge dig en överblick över alla riskfyllda inloggningar i din miljö, ”logga in med ytterligare risk identifierats” fungerar som platshållare för inloggningar för identifieringar som är exklusivt för Azure AD Identity Protection-prenumeranter.

---

**F: Vad gör riskhändelsen ”logga in med identifierad ytterligare risk” en obestämd?**

**S:** för att ge dig en överblick över alla riskfyllda inloggningar i din miljö, ”logga in med ytterligare risk identifierats” fungerar som platshållare för inloggningar för identifieringar som är exklusivt för Azure AD Identity Protection-prenumeranter.

---

## <a name="conditional-access"></a>Villkorlig åtkomst

**F: Vad är nytt med den här funktionen?**

**S:** kunder kan nu felsöka principer för villkorlig åtkomst via rapporten för alla inloggningar. Kunder kan granska statusen för villkorlig åtkomst och genomgång av information om de principer som tillämpas på inloggningen och resultatet för varje princip.

**F: hur kommer jag igång?**

**S:** att komma igång:
    * Gå till rapporten inloggningar i den [Azure-portalen](https://portal.azure.com). 
    * Klicka på inloggning som du vill felsöka.
    * Navigera till den **villkorlig åtkomst** fliken. Här kan visa du alla principer som påverkas inloggningen och resultatet för varje princip. 
    
**F: Vad är alla möjliga värden för statusen för villkorlig åtkomst?**

**S:** statusen för villkorlig åtkomst kan ha följande värden:
    * **Tillämpas inte**: Det innebär att det fanns ingen CA-princip med användaren och appen i omfånget. 
    * **Lyckade**: Det innebär att det uppstod en CA-princip med användaren och appen i omfånget och CA-principer har uppfylls. 
    * **Fel**: Det innebär att det uppstod en CA-princip med användaren och appen i omfånget och principer för CA: N inte har uppfyllts. 
    
**F: Vad är alla möjliga värden för villkorlig åtkomst princip resultatet?**

**S:** principer för villkorlig åtkomst kan ha följande resultat:
    * **Lyckade**: principen har uppfyllts.
    * **Fel**: principen uppfylldes inte.
    * **Tillämpas inte**: Det kan bero på Principvillkor inte uppfyllde.
    * **Inte aktiverad**: Detta är p.g.a. principen i inaktiverat tillstånd. 
    
**F: på principnamnet i rapporten för alla inloggning matchar inte namnet på principen i CA: N. Varför?**

**S:** på principnamnet i alla loggar in rapporten baseras på namnet på CA-principen vid tidpunkten för inloggningen. Detta kan inte överensstämmer med principens namn i CA: N om du har uppdaterat principnamnet senare, det vill säga efter inloggningen.