---
title: Felsöka problem när du registrerar ett nytt konto i Microsoft Azure-portalen eller Azure-kontocenter
description: Lösa ett problem vid försök att registrera ett nytt konto i Microsoft Azure-portalen eller kontocentret.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: 7bcf3197ae218c469983200814cecf551dc42a6b
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133169"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>Felsöka problem när du registrerar ett nytt konto i Microsoft Azure-portalen eller Azure-kontocenter

Ett problem kan uppstå när du försöker registrera ett nytt konto i Microsoft Azure-portalen eller Azure-kontocenter. Innan du felsöker problemet bör du först kontrollera följande:

- De uppgifter du angav för din Azure-kontoprofil (däribland e-postadress, gatuadress och telefonnummer för kontakt) är korrekta.
- Kreditkortsuppgifterna stämmer.
- Du har inte redan ett Microsoft-konto där du använder samma uppgifter.

> [!NOTE]
> Om du redan har ett befintligt konto och behöver hjälp med att felsöka inloggningsproblem kan du läsa [Felsöka problem med inloggning för Azure-prenumeration](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue).

## <a name="resolutions"></a>Lösningar

Lös eventuella problem genom att välja det problem som uppstår för dig när du försöker registrera dig för Azure.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Fel: *Det går inte att fortsätta med registreringen på grund av ett problem med ditt konto. Kontakta faktureringssupporten.*

Följ dessa anvisningar för att lösa problemet:

