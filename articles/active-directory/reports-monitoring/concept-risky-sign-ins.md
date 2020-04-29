---
title: Rapport över riskfyllda inloggningar i portalen | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "78273842"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Rapporten över riskfyllda inloggningar i Azure Active Directory-portalen

Azure Active Directory (Azure AD) identifierar misstänkta åtgärder som är relaterade till dina användar konton. För varje identifierad åtgärd skapas en post som kallas **identifiering av risker** . Mer information finns i [identifieringar av Azure AD-risker](concept-risk-events.md). 

Du kan komma åt säkerhetsrapporterna via [Azure-portalen](https://portal.azure.com) genom att välja bladet **Azure Active Directory** och sedan navigera till avsnittet **Säkerhet**. 

Det finns två olika säkerhets rapporter som beräknas utifrån risk identifiering:

- **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

- **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. 

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/10.png)

Information om hur du konfigurerar principerna som utlöser dessa risk identifieringar finns i [så här konfigurerar du principen för användar risk](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Vem har åtkomst till rapporten över riskfyllda inloggningar?

De riskfyllda inloggnings rapporterna är tillgängliga för användare i följande roller:

- Säkerhetsadministratör
- Global administratör
- Säkerhetsläsare

Information om hur du tilldelar administrativa roller till en användare i Azure Active Directory finns i [Visa och tilldela administratörs roller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Vilken Azure AD-licens behöver du för att komma åt en säkerhetsrapport?  

Alla utgåvor av Azure AD tillhandahåller rapporter om riskfyllda inloggningar. Nivån av rapportens detaljrikedom varierar dock mellan versionerna: 

- I **Azure Active Directory frees versionen**får du en lista över riskfyllda inloggningar. 

- Dessutom kan du med **Azure Active Directory Premium 1** -versionen granska några av de underliggande risk identifieringarna som har identifierats för varje rapport. 

- **Azure Active Directory Premium 2** -versionen ger den mest detaljerade informationen om alla underliggande risk identifieringar och du kan också konfigurera säkerhets principer som automatiskt svarar på konfigurerade risk nivåer.

## <a name="risky-sign-ins-report-for-azure-ad-free-edition"></a>Rapport över riskfyllda inloggningar för Azure AD kostnads fri utgåva

Den kostnads fria versionen av Azure AD tillhandahåller en lista över riskfyllda inloggningar som har identifierats för dina användare. Varje post innehåller följande attribut:

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

- Sammanställd information om de [typer av risk identifiering](concept-risk-events.md) som har identifierats. Med **Azure AD Premium P1-versionen**visas identifieringar som inte täcks av din licens som inloggning med risk identifiering **med ytterligare risk upptäckt**. Med **Azure AD Premium P2-versionen**får du den mest detaljerade informationen om alla underliggande identifieringar.

- Ett alternativ för att ladda ned rapporten

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/456.png)

När du väljer en risk identifiering får du en detaljerad rapportvy för den här identifieringen av risker som gör att du kan:

- Ett alternativ för att konfigurera en [princip för att åtgärda användarrisker](../identity-protection/howto-user-risk-policy.md)  

- Granska identifierings tids linjen för identifiering av risker  

- Granska en lista med användare för vilka den här risk identifieringen har upptäckts

- Manuellt stänga risk identifieringar. 

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Ibland kan du hitta en risk identifiering utan motsvarande inloggnings post i [inloggnings rapporten](concept-sign-ins.md). Detta beror på att identitets skyddet utvärderar risker för både **interaktiva** och **icke-interaktiva** inloggningar, medan inloggnings rapporten endast visar interaktiva inloggningar.

När du väljer en användare får du en detaljerad rapportvy för den här användaren som du kan använda för att göra följande:

- Öppna vyn Alla inloggningar

- Återställ användarens lösenord

- Ignorera alla händelser

- Undersök rapporterade risk identifieringar för användaren. 

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/324.png)

Om du vill undersöka en risk identifiering väljer du en i listan.  
Då öppnas **informations** bladet för den här identifieringen av risker. På bladet **information** har du möjlighet att antingen manuellt stänga en risk identifiering eller återaktivera en manuellt stängd risk identifiering. 

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera riskprincipen för användare](../identity-protection/howto-user-risk-policy.md)
- [Konfigurera riskåtgärdsprincipen](../identity-protection/howto-user-risk-policy.md)
- [Typer av risk identifiering](concept-risk-events.md)
