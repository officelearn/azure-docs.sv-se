---
title: Riskarisk inloggningsrapport i portal | Microsoft-dokument
description: Lär dig mer om rapporten över riskfyllda inloggningar i Azure Active Directory-portalen
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77486064139895799ac5a48327377154f75da6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273842"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Rapporten över riskfyllda inloggningar i Azure Active Directory-portalen

Azure Active Directory (Azure AD) identifierar misstänkta åtgärder som är relaterade till dina användarkonton. För varje identifierad åtgärd skapas en post som kallas **riskidentifiering.** Mer information finns i [Azure AD-riskidentifieringar](concept-risk-events.md). 

Du kan komma åt säkerhetsrapporterna via [Azure-portalen](https://portal.azure.com) genom att välja bladet **Azure Active Directory** och sedan navigera till avsnittet **Säkerhet**. 

Det finns två olika säkerhetsrapporter som beräknas baserat på riskidentifieringarna:

- **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

- **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. 

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/10.png)

Mer information om hur du konfigurerar principer som utlöser dessa riskidentifieringar finns i [Så här konfigurerar du användarriskprincipen](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Vem kan komma åt rapporten för riskfyllda inloggningar?

De riskfyllda inloggningsrapporterna är tillgängliga för användare i följande roller:

- Säkerhetsadministratör
- Global administratör
- Säkerhetsläsare

Mer information om hur du tilldelar administrativa roller till en användare i Azure Active Directory finns [i Visa och tilldela administratörsroller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Vilken Azure AD-licens behöver du för att komma åt en säkerhetsrapport?  

Alla versioner av Azure AD ger dig riskfyllda inloggningsrapporter. Nivån av rapportens detaljrikedom varierar dock mellan versionerna: 

- I **Azure Active Directory Free Edition**får du en lista över riskfyllda inloggningar. 

- Dessutom kan **Azure Active Directory Premium 1-utgåvan** undersöka några av de underliggande riskidentifieringar som har identifierats för varje rapport. 

- **Azure Active Directory Premium 2-versionen** ger dig den mest detaljerade informationen om alla underliggande riskidentifieringar och det gör att du också kan konfigurera säkerhetsprinciper som automatiskt svarar på konfigurerade risknivåer.

## <a name="risky-sign-ins-report-for-azure-ad-free-edition"></a>Riskarisk inloggningsrapport för Azure AD-friutgåva

Azure AD free edition ger dig en lista över riskfyllda inloggningar som har upptäckts för dina användare. Varje post innehåller följande attribut:

- **Användare** - Namnet på den användare som användes under inloggningen.
- **IP** - IP-adressen för den enhet som användes för att ansluta till Azure Active Directory.
- **Plats** - Den plats som används för att ansluta till Azure Active Directory. Detta är en bästa ansträngning tillnärmning baserat på spår, registerdata, omvänd look ups och annan information.
- **Inloggningstid** – Tidpunkten för inloggningen.
- **Status** – Inloggningens status.

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/01.png)

Baserat på din undersökning av den riskfyllda inloggningen kan du ge feedback till Azure AD genom att vidta följande åtgärder:

- Lös
- Markera som falskt positivt resultat
- Ignorera
- Återaktivera

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/21.png)

Den här rapporten ger dig också möjlighet att:

- Sök efter resurser
- Ladda ned rapportdata

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Riskabile en rapport om inloggningar för Azure AD premium-utgåvor

Rapporten riskfyllda inloggningar i Azure AD premium-utgåvorna ger dig:

- Aggregerad information om de [riskidentifieringstyper](concept-risk-events.md) som har upptäckts. Med **Azure AD Premium P1-utgåvan**visas identifieringar som inte omfattas av din licens som riskidentifieringsavkännings **inloggning med ytterligare riskidentifiering.** Med **Azure AD Premium P2-utgåvan**får du den mest detaljerade informationen om alla underliggande identifieringar.

- Ett alternativ för att ladda ned rapporten

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/456.png)

När du väljer en riskidentifiering får du en detaljerad rapportvy för den här riskidentifieringen som gör att du kan:

- Ett alternativ för att konfigurera en [princip för att åtgärda användarrisker](../identity-protection/howto-user-risk-policy.md)  

- Granska identifieringstidslinjen för riskidentifiering  

- Granska en lista över användare för vilka den här riskidentifieringen har upptäckts

- Stäng riskidentifiering manuellt. 

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Ibland kan du hitta en riskidentifiering utan motsvarande [inloggningspost i inloggningsrapporten](concept-sign-ins.md). Detta beror på att Identity Protection utvärderar risken för både **interaktiva** och **icke-interaktiva** inloggningar, medan inloggningsrapporten bara visar interaktiva inloggningar.

När du väljer en användare får du en detaljerad rapportvy för den här användaren som du kan använda för att göra följande:

- Öppna vyn Alla inloggningar

- Återställ användarens lösenord

- Ignorera alla händelser

- Undersök rapporterade riskidentifieringar för användaren. 

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/324.png)

Om du vill undersöka en riskidentifiering väljer du en i listan.  
Detta öppnar **bladet Detaljer** för den här riskdetekteringen. På **bladet Detaljer** har du möjlighet att antingen manuellt stänga en riskdetektering eller återaktivera en manuellt sluten riskdetektering. 

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera riskprincipen för användare](../identity-protection/howto-user-risk-policy.md)
- [Konfigurera riskåtgärdsprincipen](../identity-protection/howto-user-risk-policy.md)
- [Typer av riskidentifiering](concept-risk-events.md)
