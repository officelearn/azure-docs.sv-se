---
title: Riskbaserad MFA och SSPR med Azure Identity Protection
description: I den här självstudien aktiverar du Azure Identity Protection-integreringar för multifaktorautentisering och självåterställning av lösenord i syfte att minska riskfyllt beteende.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: df3344efadbc915bba0c863979cae8b8fdff99b0
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512133"
---
# <a name="tutorial-use-risk-events-to-trigger-multi-factor-authentication-and-password-changes"></a>Självstudier: Använda riskhändelser för att utlösa multifaktorautentisering och lösenordsändringar

I den här självstudien aktiverar du funktionerna för Azure Active Directory (Azure AD) Identity Protection, en Azure AD Premium P2-funktion som är mer än bara ett övervaknings- och rapporteringsverktyg. För att skydda organisationens identiteter kan du konfigurera riskbaserade principer som automatiskt svarar på riskfyllda beteenden. Dessa principer kan antingen automatiskt blockera eller initiera reparationer, inklusive att kräva lösenordsändring och framtvinga multifaktorautentisering.

Azure AD Identity Protection-principer kan användas utöver befintliga principer för villkorlig åtkomst som ett extra skyddslager. Det kan hända att användarna aldrig utlöser en riskfyllt beteende som kräver någon av dessa principer, men som administratör vet du att de ändå är skyddade.

Vissa objekt som kan utlösa en riskhändelse är:

* Användare med läckta autentiseringsuppgifter
* Inloggningar från anonyma IP-adresser
* Omöjliga resor till ovanliga platser
* Inloggningar från angripna enheter
* Inloggningar från IP-adresser med misstänkt aktivitet
* Inloggningar från okända platser

Mer information om Azure AD Identity Protection finns i artikeln [Vad är Azure AD Identity Protection](../active-directory-identityprotection.md)

> [!div class="checklist"]
> * Aktivera Azure MFA-registrering
> * Aktivera riskbaserade lösenordsändringar
> * Aktivera riskbaserad multifaktorautentisering

## <a name="prerequisites"></a>Nödvändiga komponenter

* Åtkomst till en aktiv Azure AD-klientorganisation med minst en utvärderingslicens för Azure AD Premium P2 som tilldelats.
* Ett konto med behörigheter som global administratör i Azure AD-klientorganisationen.
* Har slutfört föregående självstudier om självåterställning av lösenord (SSPR) och multifaktorautentisering (MFA).

## <a name="enable-risk-based-policies-for-sspr-and-mfa"></a>Aktivera riskbaserade principer för SSPR och MFA

Att aktivera riskbaserade principer är en enkel process. Stegen nedan vägleder dig genom en exempelkonfiguration.

### <a name="enable-users-to-register-for-multi-factor-authentication"></a>Aktivera användare för registrering för multifaktorautentisering

Azure AD Identity Protection innehåller en standardprincip som kan hjälpa dig att registrera användarna för multifaktorautentisering och enkelt identifiera den aktuella registreringsstatusen. När den här principen aktiveras kräver den inte att användare utför multifaktorautentisering, men den ber dem att förhandsregistrera sig.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **Alla tjänster** och bläddra sedan till **Azure AD Identity Protection**.
1. Klicka på **MFA-registrering**.
1. Ange Tillämpa princip till **På**.
   1. Om du anger den här principen måste alla användare registrera metoder för att förbereda användningen av multifaktorautentisering.
1. Klicka på **Spara**.

   ![Kräv att användare registrerar sig för MFA vid inloggning med Azure AD Identity Protection](./media/tutorial-risk-based-sspr-mfa/risk-based-require-mfa-registration.png)

### <a name="enable-risk-based-password-changes"></a>Aktivera riskbaserade lösenordsändringar

Microsoft samarbetar med forskare, polis och rättsväsende, flera säkerhetsteam på Microsoft och andra betrodda källor för att hitta par av användarnamn och lösenord. När något av dessa par matchar ett konto i din miljö kan en riskbaserad lösenordsändring utlösas med hjälp av följande princip.

1. Klicka på Riskprincip för användare.
1. Under **Villkor** väljer du **Användarrisk**, och välj sedan **Medel och över**.
1. Klicka på ”Välj” och sedan ”Klar”
1. Under **Åtkomst** väljer du **Tillåt åtkomst** och väljer sedan **Kräv lösenordsändring**.
1. Klicka på ”Välj”
1. Ange Tillämpa princip till **På**.
1. Klicka på **Spara**

### <a name="enable-risk-based-multi-factor-authentication"></a>Aktivera riskbaserad multifaktorautentisering

De flesta användare har ett normalt beteende som kan spåras. När de hamnar utanför den här normen kan det vara riskabelt att tillåta att de bara loggar in. Du vill kanske blockera den användaren eller be användaren att utföra en multifaktorautentisering för att bevisa att han eller hon verkligen är rätt person. Om du vill aktivera en princip som kräver MFA när en riskfylld inloggning har identifierats aktiverar du följande princip.

1. Klicka på Princip för inloggningsrisk
1. Under **Villkor** väljer du **Användarrisk**, och välj sedan **Medel och över**.
1. Klicka på ”Välj” och sedan ”Klar”
1. Under **Åtkomst** väljer du **Tillåt åtkomst** och väljer sedan **Kräv multifaktorautentisering**.
1. Klicka på ”Välj”
1. Ange Tillämpa princip till **På**.
1. Klicka på **Spara**

## <a name="clean-up-resources"></a>Rensa resurser

Om du har slutfört testningen och inte längre vill att de riskbaserade principerna ska vara aktiverade går du tillbaka till varje princip som du vill inaktivera och ställer in **Tillämpa princip** på **Av**.
