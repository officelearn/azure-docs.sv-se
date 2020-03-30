---
title: Användare som flaggats i säkerhetsrapporten i Azure Active Directory | Microsoft Docs
description: Lär dig mer om användare som flaggats i säkerhetsrapporten i Azure Active Directory-portalen
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 894d8dfb7f870ec4a2a11f1d75ee0376b25d8c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014457"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Rapport över användare som har flaggats för risk i Azure-portalen

Azure Active Directory (Azure AD) identifierar misstänkta åtgärder relaterade till dina användarkonton. För varje identifierad åtgärd skapas en post som kallas [riskidentifiering.](concept-risk-events.md)

Du kan komma åt säkerhetsrapporterna via [Azure-portalen](https://portal.azure.com) genom att välja bladet **Azure Active Directory** och sedan navigera till avsnittet **Säkerhet**. 

De upptäckta riskidentifieringarna används för att beräkna:

- **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. 

- **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. 

Mer information om hur du konfigurerar principer som utlöser dessa riskidentifieringar finns i [Så här konfigurerar du användarriskprincipen](../identity-protection/howto-user-risk-policy.md). 

![Riskfyllda inloggningar](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Vilken Azure AD-licens behöver du för åtkomst till rapporten över användare i farozonen?  

Alla utgåvor av Azure Active Directory ger rapporter över användare som har flaggats för risk. Nivån av rapportens detaljrikedom varierar dock mellan versionerna: 

- I **versionerna Azure Active Directory Free och Basic** finns en lista över användare som har flaggats för risk. 

- Dessutom kan **Azure Active Directory Premium 1-utgåvan** undersöka några av de underliggande riskidentifieringar som har identifierats för varje rapport. 

- **Azure Active Directory Premium 2-versionen** ger dig den mest detaljerade informationen om alla underliggande riskidentifieringar och det gör att du också kan konfigurera säkerhetsprinciper som automatiskt svarar på konfigurerade risknivåer.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Rapport över användare i farozonen för Azure AD Free och Basic

Rapporten om användare som flaggats för risk i den kostnadsfria och grundläggande versionen av Azure AD tillhandahåller en lista över användarkonton som kan ha komprometterats. 

![Riskfyllda inloggningar](./media/concept-user-at-risk/03.png)

När en användare väljs visas autentiseringsuppgifter. För användare i farozonen går du igenom användarens inloggningshistorik och återställer lösenordet om det behövs.

Dialogrutan tillhandahåller ett alternativ för att:

- Ladda ned rapport
- Söka efter användare

    ![Riskfyllda inloggningar](./media/concept-user-at-risk/16.png)

För att få mer detaljerad information behöver du en premiumlicens.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Rapport över användare i farozonen för Azure AD Premium-versioner

Rapporten om användare som flaggats för risk i Azure AD Premium-versionerna innehåller följande:

- En lista över användarkonton som kan ha drabbats 

- Aggregerad information om de [riskidentifieringstyper](concept-risk-events.md) som har upptäckts

- Ett alternativ för att ladda ned rapporten

- Ett alternativ för att konfigurera en [princip för att åtgärda användarrisker](../identity-protection/howto-user-risk-policy.md)  

![Riskfyllda inloggningar](./media/concept-user-at-risk/71.png)

När du väljer en användare får du en detaljerad rapportvy för den här användaren som du kan använda för att göra följande:

- Öppna vyn Alla inloggningar

- Återställ användarens lösenord

- Ignorera alla händelser

- Undersök rapporterade riskidentifieringar för användaren. 

![Riskfyllda inloggningar](./media/concept-user-at-risk/324.png)

Om du vill undersöka en riskidentifiering väljer du en i listan för att öppna **bladet Detaljer** för den här riskidentifieringen. På **bladet Detaljer** har du möjlighet att antingen manuellt stänga en riskdetektering eller återaktivera en manuellt sluten riskdetektering. 

![Riskfyllda inloggningar](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Nästa steg

- [Konfigurera riskprincipen för användare](../identity-protection/howto-user-risk-policy.md)
- [Konfigurera riskåtgärdsprincipen](../identity-protection/howto-user-risk-policy.md)
- [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)

