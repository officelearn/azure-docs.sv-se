---
title: "Säkerhetsrapporten Användare i farozonen i förhandsutgåvan av Azure Active Directory | Microsoft Docs"
description: "Lär dig mer om rapporten om användare i farozonen i Azure Active Directory-portalen – förhandsutgåva"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 349109e0c12a1394f96529a94ab884eeb451d242
ms.openlocfilehash: 48c504a9ed5bc4ef9f0bff889df031962c5bf6e8
ms.lasthandoff: 02/22/2017


---
# <a name="users-at-risk-security-report-in-the-azure-active-directory-portal---preview"></a>Säkerhetsrapporten Användare i farozonen i portalen Azure Active Directory – förhandsutgåva

Med hjälp av säkerhetsrapporterna i [förhandsversionen](active-directory-preview-explainer.md) av Azure Active Directory kan du bedöma risken för att användarkonton i din miljö har komprometterats. 

Azure Active Directory identifierar misstänkta åtgärder relaterade till dina användarkonton. För varje identifierad åtgärd skapas en post med namnet *riskhändelse*. Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](active-directory-identity-protection-risk-events.md). 

De identifierade riskhändelserna används för att beräkna:

- **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. Mer information finns i avsnittet om [riskfyllda inloggningar](active-directory-identityprotection.md#risky-sign-ins). 

- **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. Mer information finns i avsnittet om [användare som har flaggats för risk](active-directory-identityprotection.md#users-flagged-for-risk).  

I Azure-portalen hittar du säkerhetsrapporter på bladet **Azure Active Directory** i avsnittet **Säkerhet**.  

![Riskfyllda inloggningar](./media/active-directory-reporting-security-user-at-risk/10.png)

## <a name="azure-active-directory-free-and-basic-edition"></a>Azure Active Directory kostnadsfri och grundläggande utgåva

Rapporten om användare i farozonen i den kostnadsfria och grundläggande versionen av Azure Active Directory tillhandahåller en lista över användarkonton som kan ha komprometterats. 


![Riskfyllda inloggningar](./media/active-directory-reporting-security-user-at-risk/03.png)

Om du klickar på en användare öppnas tillhörande blad med användardata.
För användare i farozonen går du igenom användarens inloggningshistorik och återställer lösenordet om det behövs.

![Riskfyllda inloggningar](./media/active-directory-reporting-security-user-at-risk/46.png)

## <a name="azure-active-directory-premium-editions"></a>Azure Active Directory Premium-versioner

Rapporten om användare i farozonen i Azure Active Directory Premium-versionerna innehåller följande:

- En [lista över användarkonton](active-directory-identityprotection.md#users-flagged-for-risk) som kan ha drabbats 

- Sammanställd information om de [riskhändelsetyper](active-directory-identity-protection-risk-events.md) som har identifierats

- Ett alternativ för att ladda ned rapporten

- Ett alternativ för att konfigurera en [princip för att åtgärda användarrisker](active-directory-identityprotection.md#user-risk-security-policy)  


![Riskfyllda inloggningar](./media/active-directory-reporting-security-user-at-risk/71.png)

När du väljer en användare får du en detaljerad rapportvy för den här användaren som du kan använda för att göra följande:

- Öppna vyn Alla inloggningar

- Återställ användarens lösenord

- Ignorera alla händelser

- Undersök rapporterade riskhändelser för användaren. 


![Riskfyllda inloggningar](./media/active-directory-reporting-security-user-at-risk/324.png)


Välj en riskhändelse i listan om du vill undersöka den.  
Detta öppnar bladet **Information** för den här riskhändelsen. På bladet **Information** har du möjlighet att antingen [stänga en riskhändelse manuellt](active-directory-identityprotection.md#closing-risk-events-manually) eller återaktivera en manuellt stängd riskhändelse. 


![Riskfyllda inloggningar](./media/active-directory-reporting-security-user-at-risk/325.png)



## <a name="next-steps"></a>Nästa steg

- Mer information om Azure Active Directory Identity Protection finns i [Azure Active Directory Identity Protection](active-directory-identityprotection.md).


