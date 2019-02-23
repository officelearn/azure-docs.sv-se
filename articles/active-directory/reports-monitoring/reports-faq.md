---
title: Vanliga frågor och svar för Azure Active Directory-rapporter | Microsoft Docs
description: Vanliga quesitons kring Azure Active Directory-rapporter.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: c51f1d47a5412e77b7113fccfd2e9a54e1d2ff7f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730213"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Vanliga frågor om Azure Active Directory-rapporter

Den här artikeln innehåller svar på vanliga frågor och svar om Azure Active Directory (Azure AD) reporting. Läs mer i informationen om [Azure Active Directory-rapportering](overview-reports.md). 

## <a name="getting-started"></a>Komma igång 

**F: Jag använder den https://graph.windows.net/&lt; klientnamn&gt;/reports/ endpoint API: er till pull Azure AD-gransknings- och integrerad programanvändning rapporterar i vår rapporteringssystem programmässigt. Vad ska jag växla till?**

**S:** Leta upp den [API-referens](https://developer.microsoft.com/graph/) att se hur du kan [använda API: erna för att komma åt aktivitetsrapporter](concept-reporting-api.md). Den här slutpunkten har två rapporter (**Audit** och **inloggningar**) som ger alla data som du fick i den gamla API-slutpunkten. Den här nya slutpunkten har också en inloggningar rapport med Azure AD Premium-licens som du kan använda för att hämta appanvändning, användning av enhet och logga in användarinformation.

---

**F: Jag använder den https://graph.windows.net/&lt; klientnamn&gt;/reports/ endpoint API: er för att hämta Azure AD-säkerhetsrapporter (vissa typer av identifieringar, till exempel läckta autentiseringsuppgifter eller inloggningar från anonyma IP-adresser) till våra reporting system programmässigt. Vad ska jag växla till?**

**S:** Du kan använda den [Identity Protection riskhändelser API](../identity-protection/graph-get-started.md) till säkerhetsidentifieringar för åtkomst via Microsoft Graph. Detta nya format ger större flexibilitet i hur du kan fråga efter data med avancerade filter, valda fält och mycket mer och standardiserar riskhändelser till en typ för enklare integrering till siem-servrar och andra verktyg för insamling av data. Eftersom data är i ett annat format, kan du ersätta en ny fråga för dina gamla frågor. Dock [nya API: et använder Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), vilket är Microsoft-standarden för dessa API: er som O365 eller Azure AD. Så att arbetet som krävs kan antingen utöka dina aktuella investeringar i MS Graph eller hjälp kan du börja dig vid övergången till den här nya standard-plattformen.

---

**F: Hur får jag en premiumlicens?**

**S:** Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version.

---

**F: Hur snart Se bör granska data när du har fått en premiumlicens?**

**S:** Om du redan har data för aktiviteter som en kostnadsfri licens och du kan se dem direkt. Om du inte har några data, kan det ta en eller två dagar innan data visas i rapporterna.

---

**F: Kan jag se förra månadens data när du har skaffat en Azure AD premium-licens?**

**S:** Om du har nyligen har bytt till en Premium-version (inklusive en utvärderingsversion) kan du se data upp till 7 dagar från början. När en publiceringskonfiguration ackumuleras data, kan du se data för de senaste 30 dagarna.

---

**F: Måste jag vara en global administratör för att se aktiviteten inloggningar till Azure-portalen eller hämta data via API: et?**

**S:** Nej, du kan också öppna rapporteringsdata via portalen eller API: et om du är en **Säkerhetsläsare** eller **säkerhetsadministratör** för klienten. Naturligtvis **globala administratörer** får åtkomst till dessa data.

---


## <a name="activity-logs"></a>Aktivitetsloggar


**F: Vad är datakvarhållning aktivitetsloggar (gransknings- och inloggningar) i Azure-portalen?** 

**S:** I följande tabell visas Datalagringsperiod aktivitetsloggar. Mer information finns i [datalagringsprinciper för Azure AD-rapporter](reference-reports-data-retention.md).

| Rapport                 | Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Granskningsloggar             | 7 dagar        | 30 dagar             | 30 dagar             |
| Inloggningar               | Gäller inte           | 30 dagar             | 30 dagar             |
| Azure MFA-användning        | 30 dagar       | 30 dagar             | 30 dagar             |

---

**F: Hur lång tid tar det innan jag kan se alla aktivitetsdata när jag har slutfört min uppgift?**

**S:** Granskningsloggar har en svarstid som sträcker sig från 15 minuter till en timme. Logga in aktivitetsloggar kan ta mellan 15 minuter till upp till 2 timmar för vissa poster.

---

**F: Kan jag få information från aktivitetsloggen i Office 365 via Azure portal?**

**S:** Om du vill ha en fullständig överblick över aktivitetsloggarna i Office 365 bör du gå till administrationscentret för Office 365 för att hämta information om dem, även om aktivitetsloggarna i Office 365 och aktivitetsloggarna i Azure AD delar många katalogresurser.

---

**F: Vilka API: er ska jag använda för att få information om Office 365-aktivitetsloggar?**

**S:** Använd den [API: er för Office 365-Management](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) åtkomst till Office 365-aktivitetsloggar via ett API.

---

**F: Hur många poster som jag kan hämta från Azure-portalen?**

**S:** Du kan ladda ned upp till 5 000 poster från Azure-portalen. Posterna sorteras efter *senaste* och som standard, du får de senaste 5 000 poster.

---

## <a name="risky-sign-ins"></a>Riskfyllda inloggningar

**F: Det finns en riskhändelse i Identity Protection men jag ser inte motsvarande inloggning i rapporten inloggningar. Förväntas detta?**

**S:** Ja, Identity Protection utvärderar risken för alla autentiseringsflöden om interaktivt eller icke-interaktivt. Alla inloggningar endast rapporten innehåller dock endast de interaktiva inloggningarna.

---

**F: Hur får jag reda på varför en inloggning eller en användare har flaggats riskfyllda i Azure-portalen?**

**S:** Om du har en **Azure AD Premium** prenumeration, kan du läsa mer om de underliggande riskhändelser genom att välja användaren i **användare som har flaggats för risk** eller genom att välja en post i den **riskfylld inloggningar** rapporten. Om du har en **kostnadsfri** eller **grundläggande** prenumeration och du kan visa användare i risk och riskfyllda inloggningar rapporter, men du kan inte se den underliggande informationen om identitetsriskhändelser.

---

**F: Hur beräknas IP-adresser i inloggningar och rapporten över riskfyllda inloggningar?**

**S:** IP-adresser utfärdas så att det finns ingen slutgiltiga koppling mellan en IP-adress och var datorn med den här adressen är fysiskt. Mappning av IP-adresser är mer komplicerat av faktorer, till exempel mobila leverantörer och VPN-anslutningar utfärda IP-adresser från central pooler ofta mycket är långt från där klientenheten faktiskt används. För närvarande i Azure AD-rapporter är konverterar IP-adress till en fysisk plats en bästa prestanda baserat på spårningar, registerdata, omvänd sökningar och annan information. 

---

**F: Vad kostar riskhändelsen ”logga in med identifierad ytterligare risk” en obestämd?**

**S:** Att ge dig insyn i alla riskfyllda inloggningar i din miljö, ”logga in med identifierad ytterligare risk” funktioner som platshållare för inloggningar för identifieringar som är exklusivt för Azure AD Identity Protection-prenumeranter.

---

## <a name="conditional-access"></a>Villkorlig åtkomst

**F: Vad är nytt med den här funktionen?**

**S:** Kunder kan nu felsöka principer för villkorlig åtkomst via rapporten för alla inloggningar. Kunder kan granska statusen för villkorlig åtkomst och genomgång av information om de principer som tillämpas på inloggningen och resultatet för varje princip.

**F: Hur kommer jag igång?**

**S:** Så här kommer du igång:
    * Gå till rapporten inloggningar i den [Azure-portalen](https://portal.azure.com). 
    * Klicka på inloggning som du vill felsöka.
    * Navigera till den **villkorlig åtkomst** fliken. Här kan visa du alla principer som påverkas inloggningen och resultatet för varje princip. 
    
**F: Vilka är de möjliga värdena för statusen för villkorlig åtkomst?**

**S:** Statusen för villkorlig åtkomst kan ha följande värden:
    * **Tillämpas inte**: Det innebär att det fanns ingen CA-princip med användaren och appen i omfånget. 
    * **Lyckade**: Det innebär att det var en CA-princip med användaren och appen i omfånget och CA-principer har uppfylls. 
    * **Fel**: Det innebär att det var en CA-princip med användaren och appen i omfånget och principer för CA: N inte har uppfyllts. 
    
**F: Vilka är de möjliga värdena för villkorlig åtkomst princip resultatet?**

**S:** Principer för villkorlig åtkomst kan ha följande resultat:
    * **Lyckade**: Principen uppfylldes har.
    * **Fel**: Principen uppfylldes inte.
    * **Tillämpas inte**: Det kan bero på Principvillkor inte uppfyllde.
    * **Inte aktiverad**: Detta beror på principen i inaktiverat tillstånd. 
    
**F: På principnamnet i rapporten för alla logga in matchar inte namnet på principen i CA: N. Varför?**

**S:** Principnamnet i alla loggar in rapporten baseras på CA-principnamn vid tidpunkten för inloggningen. Detta kan inte överensstämmer med principens namn i CA: N om du har uppdaterat principnamnet senare, det vill säga efter inloggningen.

**F: Min inloggning har blockerats på grund av en princip för villkorlig åtkomst, men inloggningsaktivitet rapporten visar att inloggningen lyckades. Varför?**

**S:** Logga in rapporten kan för närvarande inte visa korrekta resultat för Exchange ActiveSync-scenarier när villkorlig åtkomst tillämpas. Det kan finnas fall när du loggar in resultatet i rapporten visar en lyckad inloggning, men inloggningen faktiskt misslyckades på grund av en princip för villkorlig åtkomst. 
