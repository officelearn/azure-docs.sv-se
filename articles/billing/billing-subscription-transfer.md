---
title: Överföra faktureringsägarskapet för Azure-prenumeration till ett annat konto | Microsoft Docs
description: Beskriver hur du överföra faktureringsägarskapet för Azure-prenumeration till ett annat konto och några vanliga frågor (och svar FAQ) om processen
keywords: överföra prenumerationen för överföring av azure-prenumeration, azure, flytta azure-prenumeration till ett annat konto, azure ändra Prenumerationens ägare, överföra azure-prenumeration till ett annat konto, azure-överföring fakturering
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff3c57c31526119ab81225a1c70b163173be937
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514433"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Överföra faktureringsägarskapet för en Azure-prenumeration till ett annat konto

Du kanske vill överföra faktureringsägarskapet för din Azure-prenumeration om du lämnar organisationen, eller om du vill att prenumerationen ska debiteras till ett annat konto. Överföra faktureringsägarskapet till ett annat konto ger administratörer i den nya kontot behörigheten att utföra fakturering uppgifter som ändra betalningsmetod, se kostnader och avbryta prenumerationen.

Om du vill behålla faktureringsägarskapet men ändra vilken typ av din prenumeration, se [växla din Azure-prenumeration till ett annat erbjudande](billing-how-to-switch-azure-offer.md). Om du vill kontrollera vem som kan hantera resurser i prenumerationen Se [inbyggda roller för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Om du är kund med Enterprise Agreement(EA) kan enterprise-administratörer överföra faktureringsägarskapet för dina prenumerationer mellan konton. Mer information finns i [överföra faktureringsägarskapet för Enterprise Agreement (EA) prenumerationer](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Överföra faktureringsägarskapet för en Azure-prenumeration

1. Logga in på den [Azure-portalen](https://portal.azure.com) som administratör för faktureringskonto som har den prenumeration som du vill överföra. Om du vill ta reda på om du är administratör kan se [vanliga frågor och svar](#faq).

1. Sök på **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Välj **prenumerationer** från det vänstra fönstret. Beroende på din åtkomst, du kan behöva väljer du en fakturering omfattning och välj sedan **prenumerationer** eller **Azure-prenumerationer**.

1. Välj **överföring till andra kontot** för den prenumeration som du vill överföra. 

   ![Välj prenumeration för att överföra](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Ange e-postadress för en användare som är en faktureringsadministratör för det konto som ska vara den nya ägaren för prenumerationen och välj sedan **skicka överföringsbegäran**.

    > [!IMPORTANT]
    >
    > Om du överföra faktureringsägarskapet för prenumerationen till ett användarkonto i en annan Azure AD-klient, alla [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) tilldelningar för att hantera resurser i prenumerationen tas bort permanent. Endast den nya ägaren får åtkomst till att hantera resurser i prenumerationen. Mer information finns i [överföring av prenumeration till en användare i en annan Azure AD-klient](../active-directory/managed-identities-azure-resources/known-issues.md).
  
    ![Skicka sida för överföring](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Användaren får ett e-postmeddelande med anvisningar för att granska din överföringsbegäran.

   ![Prenumeration överföring e-postmeddelande till mottagare](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Om du vill godkänna överföringsbegäran användaren väljer länken i e-postmeddelandet och följer instruktionerna. Användaren behöver att välja en betalningsmetod som används för att betala för prenumerationen. Om användaren inte har ett Azure-konto, skulle de dessutom behöva registrera dig för ett nytt konto. 

   ![Första prenumeration överföring-webbsida](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Webbsida för överföring till andra prenumeration](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Webbsida för överföring till andra prenumeration](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Lyckades! Prenumerationen överförs nu.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Överföring av prenumeration till ett konto i en annan Azure AD-klient

En Azure Active Directory (AD)-klient skapas åt dig när du registrerar dig för Azure. Klienten representerar ditt konto. Du kan använda klienten för att hantera åtkomst till dina prenumerationer och resurser.

När du skapar en ny prenumeration, finns det i Azure AD-klient för ditt konto. Om du vill ge andra åtkomst till din prenumeration eller resurser måste du bjuda in dem till din klient. Det här hjälper dig att styra åtkomsten till dina prenumerationer och resurser.

När du överföra faktureringsägarskapet för prenumerationen till ett konto i en annan Azure AD-klient prenumerationen att flyttas till det nya kontot klient. Alla användare, grupper eller tjänstens huvudnamn som haft [rollbaserad åtkomst (RBAC)](../role-based-access-control/overview.md) för att hantera resurser i prenumerationen förlorar sin åtkomst. Endast användare i det nya kontot som kan ta emot din överföringsbegäran har åtkomst att hantera resurserna. För att ge åtkomst till de användare som ursprungligen hade åtkomst till den nya ägaren skulle behöva [manuellt lägga till dessa användare i prenumerationen](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Överföring av Visual Studio, Microsoft Partner Network (MPN) och betala Dev/Test-prenumerationer

Visual Studio och Microsoft Partner Network-prenumerationer har återkommande Azure-månadskrediten som är associerade med dem. Din kredit är inte tillgänglig i mål faktureringskonto när du överför dessa prenumerationer. Prenumerationen använder kredit i faktureringskonto för målet. Till exempel godkänner Bob överför en Visual Studio Enterprise-prenumeration till Annas konto på 9 september och Jane du överföringen. När överföringen är klar startar prenumerationen med kredit i Annas konto. Krediten återställs varje månad på 9. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Överföra faktureringsägarskapet för Enterprise Agreement (EA) prenumerationer

Enterprise-administratör kan överföra ägarskapet för prenumerationer mellan konton inom en registrering. Mer information finns i [överföra ägarskap för kontot](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) i EA-portalen.

<a id="CSP"></a>

## <a name="next-steps-after-accepting-billing-ownership"></a>Nästa steg efter att du godkänt faktureringsägarskapet

Om du har accepterat faktureringsägarskapet för en Azure-prenumeration, rekommenderar vi att du läser dessa nästa steg:

1. Granska och uppdatera tjänstadministratören och Medadministratörer andra RBAC-roller. Mer information finns i [Lägg till eller ändra Azure-prenumerationsadministratörer](billing-add-change-azure-subscription-administrator.md) och [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md).
1. Uppdatera autentiseringsuppgifter som är associerade med den här prenumerationens tjänster, inklusive:
   1. Av hanteringscertifikat som och ger användaren administratörsbehörighet till prenumerationsresurser. Mer information finns i [skapa och ladda upp ett certifikat för Azure](../cloud-services/cloud-services-certs-create.md)
   1. Snabbtangenter för tjänster som lagring. Mer information finns i [om Azure storage-konton](../storage/common/storage-create-storage-account.md)
   1. Fjärrautentiseringsuppgifter åtkomst för tjänster som Azure Virtual Machines.
1. Om du arbetar med en partner Överväg gå att uppdatera partner-ID för den här prenumerationen. Du kan uppdatera partner-ID i den [Azure-portalen](https://portal.azure.com). Mer information finns i [länka ett partner-ID till dina Azure-konton](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Prenumerationstyper som stöds

Överföring av prenumeration i Azure portal är tillgänglig för prenumerationstyper som anges nedan. För närvarande överföring stöds inte för [kostnadsfri utvärderingsversion](https://azure.microsoft.com/offers/ms-azr-0044p/) eller [Azure i Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) prenumerationer. Du hittar en lösning i [flytta resurser till ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md). Om du vill överföra andra prenumerationer, som [sponsring](https://azure.microsoft.com/offers/ms-azr-0036p/) eller supportavtal, [kontakta supporten för Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Prenumeranter på Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Betala per användning – utveckling/testning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [Via EA-portalen](#EA).

\*\* Stöds endast för konton som skapas när du registrerade dig på Azure-webbplatsen. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Vanliga frågor (och svar FAQ) om avsändare

Dessa vanliga frågor och svar gäller för användare som överföra faktureringsägarskapet för en Azure-prenumeration till ett annat konto.

### <a name="whoisaa"></a> Vem är en faktureringsadministratör för ett konto?

Faktureringsadministratör är en person som har behörighet att hantera faktureringen för ett konto. De har behörighet att komma åt fakturering på den [Azure-portalen](https://portal.azure.com) och utföra olika fakturering uppgifter som att skapa prenumerationer, visa och betala fakturor eller uppdatera betalningsmetoder.

Använd följande steg för att identifiera prenumerationer som du är en faktureringsadministratör:

1. Gå till den [kostnadshantering + fakturering sidan på Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Välj **prenumerationer** från det vänstra fönstret. Beroende på din åtkomst, du kan behöva väljer du en fakturering omfattning och välj sedan **prenumerationer** eller **Azure-prenumerationer**
1. Prenumerationssidan visar en lista över alla prenumerationer som du är administratör för fakturering.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Allt överföra? Inklusive resursgrupper, virtuella datorer, diskar och andra tjänster som körs?

Alla resurser som virtuella datorer, diskar och webbplatser överföring till det nya kontot. Men om du överför prenumerationen till ett konto i en annan Azure AD-klient, någon [administratörsroller](billing-add-change-azure-subscription-administrator.md) och [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md) tilldelningar för prenumerationen [inte överföring](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Dessutom [appregistreringar](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) och andra tjänster för klientspecifik överförs inte tillsammans med prenumerationen.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Kan jag överföra ägarskap till ett konto i ett annat land?
Tyvärr kan inte överföringar utföras i Azure-portalen mellan land. Att överföra din prenumeration över länder/regioner, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Jag är administratör på två konton. Kan jag överföra en prenumeration från en av Mina konton till en annan?
Ja, kan du överföra prenumerationen mellan dina konton. Dina konton anses begreppsmässigt konton i två olika användare så att du kan använda stegen ovan för att överföra prenumerationer mellan dina konton.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Resulterar en överföring av prenumeration i något avbrott av tjänsten?

Om du överför en prenumeration till en användare i samma Azure AD-klient påverkas inte till de resurser som körs i prenumerationen.  Men om du överför prenumerationen till en användare i en annan klient, alla användare, grupper och tjänstens huvudnamn som haft [rollbaserad åtkomst (RBAC)](../role-based-access-control/overview.md) för att hantera resurser i prenumerationen förlorar sin åtkomst. 

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>Mottagaren hanteringskonfigurationstjänsten har tillgång till användnings- och faktureringshistorik?

Den enda informationen som är tillgängliga för mottagaren är den senaste månaden kostnaden för din prenumeration. Resten av användnings- och faktureringshistorik överförs inte med prenumerationen

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Hur migrerar data och tjänster för min Azure-prenumeration till ny prenumeration?

Om du inte kan överföra äganderätten till prenumerationen kan du migrera dina resurser manuellt. Se [flytta resurser till ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Om jag överföra en Visual Studio eller Microsoft Partner Network-prenumeration min kredit Överför med prenumerationen i det nya kontot?

Nej, din kredit är inte tillgänglig i det nya kontot. Den användare som accepterar överföringsbegäran måste ha en licens för Visual Studio att acceptera överföringsbegäran. Prenumerationen använder Visual Studio-kredit som finns i användarens konto. Mer information finns i [överföring av Visual Studio, Microsoft Partner Network (MPN) och betala Dev/Test-prenumerationer](#transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions)


## <a name="frequently-asked-questions-faq-for-recipients"></a>Vanliga frågor (och svar FAQ) för mottagare

Dessa vanliga frågor och svar gäller för användare som tar över faktureringsägarskapet för en Azure-prenumeration från ett annat konto.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Om jag tar över faktureringsägarskapet för en prenumeration från ett annat konto, göra användare i det konto som fortsätter att ha åtkomst till mina resurser?

Om prenumerationen överförs till ett konto i samma Azure AD-klient, alla användare, grupper och tjänstens huvudnamn som haft [rollbaserad åtkomst (RBAC)](../role-based-access-control/overview.md) för att hantera resurser i prenumerationen för att behålla åtkomsten. Om du vill visa användare som har RBAC-åtkomst till prenumerationen, använder du följande steg:

1. Gå till den [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration som du vill kontrollera och välj sedan **åtkomstkontroll (IAM)** från det vänstra fönstret.
1. Välj **rolltilldelningar** högst upp på sidan. Tilldelningar rollsidan visas alla användare som har Rollbaserad åtkomstkontroll för prenumerationen.

Om prenumerationen överförs till ett konto i en annan Azure AD-klient, alla användare, grupper och tjänstens huvudnamn som haft [rollbaserad åtkomst (RBAC)](../role-based-access-control/overview.md) för att hantera resurser i prenumerationen förlorar sin åtkomst. Även om de inte längre har Rollbaserad åtkomstkontroll, kan de dock fortfarande ha åtkomst till prenumerationen via vissa sätt, inklusive:

* Av hanteringscertifikat som och ger användaren administratörsbehörighet till prenumerationsresurser. Mer information finns i [skapa och ladda upp ett Hanteringscertifikat för Azure](../cloud-services/cloud-services-certs-create.md).
* Snabbtangenter för tjänster som lagring. Mer information finns i [Om Azure Storage-konton](../storage/common/storage-create-storage-account.md).
* Fjärrautentiseringsuppgifter åtkomst för tjänster som Azure Virtual Machines.

Om mottagaren behöver begränsa, åtkomst till sina resurser, bör de uppdatera alla hemligheter som är kopplade till tjänsten. De flesta resurser kan uppdateras med hjälp av följande steg:

  1. Logga in på [Azure Portal](https://portal.azure.com).
  2. På navmenyn väljer **alla resurser**.
  3. Välj resursen.
  4. I resurssidan klickar du på **inställningar**. Här kan du visa och uppdatera befintliga hemligheter.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Om jag tar över faktureringsägarskapet för en prenumeration i mitten faktureringsperioden måste jag betala för hela faktureringsperioden?

Ditt konto ansvarar för betalningen för all användning som rapporteras från tidpunkten för överföring och senare. Det kan finnas vissa användning som ägde rum före överföringen men rapporterades efteråt. Användningen är inkluderat i ditt konto faktura.

### <a name="can-i-use-a-different-payment-method"></a>Kan jag använda en annan betalningsmetod?

Ja. Vid accepterande av organisationens överföringsbegäran, kan du välja en befintlig betalningsmetod som är kopplat till ditt konto eller lägga till en ny betalningsmetod.

## <a name="troubleshooting"></a>Felsökning

### <a id="no-button"></a> Varför visas inte knappen ”överföra prenumerationen”?

Självbetjäning prenumerationsöverföringen är tyvärr inte tillgängligt för ditt faktureringskonto. För närvarande kan stöder vi inte överföra ut faktureringsägarskapet för Enterprise Agreement (EA)-konto i Azure-portalen. Dessutom stöder kundavtal för Microsoft-konton som skapas när den arbetade via Microsoft-försäljning inte överföra faktureringsägarskapet för prenumerationer. 

### <a id="no-button"></a> Varför inte min prenumeration skriver support överföring? 

Tyvärr stöder inte alla typer av prenumerationer överföringen av ägarskapet för fakturering. Om du vill visa listan över prenumerationstyper som har stöd för överföringar, se [prenumerationstyper som stöds](#supported-subscription-types)

### <a id="no-button"></a> Varför får jag ett åtkomst nekad-fel när jag försöker överföra faktureringsägarskapet för en prenumeration? 

Det här felet visas om du försöker överföra en Plan för Microsoft Azure-prenumeration och du inte har nödvändig behörighet. Du måste vara ägare eller deltagare i avsnittet faktura som prenumerationen debiteras om du vill överföra en prenumeration på Azure-plan. Mer information finns i [hantera prenumerationer för faktura](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Granska och uppdatera tjänstadministratören och Medadministratörer andra RBAC-roller. Mer information finns i [Lägg till eller ändra Azure-prenumerationsadministratörer](billing-add-change-azure-subscription-administrator.md) och [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md).
