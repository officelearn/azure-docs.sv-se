---
title: Så här konfigurerar du principen för inloggnings risker i Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur du konfigurerar Azure AD Identity Protection inloggnings risk princip.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d00376c6689b6be773f24e8acd09c3697fb6a799
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126298"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Instruktioner: Konfigurera riskprincipen för inloggning

Azure Active Directory identifierar [risk identifierings typer](../reports-monitoring/concept-risk-events.md#risk-detection-types) i real tid och offline. Varje risk identifiering som har identifierats för en användares inloggning bidrar till ett logiskt begrepp som kallas riskfylld inloggning. En riskfylld inloggning är en indikator för ett inloggnings försök som kanske inte har utförts av en legitim ägare till ett användar konto.

## <a name="what-is-the-sign-in-risk-policy"></a>Vad är inloggnings risk principen?

Azure AD analyserar varje inloggning av en användare. Syftet med analysen är att identifiera misstänkta åtgärder som kommer tillsammans med inloggningen. Till exempel är inloggningen som görs med en anonym IP-adress eller så har inloggningen initierats från en okänd plats? I Azure AD kan de misstänkta åtgärder som systemet kan identifiera också kallas för risk identifiering. Baserat på risk identifieringar som har identifierats under en inloggning beräknar Azure AD ett värde. Värdet representerar sannolikheten (låg, medium, hög) som inloggningen inte utförs av den legitima användaren. Sannolikheten kallas **inloggnings risk nivå**.

Principen för inloggnings risker är ett automatiserat svar som du kan konfigurera för en enskild risk nivå för inloggning. I ditt svar kan du blockera åtkomst till dina resurser eller kräva en Multi-Factor Authentication-utmaning (MFA) för att få åtkomst.
   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Hur gör jag för att åtkomst till inloggnings risk principen?
   
Principen för inloggnings risker finns i avsnittet **Konfigurera** på [sidan Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Princip för inloggnings risk](./media/howto-sign-in-risk-policy/1014.png "Princip för inloggnings risk")

## <a name="policy-settings"></a>Principinställningar

När du konfigurerar inloggnings risk principen måste du ange:

- De användare och grupper som principen gäller för:

    ![Användare och grupper](./media/howto-sign-in-risk-policy/11.png)

- Den inloggnings risk nivå som utlöser principen:

    ![Inloggnings risk nivå](./media/howto-sign-in-risk-policy/12.png)

- Den typ av åtkomst du vill tillämpa när inloggnings risk nivån har uppfyllts:  

    ![Åtkomst](./media/howto-sign-in-risk-policy/13.png)

- Principens tillstånd:

    ![Tillämpa princip](./media/howto-sign-in-risk-policy/14.png)

I dialog rutan princip konfiguration får du ett alternativ för att uppskatta effekten av omkonfiguration.

![Uppskattad påverkan](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>Det här bör du känna till

Du kan konfigurera en säkerhets princip för inloggnings risk för att kräva MFA:

![Kräv MFA](./media/howto-sign-in-risk-policy/16.png)

Men av säkerhets skäl fungerar den här inställningen bara för användare som redan har registrerats för MFA. Identitets skyddet blockerar användare med ett MFA-krav om de inte är registrerade för MFA än.

Om du vill kräva MFA för riskfyllda inloggningar bör du:

1. Aktivera [registrerings principen för Multi-Factor Authentication](howto-mfa-policy.md) för berörda användare.
2. Kräv att de berörda användarna loggar in på en icke-riskfylld session för att utföra en MFA-registrering.

Genom att utföra de här stegen ser du till att Multi-Factor Authentication krävs för en riskfylld inloggning.

Principen för inloggnings risker är:

- Tillämpas på all webb läsar trafik och inloggningar med modern autentisering.
- Tillämpas inte på program som använder äldre säkerhets protokoll genom att inaktivera WS-Trust-slutpunkten på den federerade IDP, till exempel ADFS.

En översikt över den relaterade användar upplevelsen finns i:

* [Återställning av riskfyllda inloggningar](flows.md#risky-sign-in-recovery)
* [Riskfylld inloggning blockerad](flows.md#risky-sign-in-blocked)  
* [Inloggnings upplevelser med Azure AD Identity Protection](flows.md)  

## <a name="best-practices"></a>Bästa praxis

Om du väljer ett **högt** tröskelvärde minskar antalet gånger som en princip utlöses och minimerar påverkan på användare.  

Men det utesluter **små** och **medel stora** inloggningar som flaggats för risk från principen, vilket inte kan hindra en angripare från att utnyttja en komprometterad identitet.

När du anger principen

- Exkludera användare som inte kan ha Multi-Factor Authentication
- Exkludera användare i språk där det inte är praktiskt att aktivera principen (till exempel ingen åtkomst till supportavdelningen)
- Exkludera användare som sannolikt kommer att generera många falska positiva (utvecklare, säkerhets analyser)
- Använd ett **högt** tröskelvärde under den inledande principen, eller om du måste minimera utmaningarna som visas av slutanvändarna.
- Använd ett **lågt** tröskelvärde om organisationen kräver större säkerhet. Om du väljer en **låg** tröskel införs ytterligare användar inloggnings utmaningar, men ökad säkerhet.

Den rekommenderade standarden för de flesta organisationer är att konfigurera en regel för en **medels Tor** tröskel för att få en balans mellan användbarhet och säkerhet.

## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection, se [Översikt över Azure AD Identity Protection](overview.md).
