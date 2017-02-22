---
title: "Säkerhetsrapportering i Basic-versionen och den kostnadsfria versionen av Azure Active Directory – förhandsversion | Microsoft Docs"
description: "Visar de olika tillgängliga rapporterna i Azure Active Directory-förhandsgranskning"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/19/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: fa5a6dab1e76d62956cbfdd9b8b0f64c014696db
ms.openlocfilehash: c1a3953c79cfac9d6f55b38971ee2b79ff4244eb


---
# <a name="security-reporting-in-the-azure-active-directory-free-and-basic-edition---preview"></a>Säkerhetsrapportering i Basic-versionen och den kostnadsfria versionen av Azure Active Directory – förhandsversion

Med hjälp av säkerhetsrapporterna i [förhandsversionen](active-directory-preview-explainer.md) av Azure Active Directory kan du bedöma risken för att användarkonton i din miljö har komprometterats. 

Azure Active Directory identifierar misstänkta åtgärder relaterade till dina användarkonton. För varje identifierad åtgärd skapas en post med namnet *riskhändelse*. Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](active-directory-identity-protection-risk-events.md). 

De identifierade riskhändelserna används för att beräkna:

- **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. Mer information finns i avsnittet om [riskfyllda inloggningar](active-directory-identityprotection.md#risky-sign-ins). 

- **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. Mer information finns i avsnittet om [användare som har flaggats för risk](active-directory-identityprotection.md#users-flagged-for-risk).  


## <a name="risky-sign-ins-report"></a>Rapport över riskfyllda inloggningar

Basic-versionen och den kostnadsfria versionen av Azure Active Directory tillhandahåller en lista över riskfyllda inloggningar som har identifierats för dina användare. Rapporten över riskhändelser innehåller följande information:

- **Användare** –Namnet på användaren som användes vid inloggningen.
- **IP-adress** – IP-adressen för enheten som användes för att ansluta till Azure Active Directory.
- **Plats** – Platsen som används för att ansluta till Azure Active Directory.
- **Inloggningstid** – Tidpunkten för inloggningen.
- **Status** – Inloggningens status.

Rapporten tillhandahåller ett alternativ för att hämta rapportdata.

![Rapportering](./media/active-directory-reporting-security-azure-portal-free-basic/01.png)

Baserat på din undersökning av den riskfyllda inloggningen kan du lämna feedback till Azure Active Directory genom någon av följande åtgärder:

- Lös
- Markera som falskt positivt resultat
- Ignorera
- Återaktivera

![Rapportering](./media/active-directory-reporting-security-azure-portal-free-basic/21.png)

Mer information finns i [Stänga riskhändelser manuellt](active-directory-identityprotection.md#closing-risk-events-manually).


## <a name="users-at-risk-report"></a>Användare i farozonen

Den kostnadsfria versionen av Azure Active Directory tillhandahåller en lista över användarkonton som kan ha komprometterats. 


![Rapportering](./media/active-directory-reporting-security-azure-portal-free-basic/03.png)

Om du klickar på en användare i listan öppnas tillhörande blad med användardata.
För användare i farozonen går du igenom användarens inloggningshistorik och återställer lösenordet om det behövs.

![Rapportering](./media/active-directory-reporting-security-azure-portal-free-basic/46.png)



## <a name="next-steps"></a>Nästa steg

- Mer information om Azure Active Directory-rapportering finns i [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).
- Mer information om Azure Active Directory Identity Protection finns i [Azure Active Directory Identity Protection](active-directory-identityprotection.md).




<!--HONumber=Jan17_HO3-->


