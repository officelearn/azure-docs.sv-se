---
title: Rapporten om riskfyllda inloggningar i Azure Active Directory-portalen | Microsoft Docs
description: "Lär dig mer om rapporten över riskfyllda inloggningar i Azure Active Directory-portalen"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: f31482a8b37858b93391afc561223a1b1110a1a9
ms.contentlocale: sv-se
ms.lasthandoff: 08/16/2017

---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Rapporten över riskfyllda inloggningar i Azure Active Directory-portalen

Med hjälp av säkerhetsrapporterna i Azure Active Directory (Azure AD) kan du bedöma risken för att användarkonton i din miljö har komprometterats. 

Azure AD identifierar misstänkta åtgärder relaterade till dina användarkonton. För varje identifierad åtgärd skapas en post med namnet *riskhändelse*. Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](active-directory-identity-protection-risk-events.md). 

De identifierade riskhändelserna används för att beräkna:

- **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. Mer information finns i avsnittet om [riskfyllda inloggningar](active-directory-identityprotection.md#risky-sign-ins). 

- **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. Mer information finns i avsnittet om [användare som har flaggats för risk](active-directory-identityprotection.md#users-flagged-for-risk).  

I [Azure Portal](https://portal.azure.com) hittar du säkerhetsrapporter på bladet **Azure Active Directory** i avsnittet **Säkerhet**. 

![Riskfyllda inloggningar](./media/active-directory-reporting-security-risky-sign-ins/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Vilken Azure AD-licens behöver du för att komma åt en säkerhetsrapport?  

Alla utgåvor av Azure Active Directory ger rapporter över riskfyllda inloggningar.  
Nivån av rapportens detaljrikedom varierar dock mellan versionerna: 

- I **versionerna Azure Active Directory Free och Basic** finns det redan en lista över riskfyllda inloggningar. 

- Utgåvan **Azure Active Directory Premium 1** har en utökad modell där du även kan utforska några av de underliggande riskhändelser som har identifierats för varje rapport. 

- Utgåvan **Azure Active Directory Premium 2** ger den mest detaljerade informationen om alla underliggande riskhändelser och du kan konfigurera säkerhetsprinciper som automatiskt svarar på konfigurerade risknivåer.



## <a name="azure-active-directory-free-and-basic-edition"></a>Azure Active Directory kostnadsfri och grundläggande utgåva

Den grundläggande och den kostnadsfria versionen av Azure Active Directory tillhandahåller en lista över riskfyllda inloggningar som har identifierats för dina användare. Rapporten över riskhändelser innehåller följande information:

- **Användare** – Namnet på användaren som användes vid inloggningen.
- **IP-adress** – IP-adressen för enheten som användes för att ansluta till Azure Active Directory.
- **Plats** – Platsen som används för att ansluta till Azure Active Directory.
- **Inloggningstid** – Tidpunkten för inloggningen.
- **Status** – Inloggningens status.

Rapporten tillhandahåller ett alternativ för att hämta rapportdata.

![Riskfyllda inloggningar](./media/active-directory-reporting-security-risky-sign-ins/01.png)

Baserat på din undersökning av den riskfyllda inloggningen kan du lämna feedback till Azure Active Directory genom någon av följande åtgärder:

- Lös
- Markera som falskt positivt resultat
- Ignorera
- Återaktivera

![Riskfyllda inloggningar](./media/active-directory-reporting-security-risky-sign-ins/21.png)

Mer information finns i [Stänga riskhändelser manuellt](active-directory-identityprotection.md#closing-risk-events-manually).

## <a name="azure-active-directory-premium-editions"></a>Azure Active Directory Premium-versioner

Rapporten om riskfyllda inloggningar i Azure Active Directory Premium-versionerna ger dig följande:

- Sammanställd information om de [riskhändelsetyper](active-directory-identity-protection-risk-events.md) som har identifierats

- Ett alternativ för att ladda ned rapporten


![Riskfyllda inloggningar](./media/active-directory-reporting-security-risky-sign-ins/456.png)


När du väljer en riskhändelse kan få en detaljerad rapportvy för den här riskhändelsen som gör att du kan göra följande:

- Ett alternativ för att konfigurera en [princip för att åtgärda användarrisker](active-directory-identityprotection.md#user-risk-security-policy)  

- Granska identifieringstidslinjen för riskhändelsen  

- Granska en lista över användare för vilka den här riskhändelsen har upptäckts

- [Stänga riskhändelser manuellt](active-directory-identityprotection.md#closing-risk-events-manually) eller återaktivera en manuellt stängd riskhändelse. 


![Riskfyllda inloggningar](./media/active-directory-reporting-security-risky-sign-ins/457.png)

När du väljer en användare får du en detaljerad rapportvy för den här användaren som du kan använda för att göra följande:

- Öppna vyn Alla inloggningar

- Återställ användarens lösenord

- Ignorera alla händelser

- Undersök rapporterade riskhändelser för användaren. 


![Riskfyllda inloggningar](./media/active-directory-reporting-security-risky-sign-ins/324.png)


Välj en riskhändelse i listan om du vill undersöka den.  
Detta öppnar bladet **Information** för den här riskhändelsen. På bladet **Information** har du möjlighet att antingen [stänga en riskhändelse manuellt](active-directory-identityprotection.md#closing-risk-events-manually) eller återaktivera en manuellt stängd riskhändelse. 


![Riskfyllda inloggningar](./media/active-directory-reporting-security-risky-sign-ins/325.png)





## <a name="next-steps"></a>Nästa steg

- Mer information om Azure Active Directory Identity Protection finns i [Azure Active Directory Identity Protection](active-directory-identityprotection.md).


