---
title: Vad är identitetssäker poäng? - Azure Active Directory
description: Hur du kan använda identitetssäkra poäng för att förbättra säkerhetspositionen i din katalog
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f75dea2cffbe710bf2778ceab5eacc91ffcca9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523124"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Vad är identitetssäkerhetspoäng i Azure Active Directory?

Hur säker är din Azure AD-klientorganisation? Om du inte vet hur du ska svara på den här frågan förklarar den här artikeln hur den identitetssäkra poängen hjälper dig att övervaka och förbättra din identitetssäkerhetsposition.

## <a name="what-is-an-identity-secure-score"></a>Vad är identitetssäkerhetspoäng?

Den identitetssäkra poängen är ett nummer mellan 1 och 223 som fungerar som en indikator för hur anpassad du är till Microsofts rekommendationer om bästa praxis för säkerhet. Varje förbättringsåtgärd i identitetssäker poäng är skräddarsydd för din specifika konfiguration.  

![Säkerhetspoäng](./media/identity-secure-score/identity-secure-score-overview.png)

Resultatet hjälper dig att:

- Objektivt mäta din identitetssäkerhetsstatus
- Planera förbättringar i identitetssäkerheten
- Granska framgången för dina förbättringar

Du kan komma åt poängen och tillhörande information på instrumentpanelen för identitetssäkerhetspoäng. På den här instrumentpanelen hittar du:

- Din identitetssäkra poäng
- Ett jämförelsediagram som visar hur din identitetssäkra poäng kan jämföras med andra klienter i samma bransch och liknande storlek
- Ett trenddiagram som visar hur din identitetssäkra poäng har förändrats med tiden
- En lista över möjliga förbättringar

Med hjälp av följande förbättringsåtgärder kan du:

- Förbättra din säkerhetsposition och din poäng
- Dra nytta av de funktioner som är tillgängliga för din organisation som en del av dina identitetsinvesteringar

## <a name="how-do-i-get-my-secure-score"></a>Hur får jag mina säkerhetspoäng?

Identitetssäker poäng är tillgänglig i alla versioner av Azure AD. Organisationer kan komma åt sin identitetssäkra poäng från **Azure Portal** > **Azure Active Directory** > **Security** > **Identity Secure Score**.

## <a name="how-does-it-work"></a>Hur fungerar det?

Var 48 timme tittar Azure på din säkerhetskonfiguration och jämför dina inställningar med de rekommenderade metoderna. Baserat på resultatet av den här utvärderingen beräknas en ny poäng för din katalog. Det är möjligt att säkerhetskonfigurationen inte är helt anpassad till vägledningen om bästa praxis och att förbättringsåtgärderna endast delvis uppfylls. I dessa scenarier kommer du bara att tilldelas en del av maxpoängen som är tillgänglig för kontrollen.

Varje rekommendation mäts baserat på din Azure AD-konfiguration. Om du använder produkter från tredje part för att aktivera en rekommendation om bästa praxis kan du ange den här konfigurationen i inställningarna för en förbättringsåtgärd. Du har också möjlighet att ange rekommendationer som ska ignoreras om de inte gäller för din miljö. En ignorerad rekommendation bidrar inte till beräkningen av dina poäng.

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

### <a name="how-are-controls-scored"></a>Hur poängsätts kontrollerna?

Kontroller kan göras på två sätt. Vissa poängsätts på ett binärt sätt - du får 100% av poängen om du har funktionen eller inställningen konfigurerad baserat på vår rekommendation. Övriga poäng beräknas som en procentandel av den totala konfigurationen. Till exempel, om förbättring rekommendationen anger att du får 30 poäng om du skyddar alla dina användare med MFA och du bara har 5 av 100 totala användare skyddade, skulle du få en partiell poäng runt 2 poäng (5 skyddade / 100 totalt * 30 max poäng = 2 poäng partiell poäng).

### <a name="what-does-not-scored-mean"></a>Vad betyder [Not Scored] ([Inte poängsatt])?

Åtgärder märkta med [Not Scored] ([Inte poängsatt]) är sådana du kan utföra i organisationen men som inte poängsätts eftersom de inte är anslutna till verktyget (än!). Du kan alltså fortfarande förbättra din säkerhet men du får inga poäng för de åtgärderna just nu.

### <a name="how-often-is-my-score-updated"></a>Hur ofta uppdateras mina poäng?

Poängen beräknas en gång per dag (kring 01:00 PST). Om du gör en ändring av en åtgärd som mäts uppdateras poängen automatiskt nästa dag. Det tar upp till 48 timmar innan en ändring återspeglas i dina poäng.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Mina poäng har ändrats. Hur får jag reda på varför?

Gå över till [Microsoft 365 security center](https://security.microsoft.com/), där du hittar din fullständiga Microsoft säker poäng. Du kan enkelt se alla ändringar i din säkra poäng genom att granska de djupgående ändringarna på fliken historik.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>Mäter den säkra poängen min risk att bli kränkt?

Det korta svaret är nej. Den säkra poängen uttrycker inte ett absolut mått på hur sannolikt det är att du kommer att bli kränkt. De anger i vilken utsträckning du har implementerat funktioner som kan motverka risken för intrång. Ingen tjänst kan garantera att du inte kommer att brytas, och den säkra poängen bör inte tolkas som en garanti på något sätt.

### <a name="how-should-i-interpret-my-score"></a>Hur ska jag tolka mina poäng?

Du får poäng när du konfigurerar rekommenderade säkerhetsfunktioner eller när du utför säkerhetsrelaterade uppgifter (som att läsa repporter). Vissa åtgärder poängsätts när de är delvis slutförda, som att aktivera multifaktorautentisering (MFA) för dina användare. Din säkra poäng är direkt representativ för de Microsoft-säkerhetstjänster du använder. Kom ihåg att säkerheten måste balanseras med användbarhet. Alla säkerhetskontroller har en komponent med användarpåverkan. Kontroller med låg användarpåverkan bör ha lite eller ingen effekt på användarnas dagliga åtgärder.

Om du vill se din poänghistorik går du till [säkerhetscentret Microsoft 365](https://security.microsoft.com/) och granskar din totala Microsoft-säkerhetspoäng. Du kan granska ändringar i din totala säkra poäng att klicka på Visa historik. Välj ett specifikt datum för att se vilka kontroller som hade aktiverats för den dagen och vilka poäng du har fått för varje kontroll.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Hur är identitetssäkerhetspoäng relaterade till säkerhetspoäng i Office 365 Secure Score?

[Microsofts säkra poäng](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) innehåller fem olika kontroll- och poängkategorier:

- Identitet
- Data
- Enheter
- Infrastruktur
- Program

Den identitetssäkra poängen representerar identitetsdelen av Microsofts säkra poäng. Den här överlappningen innebär att dina rekommendationer för identitetssäkra poäng och identitetspoängen i Microsoft är desamma.

## <a name="next-steps"></a>Nästa steg

[Läs mer om Microsofts säkra poäng](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
