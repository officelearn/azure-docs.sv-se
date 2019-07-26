---
title: Konfigurera risk principer i Azure Active Directory Identity Protection (uppdateras) | Microsoft Docs
description: Konfigurera risk principer i Azure Active Directory Identity Protection (uppdateras).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ce4e2958978de9339f4340755e3740730025a5f
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334025"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>Instruktioner: Konfigurera risk principer i Azure Active Directory identitets skydd (uppdateras)

Azure AD identifierar risk händelser som är indikatorer för potentiellt komprometterade identiteter. Genom att konfigurera risk principer kan du definiera automatiserade svar på identifierings resultaten:

- Med inloggnings risk principen kan du konfigurera ett svar på händelser i real tid som upptäcktes under en användares inloggning. 
- Med principen för användar risk kan du konfigurera ett svar på alla aktiva användar risker som har identifierats för en användare över tid.  

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="what-is-the-sign-in-risk-policy"></a>Vad är inloggnings risk principen?

Azure AD analyserar varje inloggning av en användare. Syftet med analysen är att identifiera misstänkta åtgärder som kommer tillsammans med inloggningen. Till exempel är inloggningen som görs med en anonym IP-adress eller så har inloggningen initierats från en okänd plats? I Azure AD kan de misstänkta åtgärder som systemet kan identifiera kallas även risk händelser. Baserat på risk händelser som har identifierats under en inloggning beräknar Azure AD ett värde. Värdet representerar sannolikheten (låg, medium, hög) som inloggningen inte utförs av den legitima användaren. Sannolikheten kallas **inloggnings risk nivå**.

Principen för inloggnings risker är ett automatiserat svar som du kan konfigurera för en enskild risk nivå för inloggning. I ditt svar kan du blockera åtkomst till dina resurser eller kräva en Multi-Factor Authentication-utmaning (MFA) för att få åtkomst.

När en användare har slutfört en MFA-prompt som utlöses av inloggnings risk principen, ger den feedback till identitets skydd som inloggningen har fått från den legitima användaren. Därmed stängs den inloggnings risk händelse som utlöste MFA-prompten automatiskt och identitets skyddet förhindrar att den här händelsen bidrar till utökningen av användar risken. Genom att aktivera principen för inloggnings risker kan du minska noisiness i vyn riskfyllda inloggningar genom att tillåta att användare själv åtgärdas när de uppmanas att använda MFA och sedan stänga av den associerade riskfyllda inloggningen automatiskt.

## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Hur gör jag för att åtkomst till inloggnings risk principen?
   
Principen för inloggnings risker finns i avsnittet **Konfigurera** på [sidan Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Princip för inloggnings risk](./media/howto-configure-risk-policies/1014.png "Princip för inloggnings risk")

## <a name="sign-in-risk-policy-settings"></a>Princip inställningar för inloggnings risk

När du konfigurerar inloggnings risk principen måste du ange:

- De användare och grupper som principen gäller för:

   ![Användare och grupper](./media/howto-configure-risk-policies/11.png)

- Den inloggnings risk nivå som utlöser principen:

   ![Risknivå för inloggning](./media/howto-configure-risk-policies/12.png)

- Den typ av åtkomst du vill tillämpa när inloggnings risk nivån har uppfyllts:  

   ![Åtkomst](./media/howto-configure-risk-policies/13.png)

- Principens tillstånd:

   ![Tillämpa princip](./media/howto-configure-risk-policies/14.png)

I dialog rutan princip konfiguration får du ett alternativ för att uppskatta effekten av omkonfiguration.

