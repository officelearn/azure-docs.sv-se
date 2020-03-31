---
title: Vad är Azure Active Directory Identity Protection?
description: Identifiera, åtgärda, undersöka och analysera risker med Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 03/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2b1d9748b243dcc2104ce7b8e0e8735a7b7276f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79497673"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Vad är Azure Active Directory Identity Protection?

Identitetsskydd är ett verktyg som gör det möjligt för organisationer att utföra tre viktiga uppgifter:

- Automatisera identifiering och reparation av identitetsbaserade risker.
- Undersök risker med hjälp av data i portalen.
- Exportera riskidentifieringsdata till verktyg från tredje part för vidare analys.

Identity Protection använder de lärdomar som Microsoft har förvärvat från sin position i organisationer med Azure AD, konsumentutrymmet med Microsoft-konton och i spel med Xbox för att skydda dina användare. Microsoft analyserar 6,5 biljoner signaler per dag för att identifiera och skydda kunder från hot.

De signaler som genereras av och matas till Identity Protection, kan matas vidare in i verktyg som villkorlig åtkomst för att fatta åtkomstbeslut, eller matas tillbaka till ett verktyg för säkerhetsinformation och händelsehantering (SIEM) för vidare undersökning baserat på din organisations påtvingad politik.

## <a name="why-is-automation-important"></a>Varför är automatisering viktigt?

I sitt [blogginlägg i oktober 2018](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) förklarar Alex Weinert, som leder Microsofts identity security and protection-team, varför automatisering är så viktigt när det handlar om händelsevolymen:

> Varje dag, vår maskininlärning och heuristiska system ger riskpoäng för 18 miljarder inloggningsförsök för över 800 miljoner olika konton, varav 300 miljoner är märkbart gjort av motståndare (enheter som: kriminella aktörer, hackare).
>
> På Ignite förra året talade jag om de tre största attackerna mot våra identitetssystem. Här är den senaste volymen av dessa attacker
>   
>   - **Brott repris:** 4.6BN attacker upptäcktes i maj 2018
>   - **Lösenord spray**: 350k i april 2018
>   - **Phishing**: Detta är svårt att kvantifiera exakt, men vi såg 23M riskhändelser i mars 2018, varav många är phish relaterade

## <a name="risk-detection-and-remediation"></a>Riskdetektering och reparation

Identitetsskydd identifierar risker i följande klassificeringar:

| Typ av riskidentifiering | Beskrivning |
| --- | --- |
| Atypiska resor | Logga in från en atypisk plats baserat på användarens senaste inloggningar. |
| Anonym IP-adress | Logga in från en anonym IP-adress (till exempel: Tor browser, anonymizer VPN). |
| Okända inloggningsegenskaper | Logga in med egenskaper som vi inte har sett nyligen för den angivna användaren. |
| Skadlig kod länkad IP-adress | Logga in från en kopplad IP-adress för skadlig kod |
| Läckta autentiseringsuppgifter | Den här riskidentifieringen indikerar att användarens giltiga autentiseringsuppgifter har läckt ut |
| Azure AD-hotinformation | Microsofts interna och externa hotinformationskällor har identifierat ett känt attackmönster |

Mer information om dessa risker och hur / när de beräknas finns i artikeln, [Vad är risk](concept-identity-protection-risks.md).

Risksignalerna kan utlösa reparationsinsatser som att kräva att användare: utföra Azure Multi-Factor-autentisering, återställa sitt lösenord med självbetjäningslösenordsåterställning eller blockera tills en administratör vidtar åtgärder.

## <a name="risk-investigation"></a>Riskundersökning

Administratörer kan granska identifieringar och vidta manuella åtgärder på dem om det behövs. Det finns tre viktiga rapporter som administratörer använder för undersökningar i Identitetsskydd:

- Riskfyllda användare
- Riskfyllda inloggningar
- Riskidentifieringar

Mer information finns i artikeln, [Hur: Undersöka risk](howto-identity-protection-investigate-risk.md).

## <a name="exporting-risk-data"></a>Exportera riskdata

Data från Identitetsskydd kan exporteras till andra verktyg för arkiv och vidare utredning och samrelation. Med Microsoft Graph-baserade API:er kan organisationer samla in dessa data för vidare bearbetning i ett verktyg som deras SIEM. Information om hur du kommer åt API:et för identitetsskydd finns i artikeln, [Kom igång med Azure Active Directory Identity Protection och Microsoft Graph](howto-identity-protection-graph-api.md)

Information om hur du integrerar identitetsskyddsinformation med Azure Sentinel finns i artikeln [Connect data from Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Behörigheter

Identitetsskydd kräver att användarna är säkerhetsläsare, säkerhetsoperatör, säkerhetsadministratör, global läsare eller global administratör för att komma åt.

| Roll | Kan göra | Kan inte göra |
| --- | --- | --- |
| Global administratör | Fullständig tillgång till identitetsskydd |   |
| Säkerhetsadministratör | Fullständig tillgång till identitetsskydd | Återställa lösenord för en användare |
| Säkerhetsoperatör | Visa alla identitetsskyddsrapporter och översiktsblad <br><br> Avfärda användarrisk, bekräfta säker inloggning, bekräfta kompromiss | Konfigurera eller ändra principer <br><br> Återställa lösenord för en användare <br><br> Konfigurera varningar |
| Säkerhetsläsare | Visa alla identitetsskyddsrapporter och översiktsblad | Konfigurera eller ändra principer <br><br> Återställa lösenord för en användare <br><br> Konfigurera varningar <br><br> Ge feedback på upptäckter |

Villkorlig åtkomstadministratörer kan också skapa principer som inrålningsrisk som ett villkor, hitta mer information i artikeln [Villkorlig åtkomst: Villkor](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk).

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Funktion | Information | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/Gratis |
| --- | --- | --- | --- | --- |
| Riskpolicyer | Användarriskprincip (via identitetsskydd) | Ja | Inga | Inga |
| Riskpolicyer | Princip för inloggningsrisk (via identitetsskydd eller villkorlig åtkomst) | Ja | Inga | Inga |
| Säkerhetsrapporter | Översikt | Ja | Inga | Inga |
| Säkerhetsrapporter | Riskfyllda användare | Fullständig åtkomst | Begränsad information | Begränsad information |
| Säkerhetsrapporter | Riskfyllda inloggningar | Fullständig åtkomst | Begränsad information | Begränsad information |
| Säkerhetsrapporter | Riskidentifieringar | Fullständig åtkomst | Begränsad information | Inga |
| Meddelanden | Användare i riskzonen upptäckta aviseringar | Ja | Inga | Inga |
| Meddelanden | Veckovis sammandrag | Ja | Inga | Inga |
| | MFA-registreringspolicy | Ja | Inga | Inga |

## <a name="next-steps"></a>Nästa steg

- [Säkerhetsöversikt](concept-identity-protection-security-overview.md)

- [Vad är risk](concept-identity-protection-risks.md)

- [Policyer tillgängliga för att minska riskerna](concept-identity-protection-policies.md)
