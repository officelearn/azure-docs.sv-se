---
title: Användare som flaggats i säkerhetsrapporten i Azure Active Directory | Microsoft Docs
description: Lär dig mer om användare som flaggats i säkerhetsrapporten i Azure Active Directory-portalen
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/14/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 030774716e1af4a7d6817d64ae66ded2bcaf4081
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "41920782"
---
# <a name="users-flagged-for-risk-security-report-in-the-azure-active-directory-portal"></a>Användare som flaggats i säkerhetsrapporten i Azure Active Directory-portalen

Med hjälp av säkerhetsrapporterna i Azure Active Directory (Azure AD) kan du bedöma risken för att användarkonton i din miljö har komprometterats. 

Azure Active Directory identifierar misstänkta åtgärder relaterade till dina användarkonton. För varje identifierad åtgärd skapas en post med namnet *riskhändelse*. Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](concept-risk-events.md). 

De identifierade riskhändelserna används för att beräkna:

- **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. Mer information finns i avsnittet om [riskfyllda inloggningar](../identity-protection/overview.md#risky-sign-ins). 

- **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. Mer information finns i avsnittet om [användare som har flaggats för risk](../identity-protection/overview.md#users-flagged-for-risk).  

I Azure-portalen hittar du säkerhetsrapporter på bladet **Azure Active Directory** i avsnittet **Säkerhet**.  

![Riskfyllda inloggningar](./media/concept-user-at-risk/10.png)



## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Vilken Azure AD-licens behöver du för att komma åt en säkerhetsrapport?  

Alla utgåvor av Azure Active Directory ger rapporter över användare som har flaggats för risk.  
Nivån av rapportens detaljrikedom varierar dock mellan versionerna: 

- I **versionerna Azure Active Directory Free och Basic** finns redan en lista över användare som har flaggats för risk. 

- Utgåvan **Azure Active Directory Premium 1** har en utökad modell där du även kan utforska några av de underliggande riskhändelser som har identifierats för varje rapport. 

- Utgåvan **Azure Active Directory Premium 2** ger den mest detaljerade informationen om alla underliggande riskhändelser och du kan konfigurera säkerhetsprinciper som automatiskt svarar på konfigurerade risknivåer.



## <a name="azure-active-directory-free-and-basic-edition"></a>Azure Active Directory kostnadsfri och grundläggande utgåva

Rapporten om användare som flaggats för risk i den kostnadsfria och grundläggande versionen av Azure Active Directory tillhandahåller en lista över användarkonton som kan ha komprometterats. 


![Riskfyllda inloggningar](./media/concept-user-at-risk/03.png)

Om du klickar på en användare öppnas tillhörande blad med användardata.
För användare i farozonen går du igenom användarens inloggningshistorik och återställer lösenordet om det behövs.

![Riskfyllda inloggningar](./media/concept-user-at-risk/46.png)


Dialogrutan tillhandahåller ett alternativ för att:

- Ladda ned rapport

- Söka efter användare

![Riskfyllda inloggningar](./media/concept-user-at-risk/16.png)


## <a name="azure-active-directory-premium-editions"></a>Azure Active Directory Premium-versioner

Rapporten om användare som flaggats för risk i Azure Active Directory Premium-versionerna innehåller följande:

- En [lista över användarkonton](../identity-protection/overview.md#users-flagged-for-risk) som kan ha drabbats 

- Sammanställd information om de [riskhändelsetyper](concept-risk-events.md) som har identifierats

- Ett alternativ för att ladda ned rapporten

- Ett alternativ för att konfigurera en [princip för att åtgärda användarrisker](../identity-protection/overview.md#user-risk-security-policy)  


![Riskfyllda inloggningar](./media/concept-user-at-risk/71.png)

När du väljer en användare får du en detaljerad rapportvy för den här användaren som du kan använda för att göra följande:

- Öppna vyn Alla inloggningar

- Återställ användarens lösenord

- Ignorera alla händelser

- Undersök rapporterade riskhändelser för användaren. 


![Riskfyllda inloggningar](./media/concept-user-at-risk/324.png)


Om du vill undersöka en riskhändelse, markerar du en på listan för att öppna bladet med **Information** om den riskhändelsen. På bladet **Information** har du möjlighet att antingen [stänga en riskhändelse manuellt](../identity-protection/overview.md#closing-risk-events-manually) eller återaktivera en manuellt stängd riskhändelse. 


![Riskfyllda inloggningar](./media/concept-user-at-risk/325.png)



## <a name="next-steps"></a>Nästa steg

- Mer information om Azure Active Directory Identity Protection finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

