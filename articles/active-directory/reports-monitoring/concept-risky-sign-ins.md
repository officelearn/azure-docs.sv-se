---
title: Rapporten om riskfyllda inloggningar i Azure Active Directory-portalen | Microsoft Docs
description: Lär dig mer om rapporten över riskfyllda inloggningar i Azure Active Directory-portalen
services: active-directory
author: priyamohanram
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 402aae15ae4fe8e6961fbc76a4ddaa470b8c797a
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54807770"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Rapporten över riskfyllda inloggningar i Azure Active Directory-portalen

Azure Active Directory (Azure AD) identifierar misstänkta åtgärder relaterade till dina användarkonton. För varje identifierad åtgärd skapas en post med namnet **riskhändelse**. Mer information finns i [Azure AD-riskhändelser](concept-risk-events.md). 

Du kan komma åt säkerhetsrapporterna via [Azure-portalen](https://portal.azure.com) genom att välja bladet **Azure Active Directory** och sedan navigera till avsnittet **Säkerhet**. 

Det finns två olika säkerhetsrapporter som beräknas utifrån riskhändelser:

- **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

- **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. 

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/10.png)

Information om hur du konfigurerar principer som utlöser dessa riskhändelser finns i [Konfigurera riskprincipen för användare](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Vem som kan komma åt rapporten över riskfyllda inloggningar?

Riskfyllda inloggningar rapporter är tillgängliga för användare i följande roller:

- Säkerhetsadministratör
- Global administratör
- Säkerhetsläsare

Om du vill lära dig mer om att tilldela administrativa roller till en användare i Azure Active Directory, se [visa och tilldela administratörsroller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Vilken Azure AD-licens behöver du för att komma åt en säkerhetsrapport?  

Alla utgåvor av Azure AD ger rapporter över riskfyllda inloggningar. Nivån av rapportens detaljrikedom varierar dock mellan versionerna: 

- I den **versionerna Azure Active Directory Free och Basic**, visas en lista över riskfyllda inloggningar. 

- Dessutom kan du med utgåvan **Azure Active Directory Premium 1** utforska några av de underliggande riskhändelser som har identifierats för varje rapport. 

- Utgåvan **Azure Active Directory Premium 2** ger den mest detaljerade informationen om alla underliggande riskhändelser och du kan konfigurera säkerhetsprinciper som automatiskt svarar på konfigurerade risknivåer.

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Rapporten om riskfyllda inloggningar för Azure AD kostnadsfri och basic-versionen

De kostnadsfria och grundläggande versionerna av Azure AD ger dig en lista över riskfyllda inloggningar som har identifierats för dina användare. Varje post innehåller följande attribut:

- **Användaren** -namnet på den användare som har använts under åtgärden logga in.
- **IP** -IP-adressen för den enhet som användes för att ansluta till Azure Active Directory.
- **Plats** -plats som används för att ansluta till Azure Active Directory. Det här är en bra arbete uppskattning baserat på spårningar, registerdata, omvänd sökningar och annan information.
- **Inloggningstid** – Tidpunkten för inloggningen.
- **Status** – Inloggningens status.

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/01.png)

Baserat på din undersökning av den riskfyllda inloggningen kan ge du feedback till Azure AD genom att utföra följande åtgärder:

- Lös
- Markera som falskt positivt resultat
- Ignorera
- Återaktivera

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/21.png)

Den här rapporten innehåller också ett alternativ för att:

- Sök efter resurser
- Ladda ned rapportdata

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Rapporten om riskfyllda inloggningar för Azure AD premium-versioner

Rapporten över riskfyllda inloggningar i Azure AD premium-versionerna ger dig:

- Sammanställd information om den [riskhändelsetyper](concept-risk-events.md) som har identifierats. Med den **Azure AD Premium P1 edition**, identifieringar som inte omfattas av din licens visas som riskhändelsen **inloggning med identifierad ytterligare risk**. Med den **Azure AD Premium P2-versionen**, du får den mest detaljerade informationen om alla underliggande identifieringar.

- Ett alternativ för att ladda ned rapporten

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/456.png)

När du väljer en riskhändelse kan få en detaljerad rapportvy för den här riskhändelsen som gör att du kan göra följande:

- Ett alternativ för att konfigurera en [princip för att åtgärda användarrisker](../identity-protection/howto-user-risk-policy.md)  

- Granska identifieringstidslinjen för riskhändelsen  

- Granska en lista över användare för vilka den här riskhändelsen har upptäckts

- Stänga riskhändelser manuellt. 

![Riskfyllda inloggningar](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Ibland, kan du hitta en riskhändelse utan en motsvarande inloggning transaktion i den [rapporten inloggningar](concept-sign-ins.md). Detta beror på att Identity Protection utvärderar risken för både **interaktiva** och **icke-interaktiv** inloggningar, medan den inloggningar rapporten visas endast de interaktiva inloggningarna.

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
