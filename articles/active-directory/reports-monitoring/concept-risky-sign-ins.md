---
title: Rapporten om riskfyllda inloggningar i Azure Active Directory-portalen | Microsoft Docs
description: Lär dig mer om rapporten över riskfyllda inloggningar i Azure Active Directory-portalen
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 629e7bf8ee7100b5927483ecfd4efa1d9223c151
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989928"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Rapporten över riskfyllda inloggningar i Azure Active Directory-portalen

Azure Active Directory (Azure AD) identifierar misstänkta åtgärder som är relaterade till dina användar konton. För varje identifierad åtgärd skapas en post med namnet **riskhändelse**. Mer information finns i [Azure AD-risk händelser](concept-risk-events.md). 

Du kan komma åt säkerhetsrapporterna via [Azure-portalen](https://portal.azure.com) genom att välja bladet **Azure Active Directory** och sedan navigera till avsnittet **Säkerhet**. 

Det finns två olika säkerhets rapporter som beräknas utifrån risk händelser:

- **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

- **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. 

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/10.png)

Information om hur du konfigurerar principer som utlöser dessa riskhändelser finns i [Konfigurera riskprincipen för användare](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Vem har åtkomst till rapporten över riskfyllda inloggningar?

De riskfyllda inloggnings rapporterna är tillgängliga för användare i följande roller:

- Säkerhetsadministratör
- Global administratör
- Säkerhetsläsare

Information om hur du tilldelar administrativa roller till en användare i Azure Active Directory finns i [Visa och tilldela administratörs roller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Vilken Azure AD-licens behöver du för att komma åt en säkerhetsrapport?  

Alla utgåvor av Azure AD tillhandahåller rapporter om riskfyllda inloggningar. Nivån av rapportens detaljrikedom varierar dock mellan versionerna: 

- I **Azure Active Directory Free-och Basic-versioner**får du en lista över riskfyllda inloggningar. 

- Dessutom kan du med utgåvan **Azure Active Directory Premium 1** utforska några av de underliggande riskhändelser som har identifierats för varje rapport. 

- Utgåvan **Azure Active Directory Premium 2** ger den mest detaljerade informationen om alla underliggande riskhändelser och du kan konfigurera säkerhetsprinciper som automatiskt svarar på konfigurerade risknivåer.

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Rapport över riskfyllda inloggningar för Azure AD kostnads fri och Basic Edition

Azure AD-versionerna kostnads fri och Basic tillhandahåller en lista över riskfyllda inloggningar som har identifierats för dina användare. Varje post innehåller följande attribut:

- **Användare** – namnet på den användare som användes vid inloggnings åtgärden.
- **IP** – IP-adressen för enheten som användes för att ansluta till Azure Active Directory.
- **Plats** – platsen som används för att ansluta till Azure Active Directory. Detta är en ungefärlig bästa prestanda som baseras på spår, register data, omvänd sökning och annan information.
- **Inloggningstid** – Tidpunkten för inloggningen.
- **Status** – Inloggningens status.

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/01.png)

Utifrån din undersökning av riskfyllda inloggningar kan du ge feedback till Azure AD genom att utföra följande åtgärder:

- Lös
- Markera som falskt positivt resultat
- Ignorera
- Återaktivera

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/21.png)

Den här rapporten innehåller också ett alternativ för att:

- Sök efter resurser
- Ladda ned rapportdata

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Rapport över riskfyllda inloggningar för Azure AD Premium-versioner

I rapporten riskfyllda inloggningar i Azure AD Premium-utgåvorna får du:

- Sammanställd information om de [typer av risk händelser](concept-risk-events.md) som har identifierats. Med **Azure AD Premium P1-versionen**visas identifieringar som inte täcks av din licens som en risk händelse **inloggning med ytterligare risk upptäckt**. Med **Azure AD Premium P2-versionen**får du den mest detaljerade informationen om alla underliggande identifieringar.

- Ett alternativ för att ladda ned rapporten

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/456.png)

När du väljer en riskhändelse kan få en detaljerad rapportvy för den här riskhändelsen som gör att du kan göra följande:

- Ett alternativ för att konfigurera en [princip för att åtgärda användarrisker](../identity-protection/howto-user-risk-policy.md)  

- Granska identifieringstidslinjen för riskhändelsen  

- Granska en lista över användare för vilka den här riskhändelsen har upptäckts

- Stäng risk händelser manuellt. 

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Ibland kan du hitta en risk händelse utan motsvarande inloggnings post i [inloggnings rapporten](concept-sign-ins.md). Detta beror på att identitets skyddet utvärderar risker för både **interaktiva** och **icke-interaktiva** inloggningar, medan inloggnings rapporten endast visar interaktiva inloggningar.

När du väljer en användare får du en detaljerad rapportvy för den här användaren som du kan använda för att göra följande:

- Öppna vyn Alla inloggningar

- Återställ användarens lösenord

- Ignorera alla händelser

- Undersök rapporterade riskhändelser för användaren. 

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/324.png)

Välj en riskhändelse i listan om du vill undersöka den.  
Detta öppnar bladet **Information** för den här riskhändelsen. På bladet **Information** har du möjlighet att antingen stänga en riskhändelse manuellt eller återaktivera en manuellt stängd riskhändelse. 

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera riskprincipen för användare](../identity-protection/howto-user-risk-policy.md)
- [Konfigurera riskåtgärdsprincipen](../identity-protection/howto-user-risk-policy.md)
- [Typer av riskhändelser](concept-risk-events.md)