![Uppskattad påverkan](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>Vad du bör veta om inloggnings risk principer

Du kan konfigurera en säkerhets princip för inloggnings risk för att kräva MFA:

![Kräv MFA](./media/howto-configure-risk-policies/16.png)

Men av säkerhets skäl fungerar den här inställningen bara för användare som redan har registrerats för MFA. Identitets skyddet blockerar användare med ett MFA-krav om de inte är registrerade för MFA än.

Om du vill kräva MFA för riskfyllda inloggningar bör du:

1. Aktivera registrerings principen för Multi-Factor Authentication för berörda användare.
2. Kräv att de berörda användarna loggar in i en icke-riskfylld session för att utföra en MFA-registrering.

Genom att utföra de här stegen ser du till att Multi-Factor Authentication krävs för en riskfylld inloggning.

Principen för inloggnings risker är:

- Tillämpas på all webb läsar trafik och inloggningar med modern autentisering.
- Tillämpas inte på program som använder äldre säkerhets protokoll genom att inaktivera WS-Trust-slutpunkten på den federerade IDP, till exempel ADFS.

En översikt över den relaterade användar upplevelsen finns i:

* [Återställning av riskfyllda inloggningar](flows.md#risky-sign-in-recovery)
* [Riskfylld inloggning blockerad](flows.md#risky-sign-in-blocked)  
* [Inloggnings upplevelser med Azure AD Identity Protection](flows.md)  

## <a name="what-is-a-user-risk-policy"></a>Vad är en användar risk princip?

Azure AD analyserar varje inloggning av en användare. Syftet med analysen är att identifiera misstänkta åtgärder som kommer tillsammans med inloggningen. I Azure AD kan de misstänkta åtgärder som systemet kan identifiera kallas även risk händelser. Vissa risk händelser kan upptäckas i real tid, men det finns också risk händelser som kräver mer tid. Om du till exempel vill upptäcka en omöjlig resa till ovanlig-platser, kräver systemet en inledande inlärnings period på 14 dagar för att lära dig om en användares normala beteende. Det finns flera alternativ för att lösa identifierade risk händelser. Du kan till exempel lösa enskilda risk händelser manuellt eller så kan du få dem att lösas med hjälp av en inloggnings risk eller en användar risk princip för villkorlig åtkomst.

Alla risk händelser som har identifierats för en användare och inte lösts kallas aktiva risk händelser. De aktiva risk händelser som är associerade med en användare kallas användar risk. Utifrån användar risken beräknar Azure AD en sannolikhet (låg, medel, hög) som en användare har komprometterat. Sannolikheten kallas användar risk nivå.

![Användar risker](./media/howto-configure-risk-policies/11031.png)

Användar risk principen är ett automatiserat svar som du kan konfigurera för en enskild risk nivå för användare. Med en användar risk princip kan du blockera åtkomst till dina resurser eller kräva en lösen ords ändring för att få ett användar konto tillbaka till ett rent tillstånd.

## <a name="how-do-i-access-the-user-risk-policy"></a>Hur gör jag för att åtkomst till användar risk principen?
   
Användar risk principen finns i avsnittet **Konfigurera** på [sidan Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Riskprincip för användare](./media/howto-configure-risk-policies/11014.png)

## <a name="user-risk-policy-settings"></a>Princip inställningar för användar risk

När du konfigurerar användar risk principen måste du ange:

- De användare och grupper som principen gäller för:

   ![Användare och grupper](./media/howto-configure-risk-policies/111.png)

- Den inloggnings risk nivå som utlöser principen:

   ![Användarisknivå](./media/howto-configure-risk-policies/112.png)

- Den typ av åtkomst du vill tillämpa när inloggnings risk nivån har uppfyllts:  

   ![Åtkomst](./media/howto-configure-risk-policies/113.png)

- Principens tillstånd:

   ![Tillämpa princip](./media/howto-configure-risk-policies/114.png)

I dialog rutan princip konfiguration får du ett alternativ för att uppskatta effekten av konfigurationen.

![Uppskattad påverkan](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>Vad du bör känna till om användar risk principer

Du kan ange en säkerhets princip för användar risk för att blockera användare vid inloggning, beroende på risk nivån.

![Blockerar](./media/howto-configure-risk-policies/116.png)

Blockerar en inloggning:

* Förhindrar generering av nya användar risk händelser för den berörda användaren
* Gör det möjligt för administratörer att manuellt reparera risk händelser som påverkar användarens identitet och återställa den till ett säkert tillstånd

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

 [Kanal 9: Azure AD och identitet visar: För hands version av identitets skydd](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
