---
title: Vanliga frågor och svar om Azure Active Directory-rapporter | Microsoft-dokument
description: Vanliga frågor och svar om Azure Active Directory-rapporter.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273fdb80475defb0576bcd29d1944c5f6c595cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266512"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Vanliga frågor och svar kring Azure Active Directory-rapporter

Den här artikeln innehåller svar på vanliga frågor om Azure Active Directory (Azure AD) rapportering. Läs mer i informationen om [Azure Active Directory-rapportering](overview-reports.md). 

## <a name="getting-started"></a>Komma igång 

**F: Jag använder `https://graph.windows.net/<tenant-name>/reports/` för närvarande slutpunkts-API:erna för att hämta Azure AD-granskning och integrerade programanvändningsrapporter i våra rapporteringssystem programmässigt. Vad ska jag byta till?**

**A.** Slå upp [API-referensen](https://developer.microsoft.com/graph/) för att se hur du kan [använda API:erna för att komma åt aktivitetsrapporter](concept-reporting-api.md). Den här slutpunkten har två rapporter (**Granskning** och inloggningar ) som innehåller alla data som du har i den gamla **API-slutpunkten.** Den här nya slutpunkten har också en inloggningsrapport med Azure AD Premium-licensen som du kan använda för att få appanvändning, enhetsanvändning och inloggningsinformation för användare.

---

**F: Jag använder `https://graph.windows.net/<tenant-name>/reports/` för närvarande slutpunkts-API:erna för att hämta Azure AD-säkerhetsrapporter (specifika typer av identifieringar, till exempel läckta autentiseringsuppgifter eller inloggningar från anonyma IP-adresser) till våra rapporteringssystem programmässigt. Vad ska jag byta till?**

**A.** Du kan använda  [API:et för identitetsskyddsriskidentifiering för](../identity-protection/graph-get-started.md)att komma åt säkerhetsidentifieringar via Microsoft Graph. Det här nya formatet ger större flexibilitet i hur du kan fråga data, med avancerad filtrering, fältval med mera, och standardiserar riskidentifieringar i en typ för enklare integrering i SIEMs och andra datainsamlingsverktyg. Eftersom data är i ett annat format kan du inte ersätta en ny fråga för dina gamla frågor. Det [nya API:et använder](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent)dock Microsoft Graph , som är Microsoft-standarden för sådana API:er som O365 eller Azure AD. Så det arbete som krävs kan antingen utöka dina nuvarande Microsoft Graph-investeringar eller hjälpa dig att påbörja övergången till den här nya standardplattformen.

---

**F: Hur får jag en premiumlicens?**

**A.** Se [Komma igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-utgåva.

---

**F: Hur snart ska jag se aktivitetsdata efter att ha fått en premiumlicens?**

**A.** Om du redan har aktiviteter data som en fri licens, då kan du se det omedelbart. Om du inte har några data tar det en eller två dagar innan data visas i rapporterna.

---

**F: Kan jag se förra månadens data efter att ha fått en Azure AD-premiumlicens?**

**A.** Om du nyligen har bytt till en Premium-version (inklusive en testversion) kan du se data upp till 7 dagar från början. När data ackumuleras kan du se data för de senaste 30 dagarna.

---

**F: Måste jag vara global administratör för att se aktivitetsin logga in till Azure-portalen eller för att få data via API?**

**A.** Nej, du kan också komma åt rapportdata via portalen eller via API:et om du är **säkerhetsläsare** eller **säkerhetsadministratör** för klienten. Naturligtvis kommer **globala administratörer** också att ha tillgång till dessa data.

---


## <a name="activity-logs"></a>Aktivitetsloggar


**F: Vad är datalagring för aktivitetsloggar (granskning och inloggningar) i Azure-portalen?** 

**A.** I följande tabell visas datalagringsperioden för aktivitetsloggar. Mer information finns i [principer för datalagring för Azure AD-rapporter](reference-reports-data-retention.md).

| Rapport                 | Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Granskningsloggar             | 7 dagar        | 30 dagar             | 30 dagar             |
| Inloggningar               | Ej tillämpligt           | 30 dagar             | 30 dagar             |
| Azure MFA-användning        | 30 dagar       | 30 dagar             | 30 dagar             |

---

**F: Hur lång tid tar det innan jag kan se aktivitetsdata när jag har slutfört min uppgift?**

**A.** Granskningsloggar har en svarstid som sträcker sig från 15 minuter till en timme. Inloggningsaktivitetsloggar kan ta från 15 minuter till upp till 2 timmar för vissa poster.

---

**F: Kan jag få information om Office 365-aktivitetslogg via Azure-portalen?**

**A.** Även om Office 365-aktivitets- och Azure AD-aktivitetsloggar delar en hel del katalogresurser bör du, om du vill ha en fullständig vy över aktivitetsloggarna för Office 365, gå till [Microsoft 365-administrationscentret](https://admin.microsoft.com) för att få information om Aktivitetsloggen för Office 365.

---

**F: Vilka API:er använder jag för att få information om Office 365-aktivitetsloggar?**

**A.** Använd [API:erna för Office 365-hantering](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) för att komma åt Office 365-aktivitetsloggarna via ett API.

---

**F: Hur många poster kan jag hämta från Azure Portal?**

**A.** Du kan hämta upp till 5 000 poster från Azure-portalen. Posterna sorteras efter *de senaste* och som standard får du de senaste 5000 posterna.

---

## <a name="risky-sign-ins"></a>Riskfyllda inloggningar

**F: Det finns en riskidentifiering i Identity Protection men jag ser inte motsvarande inloggning i inloggningsrapporten. Är detta väntat?**

**A.** Ja, Identity Protection utvärderar risken för alla autentiseringsflöden oavsett om de är interaktiva eller icke-interaktiva. Alla inloggningar visar dock bara interaktiva inloggningar.

---

**F: Hur vet jag varför en inloggning eller en användare har flaggats riskabelt i Azure-portalen?**

**A.** Om du har en **Azure AD Premium-prenumeration** kan du läsa mer om de underliggande riskidentifieringarna genom att välja användaren i **Användare som flaggats för risk** eller genom att välja en post i rapporten **Risky-inloggningar.** Om du har en **kostnadsfri** prenumeration eller **en Grundläggande** prenumeration kan du visa användarna i riskzonen och riskfyllda inloggningsrapporter, men du kan inte se den underliggande riskidentifieringsinformationen.

---

**F: Hur beräknas IP-adresser i inloggningsrapporten och den riskfyllda inloggningsrapporten?**

**A.** IP-adresser utfärdas på ett sådant sätt att det inte finns något definitivt samband mellan en IP-adress och där datorn med den adressen är fysiskt belägen. Kartläggning IP-adresser kompliceras ytterligare av faktorer som mobiloperatörer och VPN som utfärdar IP-adresser från centrala pooler ofta mycket långt från där klientenheten faktiskt används. För närvarande i Azure AD-rapporter, konvertera IP-adress till en fysisk plats är en bästa insats baserat på spår, registerdata, omvänd slå upp-enheter och annan information. 

---

**F: Vad betyder riskidentifieringen "Inloggning med ytterligare risk upptäckt"?**

**A.** För att ge dig insikt i alla riskfyllda inloggningar i din miljö fungerar "Inloggning med ytterligare risker upptäckt" som platshållare för inloggningar för identifieringar som är exklusiva för Azure AD Identity Protection-prenumeranter.

---

## <a name="conditional-access"></a>Villkorlig åtkomst

**F: Vad är nytt med den här funktionen?**

**A.** Kunder kan nu felsöka principer för villkorlig åtkomst genom alla inloggningsrapport. Kunder kan granska statusen villkorlig åtkomst och gå in på information om de principer som tillämpas på inloggningen och resultatet för varje princip.

**F: Hur kommer jag igång?**

**A.** Så här kommer du igång:

* Navigera till inloggningsrapporten i [Azure-portalen](https://portal.azure.com).
* Klicka på inloggningen som du vill felsöka.
* Navigera till fliken **Villkorlig åtkomst.** Här kan du visa alla principer som påverkade inloggningen och resultatet för varje princip. 
    
**F: Vilka är alla möjliga värden för statusen Villkorlig åtkomst?**

**A.** Status för villkorlig åtkomst kan ha följande värden:

* **Används inte**: Det innebär att det inte fanns någon ca-princip med användaren och appen i omfånget. 
* **Framgång**: Det innebär att det fanns en CA-princip med användaren och appen i omfattning och ca-principer lyckades uppfyllas. 
* **Fel**: Det innebär att det fanns en ca-princip med användaren och appen i omfattning och ca-principer inte uppfylldes. 
    
**F: Vilka är alla möjliga värden för principresultatet för principen villkorlig åtkomst?**

**A.** En princip för villkorlig åtkomst kan ha följande resultat:

* **Framgång**: Policyn var nöjd.
* **Misslyckande**: Policyn var inte nöjd.
* **Inte tillämpas**: Det kan bero på att principvillkoren inte uppfylldes.
* **Inte aktiverat**: Detta beror på principen i inaktiverat tillstånd. 
    
**F: Principnamnet i all inloggningsrapport matchar inte principnamnet i certifikatutfärdaren. Varför?**

**A.** Principnamnet i all inloggningsrapport baseras på certifikatutfärdarprincipnamnet vid tidpunkten för inloggningen. Detta kan vara oförenligt med principnamnet i certifikatutfärdaren om du uppdaterade principnamnet senare, det vill säga efter inloggningen.

**F: Min inloggning blockerades på grund av en princip för villkorlig åtkomst, men inloggningsaktivitetsrapporten visar att inloggningen lyckades. Varför?**

**A.** Inloggningsrapporten kanske inte visar korrekta resultat för Exchange ActiveSync-scenarier när villkorlig åtkomst tillämpas. Det kan finnas fall när inloggningsresultatet i rapporten visar en lyckad inloggning, men inloggningen misslyckades på grund av en princip för villkorlig åtkomst. 