1. Logga in på [Azure-kontocenter](https://account.azure.com/Profile) med hjälp av kontoadministratörens autentiseringsuppgifter.

2. Välj **Redigera information**.

3. Kontrollera att alla adressfält är ifyllda och giltiga.

4. När du registrerar dig för Azure-prenumerationen är det viktigt att du kontrollerar att faktureringsadressen för registreringen av ditt kreditkort stämmer överens med dina bankuppgifter.

Om du fortsätter att få felmeddelandet kan du försöka att registrera dig via en annan webbläsare.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>Förloppsindikatorn låser sig i avsnittet *Identitetsverifiering via kort*.

För att du ska kunna slutföra identitetsverifieringen via kort måste cookies från tredje part tillåtas i webbläsaren.

![Identitetsverifiering via kort](./media/troubleshoot-azure-sign-up/identify-verification-by-card.png)

Använd följande steg för att uppdatera webbläsarens inställningar för cookies.

1. Om du använder Chrome väljer du **Inställningar** > **Visa avancerade inställningar** > **Sekretess** > **Innehållsinställningar**. Avmarkera **Blockera cookies från tredje part och platsdata**.

2. Om du använder Microsoft Edge väljer du **Inställningar** > **Visa avancerade inställningar** > **Cookies** > **Blockera inte cookies**.

3. Uppdatera sidan för Azure-registrering och kontrollera om problemet är löst.

4. Om uppdateringen inte löste problemet avslutar du och startar om webbläsaren, och försöker sedan igen.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Kreditkortsformuläret stöder inte min faktureringsadress

Din faktureringsadress måste finnas i det land eller den region som du väljer i avsnittet **Om dig**. Kontrollera att du väljer rätt land eller region.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Inga textmeddelanden eller samtal vid kontoverifiering under registrering

Även om processen normalt är snabb kan det ta upp till fyra minuter innan en verifieringskod levereras. Det telefonnummer som du anger för verifiering lagras inte som ett kontaktnummer för kontot.

Här följer några ytterligare tips:

- Det går inte att använda ett VoiP-telefonnummer för telefonverifieringsprocessen.
- Dubbelkontrollera det telefonnummer som du anger, inklusive den landskod som du väljer i den nedrullningsbara menyn.
- Om din telefon inte får textmeddelanden (SMS) kan du prova alternativet **Ring mig**.
- Kontrollera att din telefon kan ta emot samtal eller SMS från ett USA-baserat telefonnummer.

När du får SMS:et eller telefonsamtalet anger du den kod som du får i textrutan.

### <a name="credit-card-declined-or-not-accepted"></a>Kreditkort nekas eller accepterats inte

Virtuella eller förbetalda kredit- eller debetkort accepteras inte som betalningsmedel för Azure-prenumerationer. Om du vill se fler potentiella orsaker till att ditt kort nekas kan du se om ditt debetkort eller kreditkort [nekas vid Azure-registrering](https://support.microsoft.com/help/4042960).

### <a name="free-trial-is-not-available"></a>Kostnadsfri utvärderingsversion är inte tillgänglig

Har du använt en Azure-prenumeration tidigare? Azures användningsvillkor begränsar aktivering av kostnadsfri utvärderingsversion endast för en användare som är nybörjare på Azure. Om du har haft någon annan typ av Azure-prenumeration kan du inte aktivera en kostnadsfri utvärderingsversion. Överväg att registrera dig för en [Betala per användning-prenumeration](https://azure.microsoft.com/offers/ms-azr-0003p/).

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Jag såg en avgift på mitt kostnadsfria utvärderingskonto

Det kan finnas ett litet reservationsbelopp på ditt kreditkortskonto efter att du har registrerat dig. Detta tas bort inom tre till fem dagar. Om du har funderingar kring hantering av kostnader kan du läsa mer om att [förhindra oväntade kostnader](getting-started.md).

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Det går inte att aktivera Azure-förmånsplan såsom MSDN, BizSpark, BizSparkPlus eller MPN

Kontrollera att du använder rätt inloggningsuppgifter. Kontrollera sedan förmånsprogrammet för att se om du är berättigad.

- MSDN
  - Kontrollera din berättigandestatus på din [MSDN-kontosida](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Om du inte kan verifiera din status kontaktar du [kundtjänstcentret för MSDN-prenumerationskunder](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft for Startups
  - Logga in på [Microsoft for Startups-portalen](https://startups.microsoft.com/#start-two) för att kontrollera din berättigandestatus för Microsoft for Startups.
  - Om du inte kan verifiera statusen kan du få hjälp på [forumen för Microsoft for Startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN
  - Logga in på [MPN-portalen](https://mspartner.microsoft.com/Pages/Locale.aspx) för att verifiera din berättigandestatus. Om du har rätt [molnplattformskompetenser](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx) kan du vara berättigad till ytterligare förmåner.
  - Om du inte kan verifiera din status kontaktar du [MPN-supporten](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Det går inte att aktivera ny Azure In Open-prenumeration

För att kunna skapa en Azure In Open-prenumeration måste du ha en giltig OSA-nyckel (Online Service Activation) som har minst en Azure In Open-token associerad. Om du inte har en OSA-nyckel kontaktar du en av de Microsoft-partner som anges i [Microsoft Pinpoint](https://pinpoint.microsoft.com/).

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Fel: *Du är inte berättigad till en Azure-prenumeration*

Lös det här problemet genom att kontrollera om följande saker stämmer:

- De uppgifter du angav för din Azure-kontoprofil (däribland e-postadress, gatuadress och telefonnummer för kontakt) är korrekta.
- Kreditkortsuppgifterna stämmer.
- Du har inte redan ett Microsoft-konto där samma uppgifter används.

### <a name="error-your-current-account-type-is-not-supported"></a>Fel: *Din aktuella kontotyp stöds inte*

Det här problemet kan uppstå om kontot är registrerat i en [ohanterad Azure AD-katalog](../../active-directory/users-groups-roles/directory-self-service-signup.md) och inte finns i din organisations Azure AD-katalog.

Lös det här problemet genom att registrera Azure-kontot med hjälp av ett annat konto eller ta över den ohanterade AD-katalogen. Mer information finns i [Ta över en ohanterad katalog som administratör i Azure Active Directory](../../active-directory/users-groups-roles/domains-admin-takeover.md).

## <a name="additional-help-resources"></a>Fler hjälpresurser

Här är några fler felsökningsartiklar som gäller fakturering och prenumerationer i Azure

- [Nekat kort](troubleshoot-declined-card.md)
- [Problem med prenumerationsinloggning](troubleshoot-sign-in-issue.md)
- [Inga prenumerationer hittades](no-subscriptions-found.md)
- [Företagskostnadsvy inaktiverad](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontakta oss om du behöver hjälp

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure-fakturering](../../billing/index.md)
