---
title: Felsöka Azure-registrering
description: Lösa ett problem när du försöker registrera dig för ett nytt konto i Microsoft Azure-portalen konto Center.
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: 57dc9c05778563bc0c5df1a8ac8895b51008b01f
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657078"
---
# <a name="troubleshoot-azure-sign-up"></a>Felsöka Azure-registrering

Ett problem kan uppstå när du försöker registrera dig för ett nytt konto i Microsoft Azure-portalen eller Azure konto Center. Innan du felsöker problemet ska du först kontrol lera följande:

- Den information som du har angett för din profil för Azure-kontot (inklusive e-postadress till kontakt, gatuadress och telefonnummer) är korrekt.
- Kredit korts informationen är korrekt.
- Du har inte redan en Microsoft-konto med samma information.

## <a name="resolutions"></a>Problemen

Om du vill lösa eventuella fel väljer du det problem som du stöter på när du försöker registrera dig för Azure.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Fel: *Det går inte att fortsätta med registreringen på grund av ett problem med ditt konto. Kontakta fakturerings supporten.*

Följ dessa steg för att lösa problemet:

1. Logga in på [Azure konto Center](https://account.azure.com/Profile) med hjälp av administratörs behörighet för kontot.

2. Välj **Redigera information**.

3. Kontrol lera att alla adress fält är slutförda och giltiga.

4. När du registrerar dig för Azure-prenumerationen ska du kontrol lera att fakturerings adressen för kredit korts registreringen matchar dina bank poster.

Om du fortsätter att få fel meddelandet försöker du registrera dig med hjälp av en annan webbläsare.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>Förlopps indikatorn låser sig i avsnittet om *identitets verifiering per kort* .

Om du vill slutföra identitets verifieringen med ett kort måste cookies från tredje part tillåtas för din webbläsare.

![Identitetsverifiering via kort](./media/billing-troubleshoot-azure-sign-up/identify-verification-by-card.png)
 
Använd följande steg för att uppdatera webbläsarens cookie-inställningar.

1. Om du använder Chrome väljer du **Inställningar** > **Visa avancerade inställningar** > **Sekretess** > **innehålls inställningar**. Rensa **blockera cookies från tredje part och plats data**.

2. Om du använder Microsoft Edge väljer du **Inställningar** > **Visa avancerade inställningar** > **cookies** > **Blockera inte cookies**.

3. Uppdatera sidan för Azure-registrering och kontrol lera om problemet är löst.

4. Om uppdateringen inte löste problemet avslutar du och startar om webbläsaren och försöker sedan igen.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Kredit korts formuläret stöder inte min fakturerings adress

Din fakturerings adress måste finnas i det land som du väljer i avsnittet **om** . Kontrol lera att du väljer rätt land.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Inga textmeddelanden eller anrop vid verifiering av registrerings kontot

Även om processen normalt är snabb, kan det ta upp till fyra minuter innan en verifierings kod levereras. Det telefonnummer som du anger för verifiering lagras inte som ett kontakt nummer för kontot.

Här följer några ytterligare tips:

- Det går inte att använda ett VoiP-telefonnummer (Voice-över-IP) för telefon verifierings processen.
- Dubbelklicka på det telefonnummer som du anger, inklusive lands koden som du väljer i den nedrullningsbara menyn.
- Om din telefon inte tar emot textmeddelanden (SMS) kan du prova alternativet **Ring mig** .
- Kontrol lera att din telefon kan ta emot samtal eller SMS-meddelanden från ett USA-baserat telefonnummer.

När du får textmeddelandet eller telefonsamtalet anger du den kod som du får i text rutan.

### <a name="credit-card-declined-or-not-accepted"></a>Kredit kort har avböjts eller accepterats inte

Virtuella eller förbetalda kredit-eller betalkort godkänns inte som betalning för Azure-prenumerationer. Om du vill se vad mer kan orsaka att kortet nekas, kan du läsa ditt betalkort eller kredit kortet [nekas vid Azure-registrering](https://support.microsoft.com/help/4042960).

### <a name="free-trial-is-not-available"></a>Den kostnads fria utvärderings versionen är inte tillgänglig

Har du använt en Azure-prenumeration tidigare? Azures användningsvillkor begränsar aktivering av kostnadsfri utvärderingsversion endast för en användare som är nybörjare på Azure. Om du har haft någon annan typ av Azure-prenumeration kan du inte aktivera en kostnadsfri utvärderingsversion. Överväg att registrera dig för en [prenumeration där du betalar per](https://azure.microsoft.com/offers/ms-azr-0003p/)användning.

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Jag såg en avgift på mitt kostnads fria utvärderings konto

Du kan se att en liten verifiering är kvar på ditt kredit korts konto efter registreringen. Detta tas bort inom tre till fem dagar. Om du oroar dig för att hantera kostnader kan du läsa mer om [att förhindra oväntade kostnader](billing-getting-started.md).

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Det går inte att aktivera Azures förmåns plan som MSDN, BizSpark, BizSparkPlus eller MPN

Kontrol lera att du använder rätt inloggnings uppgifter. Kontrol lera sedan förmåns programmet för att se till att du är berättigad.

- MSDN 
  - Kontrol lera status för din status på din [MSDN-konto sida](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Om du inte kan verifiera din status kontaktar du [MSDN-prenumerationens kund Service Center](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft for Startups
  - Logga in på [Microsoft for startups-portalen](https://startups.microsoft.com/#start-two) för att kontrol lera status för Microsoft för starter.
  - Om du inte kan verifiera din status kan du få hjälp i [forumen Microsoft for startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN 
  - Logga in på [MPN-portalen](https://mspartner.microsoft.com/Pages/Locale.aspx) för att verifiera din status. Om du har rätt [moln plattforms kompetens](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)kan du vara berättigad till ytterligare förmåner.
  - Kontakta [MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx)-supporten om du inte kan verifiera din status.

### <a name="cant-activate-new-azure-in-open-subscription"></a>Det går inte att aktivera ny Azure i Open-prenumeration

Om du vill skapa en Azure i Open-prenumeration måste du ha en giltig nyckel för aktivering av online tjänst (OSA) som har minst en Azure i Open-token kopplad till sig. Om du inte har en OSA-nyckel kontaktar du en av de Microsoft-partner som finns i [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Fel: *Du är inte berättigad till en Azure-prenumeration*

Lös problemet genom att kontrol lera om följande objekt är sanna:

- Den information som du har angett för din profil för Azure-kontot (inklusive e-postadress till kontakt, gatuadress och telefonnummer) är korrekt.
- Kredit korts informationen är korrekt.
- Du har inte redan en Microsoft-konto som använder samma information.

### <a name="error-your-current-account-type-is-not-supported"></a>Fel: *Den aktuella konto typen stöds inte*

Det här problemet kan uppstå om kontot är registrerat i en [ohanterad Azure AD-katalog](../active-directory/users-groups-roles/directory-self-service-signup.md)och inte finns i din organisations Azure AD-katalog. 

Lös problemet genom att registrera Azure-kontot med ett annat konto eller ta över den ohanterade AD-katalogen. Mer information finns i [ta över en ohanterad katalog som administratör i Azure Active Directory](../active-directory/users-groups-roles/domains-admin-takeover.md).
 
## <a name="additional-help-resources"></a>Ytterligare hjälp resurser

Andra fel söknings artiklar för Azure-fakturering och-prenumerationer

- [Nekat kort](billing-troubleshoot-declined-card.md)
- [Problem med prenumerations inloggning](billing-troubleshoot-sign-in-issue.md)
- [Inga prenumerationer hittades](billing-no-subscriptions-found.md)
- [Företagskostnadsvy inaktiverad](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontakta oss om du behöver hjälp

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure-fakturering](index.md)
