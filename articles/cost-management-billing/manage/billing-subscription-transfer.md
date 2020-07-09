---
title: Överföra faktureringsägarskapet för en Azure-prenumeration
description: Beskriver hur du överför faktureringsägarskapet för en Azure-prenumeration till ett annat konto samt några vanliga frågor och svar om processen
keywords: transfer azure subscription, azure transfer subscription, move azure subscription to another account,azure change subscription owner, transfer azure subscription to another account, azure transfer billing
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 722d1bca7f983c124c85e6d675f51d29c5357522
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85854947"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Överföra faktureringsägarskap för en Azure-prenumeration till ett annat konto

Du vill kanske överföra faktureringsägarskapet för din Azure-prenumeration om du lämnar din organisation eller om du vill att din prenumeration ska faktureras till ett annat konto. Genom att överföra faktureringsägarskapet till ett annat konto kan du ge administratörerna i det nya kontot behörighet till faktureringsuppgifter. De kan ändra betalningsmetoden, visa avgifter och avbryta prenumerationen.

Om du vill behålla faktureringsägarskapet men ändra typen för din prenumeration kan du läsa mer i [Ändra din Azure-prenumeration till ett annat erbjudande](switch-azure-offer.md). Information om hur du kontrollerar vem som kan komma åt resurser i prenumerationen finns i avsnittet om [inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

Om du är Enterprise-avtalskund (EA) kan ditt företags administratörer överföra faktureringsägarskapet för dina prenumerationer mellan konton. Mer information finns i [Överföra faktureringsägarskap för EA-prenumerationer (Enterprise-avtal)](#EA).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Överföra faktureringsägarskapet för en Azure-prenumeration

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör för det faktureringskonto som har den prenumeration som du vill överföra. Information om hur du tar reda på huruvida du är administratör finns i [Vanliga frågor och svar](#faq).

1. Sök efter **Kostnadshantering och fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Välj **Prenumerationer** i den vänstra rutan. Beroende på din åtkomst kan du behöva välja ett faktureringsomfång och sedan välja **Prenumerationer** eller **Azure-prenumerationer**.

1. Välj **Överför faktureringsägarskapet** för den prenumeration som du vill överföra.

   ![Välja den prenumeration som ska överföras](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Ange e-postadressen till en användare som är faktureringsadministratör för det konto som ska bli den nya ägaren till prenumerationen.

1. Om du överför din prenumeration till ett konto i en annan Azure AD-klientorganisation väljer du huruvida du vill flytta prenumerationen till det nya kontots klientorganisation. Mer information finns i [Överföra en prenumeration till ett konto i en annan Azure AD-klientorganisation](#transfer-a-subscription-to-another-azure-ad-tenant-account)

    > [!IMPORTANT]
    >
    > Om du väljer att flytta prenumerationen till det nya kontots Azure AD-klient tas alla [tilldelningar av Azure-roller](../../role-based-access-control/role-assignments-portal.md) som ger åtkomst till resurser i prenumerationen bort permanent. Endast den användare i det nya kontot som godkänner din överföringsbegäran får åtkomst till att hantera resurser i prenumerationen. Mer information finns i nästa avsnitt, [Överföra en prenumeration till ett konto för en annan Azure AD-klientorganisation](#transfer-a-subscription-to-another-azure-ad-tenant-account). Du kan också avmarkera kryssrutan för **prenumerationens Azure AD-klientorganisation** om du vill överföra faktureringsägarskapet utan att flytta prenumerationen till det nya kontots klientorganisation. Om du gör det bevaras befintliga Azure-rolltilldelningar för åtkomst till Azure-resurser.

    ![Sidan för att skicka överföring](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Välj **Skicka överföringsbegäran**.

1. Användaren får ett e-postmeddelande med instruktioner för att granska din överföringsförfrågan.

   ![E-postmeddelande om överföring av prenumeration skickas till mottagaren](./media/billing-subscription-transfer/billing-receiver-email.png)

1. För att godkänna överföringsbegäran väljer användaren länken i e-postmeddelandet och följer anvisningarna. Användaren väljer sedan en betalningsmetod som ska användas för att betala för prenumerationen. Användare som inte har ett Azure-konto måste registrera sig för ett nytt konto.

   ![Webbplats för första prenumerationsöverföringen](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Webbplats för andra prenumerationsöverföringen](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Webbplats för andra prenumerationsöverföringen](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Lyckades! Prenumerationen har nu överförts.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Överföra en prenumeration till ett konto för en annan Azure AD-klientorganisation

En Azure Active Directory-klientorganisation (AD) skapas åt dig när du registrerar dig för Azure. Klientorganisationen representerar ditt konto. Du använder klientorganisationen för att hantera åtkomst till dina prenumerationer och resurser.

När du skapar en ny prenumeration värdhanteras den i ditt kontos Azure AD-klientorganisation. Om du vill ge andra åtkomst till din prenumeration eller dess resurser måste du bjuda in dem till att gå med i din klientorganisation. Detta hjälper dig att kontrollera åtkomsten till dina prenumerationer och resurser.

När du överför faktureringsägarskap för din prenumeration till ett konto i en annan Azure AD-klientorganisation kan du flytta prenumerationen till det nya kontots klientorganisation. Om du gör det kommer alla användare, grupper eller tjänsthuvudnamn som hade [tilldelats Azure-roller](../../role-based-access-control/role-assignments-portal.md) för att hantera prenumerationer och deras resurser att förlora sin åtkomst. Endast den användare i det nya kontot som godkänner din överföringsbegäran får åtkomst till att hantera resurserna. För att de användare som ursprungligen hade åtkomst ska fortsätta ha åtkomst måste den nya ägaren lägga till dessa användare i prenumerationen manuellt. Mer information finns i [Överföra en Azure-prenumeration till en annan Azure AD-katalog (förhandsversion)](../../role-based-access-control/transfer-subscription.md).


## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Överföra prenumerationer på Visual Studio och Partner Network

Prenumerationer på Visual Studio och Microsoft Partner Network är associerade med månatlig återkommande Azure-kredit. När du överför dessa prenumerationer är din kredit inte tillgänglig på målfaktureringskontot. Prenumerationen använder krediten i målfaktureringskontot. Vi tar exemplet att Bob överför en Visual Studio Enterprise-prenumeration till Janes konto den 9 september och Jane godkänner överföringen. När överföringen är klar börjar prenumerationen använda kredit i Janes konto. Krediten nollställs den nionde dagen varje månad.


<a id="EA"></a>

## <a name="transfer-ea-subscription-billing-ownership"></a>Överföra faktureringsägarskapet för EA-prenumerationer

Företagsadministratören kan överföra ägarskapet för prenumerationer mellan konton i en registrering. Mer information finns i [Ändra kontoinnehavare](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#change-account-owner) i EA-portalen.

## <a name="next-steps-after-accepting-billing-ownership"></a>Nästa steg när faktureringsägarskapet har godkänts

Om du har godkänt faktureringsägarskapet för en Azure-prenumeration rekommenderar vi att du läser dessa nästa steg:

1. Granska och uppdatera Azure-rolltilldelningarna för tjänstadministratörer och medadministratörer. Mer information finns i [Lägga till eller ändra administratörer för en Azure-prenumeration](add-change-subscription-administrator.md) och [Lägga till eller ta bort Azure-rolltilldelningar från Azure-portalen](../../role-based-access-control/role-assignments-portal.md).
1. Uppdatera autentiseringsuppgifter som är associerade med den här prenumerationens tjänster, däribland:
   1. Hanteringscertifikat som ger användaren administratörsbehörighet till prenumerationsresurser. Mer information finns i [Skapa och ladda upp ett hanteringscertifikat för Azure](../../cloud-services/cloud-services-certs-create.md)
   1. Åtkomstnycklar för tjänster såsom Storage. Mer information finns i [Om Azure Storage-konton](../../storage/common/storage-create-storage-account.md)
   1. Autentiseringsuppgifter för fjärråtkomst för tjänster såsom Azure Virtual Machines.
1. Om du arbetar med en partner kan du överväga att uppdatera partner-ID i den här prenumerationen. Du kan uppdatera partner-ID i [Azure-portalen](https://portal.azure.com). Mer information finns i [Länka ett partner-ID till dina Azure-konton](link-partner-id.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Prenumerationstyper som stöds

Prenumerationsöverföring i Azure-portalen är tillgänglig för de prenumerationstyper som anges nedan. För närvarande stöds inte överföring för prenumerationerna [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/offers/ms-azr-0044p/) och [Azure in Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/). En tillfällig lösning beskrivs i [Flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md). För överföring av andra prenumerationer, som supportplaner, [kontaktar du Azure Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Prenumeranter på Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Dev/Test – betala per användning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure-plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [Via EA-portalen](#EA).

\*\* Stöds endast för konton som skapas vid registreringen på Azure-webbplatsen.

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Vanliga frågor och svar för avsändare

Dessa vanliga frågor och svar gäller för användare som överför faktureringsägarskapet för en Azure-prenumeration till ett annat konto.

### <a name="who-is-a-billing-administrator-of-an-account"></a><a name="whoisaa"></a> Vem är faktureringsadministratör för ett konto?

En faktureringsadministratör är en person som har behörighet att hantera fakturering för ett konto. Personen har behörighet att komma åt fakturering på [Azure-portalen](https://portal.azure.com) och utföra olika faktureringsuppgifter, till exempel skapa prenumerationer, visa och betala fakturor eller uppdatera betalningsmetoder.

Du kan identifiera de konton som du är faktureringsadministratör för med hjälp av följande steg:

1. Gå till [sidan Kostnadshantering och fakturering i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Välj **Alla faktureringsomfång** i rutan till vänster.
1. På prenumerationssidan visas alla prenumerationer som du är faktureringsadministratör för.

Om du inte vet som är kontoadministratör för en prenumeration, tar du reda på det med hjälp av följande steg.

1. Gå till [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration du vill kontrollera och gå till **Inställningar**.
1. Välj **Egenskaper**. Prenumerationens kontoadministratör visas i rutan **Kontoadministratör**.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Överförs allt? Inklusive resursgrupper, virtuella datorer, diskar och andra tjänster som körs?

Alla dina resurser såsom virtuella datorer, diskar och webbplatser överförs till det nya kontot. Men om du överför en prenumeration till ett konto i en annan Azure AD-klientorganisation kommer eventuella [administratörsroller](add-change-subscription-administrator.md) och [Azure-rolltilldelningar](../../role-based-access-control/role-assignments-portal.md) i prenumerationen [inte att överföras](#transfer-a-subscription-to-another-azure-ad-tenant-account). [Appregistreringar](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) och andra klientorganisationsspecifika tjänster överförs inte heller med prenumerationen.

### <a name="can-i-transfer-ownership-to-an-account-in-another-countryregion"></a>Kan jag överföra ägarskapet till ett konto i ett annat land/en annan region?
Det går tyvärr inte att utföra överföringar mellan olika länder/regioner i Azure-portalen. Om du vill överföra din prenumeration mellan olika länder/regioner bör du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Jag är administratör i två konton. Kan jag överföra en prenumeration från ett av mina konton till ett annat?
Ja, du kan överföra prenumerationen mellan dina konton. Konceptuellt betraktas dina konton som konton för två olika användare. Därmed kan du använda ovanstående steg för att överföra prenumerationer mellan dina konton.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Resulterar en prenumerationsöverföring i avbrottstid i tjänsten?

Om du överför en prenumeration till ett konto i samma Azure AD-klientorganisation påverkas inte de resurser som körs i prenumerationen. Kontextinformation som sparas i PowerShell uppdateras däremot inte, så du kan behöva rensa den eller ändra inställningarna. Om du överför prenumerationen till ett konto i en annan klientorganisation och väljer att flytta prenumerationen till klientorganisationen kommer alla användare, grupper och tjänsthuvudnamn som hade [tilldelats roller i Azure](../../role-based-access-control/role-assignments-portal.md) för att komma åt resurser i prenumerationen att förlora sin åtkomst. Detta kan resultera i stilleståndstid i tjänsten.

### <a name="can-users-in-new-account-access-usage-and-billing-history"></a>Kan användare i det nya kontot komma åt användnings- och faktureringshistorik?

Den enda information som är tillgänglig för användare i det nya kontot är den senaste månadens kostnad för din prenumeration. Resten av användnings- och faktureringshistoriken överförs inte med prenumerationen

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Hur migrerar jag data och tjänster för min Azure-prenumeration till en ny prenumeration?

Om du inte kan överföra ägarskapet för prenumerationen kan du migrera dina resurser manuellt. Se [Flytta resurser till ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Om jag överför en Visual Studio- eller Microsoft Partner Network-prenumeration, följer min kredit med prenumerationen i det nya kontot?

Nej, din kredit är inte tillgänglig i det nya kontot. Den användare som godkänner överföringsbegäran behöver ha en Visual Studio-licens för att godkänna överföringsbegäran. Prenumerationen använder den Visual Studio-kredit som är tillgänglig i användarens konto. Mer information finns i [Överföra prenumerationer på Visual Studio och Partner Network](#transfer-visual-studio-and-partner-network-subscriptions).


## <a name="frequently-asked-questions-faq-for-recipients"></a>Vanliga frågor och svar för mottagare

Dessa vanliga frågor och svar gäller för användare som godkänner faktureringsägarskapet för en Azure-prenumeration från ett annat konto.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Om jag tar över faktureringsägarskapet för en prenumeration från ett annat konto, fortsätter användare i det kontot att ha åtkomst till mina resurser?

Ja. [Administratörsroller](add-change-subscription-administrator.md) och [Azure-rolltilldelningar](../../role-based-access-control/role-assignments-portal.md) kan dock tas bort. Åtkomsten förloras när kontot är i en annan Azure AD-klientorganisation än prenumerationens klientorganisation och användaren som skickade överföringsbegäran flyttar prenumerationen till ditt kontos klientorganisation. Om du vill visa användare som har tilldelats roller i Azure som ger åtkomst till resurser i prenumerationen följer du dessa steg:

1. Gå till [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration som du vill kontrollera och välj sedan **Åtkomstkontroll (IAM)** i den vänstra rutan.
1. Välj **Rolltilldelningar** överst på sidan. På sidan för rolltilldelningar visas alla användare som har åtkomst i prenumerationen.

Även om [Azure-rolltilldelningar](../../role-based-access-control/role-assignments-portal.md) tas bort under överföringen kan användare under det ursprungliga ägarkontot fortfarande ha åtkomst till prenumerationen via andra säkerhetsmekanismer, t.ex.:

* Hanteringscertifikat som ger användaren administratörsbehörighet till prenumerationsresurser. Mer information finns i [Skapa och ladda upp ett hanteringscertifikat för Azure](../../cloud-services/cloud-services-certs-create.md).
* Åtkomstnycklar för tjänster som Storage. Mer information finns i [Om Azure Storage-konton](../../storage/common/storage-create-storage-account.md).
* Autentiseringsuppgifter för fjärråtkomst för tjänster som Azure Virtual Machines.

Om mottagaren behöver begränsa åtkomsten till sina resurser bör han eller hon överväga att uppdatera eventuella hemligheter som är associerade med tjänsten. De flesta resurser kan uppdateras med hjälp av följande steg:

  1. Logga in på [Azure-portalen](https://portal.azure.com).
  2. Välj **Alla resurser** i hubbmenyn.
  3. Välj resursen.
  4. Välj **Inställningar** på resurssidan. Här kan du visa och uppdatera befintliga hemligheter.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Om jag tar över ägarskapet för en prenumeration i mitten av faktureringsperioden, måste jag då betala för hela faktureringsperioden?

Ditt konto ansvarar för betalning för all användning som rapporteras från tiden för överföring och framåt. Det kan finnas en viss användning som ägde rum före överföringen men som rapporterades efteråt. Användningen ingår i fakturan för ditt konto.

### <a name="can-i-use-a-different-payment-method"></a>Kan jag använda en annan betalningsmetod?

Ja. När du godkänner överföringsbegäran kan du välja en befintlig betalningsmetod som är kopplad till ditt konto eller lägga till en ny betalningsmetod.

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>Hur överför jag kontoägarskapet för min Enterprise-avtalsprenumeration (EA) om den ursprungliga kontoinnehavaren inte längre finns i organisationen?

Företagsadministratörer kan uppdatera kontoägarskapet för alla konton, även efter att den ursprungliga kontoinnehavaren inte längre är en del av organisationen. Det kan de göra genom att följa instruktionerna för [överföring av kontoägarskap för alla prenumerationer](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) i EA-portalen.

## <a name="troubleshooting"></a>Felsökning

### <a name="why-dont-i-see-the-transfer-subscription-button"></a><a id="no-button"></a> Varför visas inte knappen ”Överför prenumeration”?

Prenumerationsöverföring via självbetjäning är inte tillgänglig för ditt faktureringskonto. För närvarande stöder vi inte överföring av faktureringsägarskap för prenumerationer i Enterprise-avtalskonton (EA) i Azure-portalen. Dessutom har inte Microsoft Customer Agreement-konton som skapas vid arbete med en Microsoft-representant stöd för överföring av faktureringsägarskap.

### <a name="why-doesnt-my-subscription-type-support-transfer"></a><a id="no-button"></a> Varför stöder inte min prenumerationstyp överföring?

Det är inte alla prenumerationstyper som stöder överföring av faktureringsägarskap. En lista över prenumerationstyper som stöder överföring finns i [Prenumerationstyper som stöds](#supported-subscription-types)

### <a name="why-am-i-receiving-an-access-denied-error-when-i-try-to-transfer-billing-ownership-of-a-subscription"></a><a id="no-button"></a> Varför får jag ett fel om nekad åtkomst när jag försöker överföra faktureringsägarskap för en prenumeration?

Det här felet uppstår om du försöker överföra en prenumeration på Microsoft Azure-plan och du inte har den behörighet som krävs. För att kunna överföra en prenumeration på Microsoft Azure-plan måste du vara ägare eller deltagare i det fakturaavsnitt som prenumerationen faktureras till. Mer information finns i [Hantera prenumerationer för fakturaavsnitt](understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Granska och uppdatera Azure-rolltilldelningarna för tjänstadministratörer och medadministratörer. Mer information finns i [Lägga till eller ändra administratörer för en Azure-prenumeration](add-change-subscription-administrator.md) och [Lägga till eller ta bort Azure-rolltilldelningar från Azure-portalen](../../role-based-access-control/role-assignments-portal.md).
