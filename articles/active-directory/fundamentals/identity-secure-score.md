---
title: Vad är identitet säker poäng? - Azure Active Directory
description: Hur du kan använda den säkra poängen identitet för att förbättra säkerhetspositionen för din katalog
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdff2305914ca6e4144f7784d1a60026a1d27c0
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988696"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Vad är identitetssäkerhetspoäng i Azure Active Directory?

Hur säker är din Azure AD-klientorganisation? Om du inte vet hur de ska svara på den här frågan, förklarar den här artikeln hur säker poängen identitet hjälper dig att övervaka och förbättra din säkerhetsposition identitet.

## <a name="what-is-an-identity-secure-score"></a>Vad är identitetssäkerhetspoäng?

Identitet säker poängen är ett tal mellan 1 och 223 som fungerar som indikerar hur justerade du är med Microsofts rekommendationer om metodtips för säkerhet. Varje förbättring åtgärd i identity säker poäng skräddarsys för din specifika konfiguration.  

![Säkerhetspoäng](./media/identity-secure-score/identity-secure-score-overview.png)

Resultatet hjälper dig att:

- Objektivt mäta din identitetssäkerhetsstatus
- Planera förbättringar i identitetssäkerheten
- Granska framgången för dina förbättringar

Du kan komma åt poängen och tillhörande information på instrumentpanelen för identitetssäkerhetspoäng. På den här instrumentpanelen hittar du:

- Dina säker identitets-poäng
- En jämförelse-diagram som visar hur din identitet skyddar score Jämför med andra klienter i samma bransch och liknande storlek
- En trend-diagram som visar hur dina säker identitets-poäng har ändrats över tid
- En lista över möjliga förbättringar

Med hjälp av följande förbättringsåtgärder kan du:

- Förbättra din säkerhetsposition och dina poäng
- Utnyttja funktionerna som är tillgänglig för din organisation som en del av dina investeringar för identitet

## <a name="how-do-i-get-my-secure-score"></a>Hur får jag mina säkerhetspoäng?

Identitet säker poängen är tillgänglig i alla utgåvor av Azure AD. Du kommer åt dina poäng på [översiktsinstrumentpanelen för Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore).

## <a name="how-does-it-work"></a>Hur fungerar det?

Var 48 timme tittar Azure på din säkerhetskonfiguration och jämför dina inställningar med de rekommenderade metoderna. Baserat på resultatet av denna utvärdering, beräknas en ny poäng för din katalog. Det är möjligt att säkerhetskonfiguration är inte helt i linje med de bästa praxis riktlinjer och förbättring åtgärder endast delvis är uppfyllda. I dessa scenarier kan tilldelas du bara en del av din högsta poäng som är tillgängliga för kontrollen.

Varje rekommendation mäts baserat på din Azure AD-konfiguration. Om du använder produkter från tredje part för att aktivera en rekommendation för bästa praxis, anger du den här konfigurationen i inställningarna för en åtgärd för förbättring. Du har också möjlighet att ange rekommendationer ignoreras om inte användas för din miljö. En ignorerad rekommendation bidrar inte till beräkningen av dina poäng.

![Ignorera eller markera åtgärd som omfattas av tredje part](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>Hur hjälper det här mig?

Med säkerhetspoängen kan du:

- Objektivt mäta din identitetssäkerhetsstatus
- Planera förbättringar i identitetssäkerheten
- Granska framgången för dina förbättringar

## <a name="what-you-should-know"></a>Det här bör du känna till

### <a name="who-can-use-the-identity-secure-score"></a>Vilka kan använda identitetssäkerhetspoängen?

Identitetssäkerhetspoängen kan användas av följande roller:

- Global administratör
- Säkerhetsadministratör
- Säkerhetsläsare

### <a name="how-are-controls-scored"></a>Hur poängsätts kontroller?

Kontroller kan mätas på två sätt. Vissa poängsätts på ett binärt sätt – du får 100% av poängen om funktionen eller ange konfigurerade baseras på våra rekommendationer. Andra poängen beräknas som en procentandel av total konfigurationen. Till exempel om improvement rekommendationen anger du får 30 punkter om du skyddar alla dina användare med MFA och du har bara 5 100 Totalt antal användare som är skyddade, du skulle få en partiell poäng cirka 2 punkter (5 skyddade / 100 totalt * 30 max punkter = 2 punkter partiella poäng) .

### <a name="what-does-not-scored-mean"></a>Vad betyder [Not Scored] ([Inte poängsatt])?

Åtgärder märkta med [Not Scored] ([Inte poängsatt]) är sådana du kan utföra i organisationen men som inte poängsätts eftersom de inte är anslutna till verktyget (än!). Du kan alltså fortfarande förbättra din säkerhet men du får inga poäng för de åtgärderna just nu.

### <a name="how-often-is-my-score-updated"></a>Hur ofta uppdateras mina poäng?

Poängen beräknas en gång per dag (kring 01:00 PST). Om du gör en ändring av en åtgärd som mäts uppdateras poängen automatiskt nästa dag. Det tar upp till 48 timmar innan en ändring återspeglas i dina poäng.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Mina poäng har ändrats. Hur får jag reda på varför?

Gå till den [Microsoft 365 Säkerhetscenter](https://security.microsoft.com/), där du hittar dina fullständig Microsoft säker poäng. Du kan enkelt se alla ändringar till ditt säkra poängen genom att granska detaljerad ändringarna på fliken Historik.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Säker poängen mäta min risken för att komma brott mot?

Det korta svaret är nej. Säker poängen inte express ett absolut mått på hur sannolikt är att hämta brott mot. De anger i vilken utsträckning du har implementerat funktioner som kan motverka risken för intrång. Ingen tjänst kan garantera att inte intrång och säker poängen ska inte tolkas som en garanti för på något sätt.

### <a name="how-should-i-interpret-my-score"></a>Hur ska jag tolka mina poäng?

Du får poäng när du konfigurerar rekommenderade säkerhetsfunktioner eller när du utför säkerhetsrelaterade uppgifter (som att läsa repporter). Vissa åtgärder poängsätts när de är delvis slutförda, som att aktivera multifaktorautentisering (MFA) för dina användare. Säker poängen är direkt av Microsoft security-tjänster som du använder. Kom ihåg att säkerheten måste balanseras med användbarhet. Alla säkerhetskontroller har en komponent med användarpåverkan. Kontroller med låg användarpåverkan bör ha lite eller ingen effekt på användarnas dagliga åtgärder.

Om du vill se dina tidigare resultat, gå till den [Microsoft 365 Säkerhetscenter](https://security.microsoft.com/) och granska din övergripande Microsoft säker poäng. Du kan granska ändringar till din övergripande säker poäng klickar på Visa historik. Välj ett specifikt datum för att se vilka kontroller som hade aktiverats för den dagen och vilka poäng du har fått för varje kontroll.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Hur är identitetssäkerhetspoäng relaterade till säkerhetspoäng i Office 365 Secure Score?

Den [Microsoft skyddar score](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) innehåller fem olika kontroll- och poäng kategorier:

- Identitet
- Data
- Enheter
- Infrastruktur
- Appar

Identitet säker poängen representerar den identitet som en del av Microsoft säker poängen. Den här överlappar varandra innebär att dina rekommendationer för identiteten skyddar score och identitet poängen på Microsoft är desamma.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om Microsoft säker poäng](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
