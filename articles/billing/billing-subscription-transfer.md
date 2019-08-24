---
title: Överför fakturerings ägandet av Azure-prenumerationen till ett annat konto | Microsoft Docs
description: Beskriver hur du överför fakturerings ägarskapet för en Azure-prenumeration till ett annat konto och några vanliga frågor och svar om processen
keywords: överför Azure-prenumeration, Azure transfer-prenumeration, flytta Azure-prenumeration till ett annat konto, Azure ändra prenumerations ägare, överför Azure-prenumeration till ett annat konto, fakturering av Azure-överföring
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
ms.openlocfilehash: 1e4496e2b5d2b21fd878ef68665b8e5b06fa6cc5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012526"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Överför fakturerings ägarskapet för en Azure-prenumeration till ett annat konto

Du kanske vill överföra fakturerings ägarskapet för din Azure-prenumeration om du lämnar din organisation eller om du vill att din prenumeration ska faktureras till ett annat konto. Överföring av fakturerings ägande till ett annat konto ger administratörerna i det nya kontot behörighet att utföra fakturerings uppgifter, till exempel ändra betalnings metod, Visa avgifter och avbryta prenumerationen.

Om du vill behålla fakturerings ägarskapet men ändra typen för din prenumeration kan du läsa mer i ändra [din Azure-prenumeration till ett annat erbjudande](billing-how-to-switch-azure-offer.md). Om du vill kontrol lera vem som kan hantera resurser i prenumerationen, se [inbyggda roller för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Om du är en Enterprise-avtal (EA)-kund kan företagets administratörer överföra fakturerings ägandet för dina prenumerationer mellan konton. Mer information finns i [överföra fakturerings ägarskap för Enterprise-avtal (EA) prenumerationer](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Överför fakturerings ägarskapet för en Azure-prenumeration

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör för det fakturerings konto som har den prenumeration som du vill överföra. För att ta reda på om du är administratör, se [vanliga frågor och svar](#faq).

1. Sök på **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Välj **prenumerationer** i den vänstra rutan. Beroende på åtkomst kan du behöva välja en fakturerings omfattning och sedan välja prenumerationer eller Azure - **prenumerationer**.

1. Välj **överför fakturerings ägarskap** för den prenumeration som du vill överföra. 

   ![Välj den prenumeration som ska överföras](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Ange e-postadressen till en användare som är fakturerings administratör för det konto som kommer att vara den nya ägaren till prenumerationen.

1. Om du överför din prenumeration till ett konto i en annan Azure AD-klient väljer du om du vill flytta prenumerationen till det nya kontots klient organisation. Mer information finns i [överföra en prenumeration till ett konto i en annan Azure AD-klient](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)

    > [!IMPORTANT]
    >
    > Om du väljer att flytta prenumerationen till det nya kontots Azure AD-klient tas all [rollbaserad åtkomst kontroll (RBAC)](../role-based-access-control/overview.md) tilldelningar för att hantera resurser i prenumerationen bort permanent. Endast användaren i det nya kontot som godkänner din överföringsbegäran har åtkomst till att hantera resurser i prenumerationen. Mer information finns i [överföra en prenumeration till en användare i en annan Azure AD-klient](../active-directory/managed-identities-azure-resources/known-issues.md). Alternativt kan du avmarkera kryss rutan för prenumerationen på Azure AD-klienten om du vill överföra fakturerings ägarskapet utan att flytta prenumerationen till det nya kontots klient organisation. Om du gör det kommer befintliga RBAC-behörigheter att hantera Azure-resurser att behållas.
  
    ![Skicka överförings sida](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Välj **Skicka överförings förfrågan**.

1. Användaren får ett e-postmeddelande med instruktioner för att granska din överförings förfrågan.

   ![E-postprenumerations överföring skickas till mottagaren](./media/billing-subscription-transfer/billing-receiver-email.png)

1. För att godkänna överförings förfrågan väljer användaren länken i e-postmeddelandet och följer instruktionerna. Användaren måste välja en betalnings metod som ska användas för att betala för prenumerationen. Om användaren inte har ett Azure-konto måste de dessutom behöva registrera sig för ett nytt konto. 

   ![Första prenumerations överförings webb sida](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Andra webb sidan för prenumerations överföring](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Andra webb sidan för prenumerations överföring](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Lyckades! Nu överförs prenumerationen.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Överföra en prenumeration till ett konto i en annan Azure AD-klient

En Azure Active Directory-klient (AD) skapas åt dig när du registrerar dig för Azure. Klienten representerar ditt konto. Du kan använda klienten för att hantera åtkomst till dina prenumerationer och resurser.

När du skapar en ny prenumeration, finns den i Azure AD-klienten för ditt konto. Om du vill ge andra åtkomst till din prenumeration eller dess resurser måste du bjuda in dem för att ansluta till din klient. Detta hjälper dig att kontrol lera åtkomsten till dina prenumerationer och resurser.

När du överför fakturerings ägarskapet för din prenumeration till ett konto i en annan Azure AD-klient kan du flytta prenumerationen till det nya kontots klient organisation. Om du gör det, kommer alla användare, grupper eller tjänst huvud namn som hade [rollbaserad åtkomst (RBAC)](../role-based-access-control/role-assignments-portal.md) att hantera prenumerationer och dess resurser att förlora sin åtkomst. Endast användaren i det nya kontot som accepterar din överföringsbegäran kommer att ha åtkomst till att hantera resurserna. För att ge åtkomst till de användare som ursprungligen hade åtkomst måste den nya ägaren [manuellt lägga till dessa användare i prenumerationen](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Överföra Visual Studio, MPN och betala per användning för utveckling/testning av prenumerationer

Visual Studio och Microsoft Partner Network prenumerationer har månatlig återkommande Azure-kredit. När du överför dessa prenumerationer är din kredit inte tillgänglig på mål fakturerings kontot. I prenumerationen används krediten på mål fakturerings kontot. Till exempel, om Bob överför en Visual Studio Enterprise-prenumeration till Jane-kontot på nionde sept och Jane accepterar överföringen. När överföringen är klar börjar prenumerationen med kredit på Jane-kontot. Krediten återställs varje månad den 9: e. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Överföra fakturerings ägarskap för Enterprise-avtal (EA)-prenumerationer

Företags administratören kan överföra ägarskapet för prenumerationer mellan konton inom en registrering. Mer information finns i [överföra konto ägarskap](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) i EA-portalen.

## <a name="next-steps-after-accepting-billing-ownership"></a>Nästa steg när du har accepterat fakturerings ägandet

Om du har accepterat fakturerings ägarskapet för en Azure-prenumeration rekommenderar vi att du läser dessa nästa steg:

1. Granska och uppdatera tjänst administratör, medadministratörer och andra RBAC-roller. Läs mer i [lägga till eller ändra Azure-prenumerations administratörer](billing-add-change-azure-subscription-administrator.md) och [Hantera åtkomst med RBAC och Azure Portal](../role-based-access-control/role-assignments-portal.md).
1. Uppdatera autentiseringsuppgifterna som är associerade med prenumerationens tjänster, inklusive:
   1. Hanterings certifikat som ger användar administratörs behörighet till prenumerations resurser. Mer information finns i [skapa och ladda upp ett hanterings certifikat för Azure](../cloud-services/cloud-services-certs-create.md)
   1. Åtkomst nycklar för tjänster som lagring. Mer information finns i [om Azure Storage-konton](../storage/common/storage-create-storage-account.md)
   1. Autentiseringsuppgifter för fjärråtkomst för tjänster som Azure Virtual Machines.
1. Om du arbetar med en partner kan du överväga att uppdatera partner-ID: t för den här prenumerationen. Du kan uppdatera partner-ID: t i [Azure Portal](https://portal.azure.com). Mer information finns i [Länka ett partner-ID till dina Azure-konton](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Prenumerations typer som stöds

Prenumerations överföring i Azure Portal är tillgänglig för de prenumerations typer som anges nedan. För närvarande stöds inte överföring av prenumerationer med [kostnads fria utvärderings versioner](https://azure.microsoft.com/offers/ms-azr-0044p/) eller [Azure i Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) . En lösning finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md). [Kontakta Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)-supporten om du vill överföra andra prenumerationer, t. ex. [sponsring](https://azure.microsoft.com/offers/ms-azr-0036p/) eller support avtal.

- [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise-prenumeranter (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Dev/Test – betala per användning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\*[Via EA-portalen](#EA).

\*\*Stöds endast för konton som skapas vid registreringen på Azure-webbplatsen. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Vanliga frågor och svar om avsändare

Dessa vanliga frågor gäller användare, som överför fakturerings ägandet för en Azure-prenumeration till ett annat konto.

### <a name="whoisaa"></a>Vem är en fakturerings administratör för ett konto?

En fakturerings administratör är en person som har behörighet att hantera fakturering för ett konto. De har behörighet att få åtkomst till fakturering på [Azure Portal](https://portal.azure.com) och utföra olika fakturerings uppgifter, t. ex. skapa prenumerationer, Visa och betala fakturor, eller uppdatera betalnings metoder.

Använd följande steg för att identifiera konton som du är fakturerings administratör för:

1. Besök [Cost Management + fakturerings sida i Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Välj **alla fakturerings omfattningar** i rutan till vänster. 
1. På sidan prenumerationer visas alla prenumerationer som du är fakturerings administratör för.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Överförs allt? Inklusive resurs grupper, virtuella datorer, diskar och andra tjänster som körs?

Alla resurser som virtuella datorer, diskar och webbplatser överförs till det nya kontot. Men om du överför prenumerationen till ett konto i en annan Azure AD-klient [överförs inte](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)alla [Administratörs roller](billing-add-change-azure-subscription-administrator.md) och [rollbaserade Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) tilldelningar för prenumerationen. Dessutom överförs [app](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) -registreringar och andra klient tjänster inte tillsammans med prenumerationen.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Kan jag överföra ägarskapet till ett konto i ett annat land?
Det går tyvärr inte att utföra överföringar mellan land i Azure Portal. [Kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om du vill överföra din prenumeration över olika länder.

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Jag är administratör på två konton. Kan jag överföra en prenumeration från en av mina konton till en annan?
Ja, du kan överföra prenumerationen mellan dina konton. Dina konton betraktas som konceptuellt för två olika användare så att du kan använda ovanstående steg för att överföra prenumerationer mellan dina konton.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Resulterar det i en prenumerations överföring i drift avbrott i tjänsten?

Om du överför en prenumeration till ett konto i samma Azure AD-klient, påverkas inte resurserna som körs i prenumerationen. Om du däremot överför prenumerationen till ett konto i en annan klient och bestämmer dig för att flytta prenumerationen till klienten, kommer alla användare, grupper och tjänst huvud namn som har [rollbaserad åtkomst (RBAC)](../role-based-access-control/overview.md) att hantera resurser i prenumerationen att förlora sin åtkomst . Detta kan resultera i avbrott i tjänsten.

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>Har användare i det nya kontot åtkomst till användnings-och fakturerings historik?

Den enda information som är tillgänglig för användare i det nya kontot är den senaste månadens kostnad för din prenumeration. Resten av användnings-och fakturerings historiken överförs inte med prenumerationen

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Hur gör jag för att migrera data och tjänster för min Azure-prenumeration till en ny prenumeration?

Om du inte kan överföra ägarskapet för prenumerationen kan du migrera dina resurser manuellt. Se [Flytta resurser till ny resurs grupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Om jag överför en Visual Studio-eller Microsoft Partner Network-prenumeration, bär min kredit att vidarebefordra med prenumerationen på det nya kontot?

Nej, din kredit är inte tillgänglig i det nya kontot. Den användare som accepterar överförings förfrågan måste ha en Visual Studio-licens för att godkänna överföringsbegäran. Prenumerationen använder Visual Studio-krediten som är tillgänglig i användarens konto. Mer information finns i [överföra Visual Studio, Microsoft Partner Network (MPN) och betala per användning för utveckling/testning](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions)av prenumerationer.


## <a name="frequently-asked-questions-faq-for-recipients"></a>Vanliga frågor och svar (FAQ) för mottagare

Dessa vanliga frågor gäller användare, som accepterar fakturerings ägandet av en Azure-prenumeration från ett annat konto.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Om jag tar över fakturerings ägarskapet för en prenumeration från ett annat konto, fortsätter användare i kontot att ha åtkomst till mina resurser?

Ja. Men om ditt konto finns i en Azure AD-klient som skiljer sig från prenumerationens klient och den användare som skickade överföringsbegäran flyttar prenumerationen till kontots klient organisation, eventuella [Administratörs roller](billing-add-change-azure-subscription-administrator.md) och [ROLLBASERAD Access Control (RBAC) ](../role-based-access-control/role-assignments-portal.md)tilldelningar tas bort. Använd följande steg om du vill visa användare som har åtkomst till [rollbaserad åtkomst (RBAC)](../role-based-access-control/overview.md) för att hantera resurser i prenumerationen:

1. Besök [prenumerations sidan i Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration som du vill kontrol lera och välj sedan **åtkomst kontroll (IAM)** i den vänstra rutan.
1. Välj **roll tilldelningar** överst på sidan. På sidan roll tilldelningar visas alla användare som har RBAC-åtkomst i prenumerationen.

Även om [rollbaserade Access Control-tilldelningar (RBAC)](../role-based-access-control/role-assignments-portal.md) tas bort under överföringen kan användare i det ursprungliga ägar kontot fortfarande ha åtkomst till prenumerationen via vissa säkerhetsmekanismer, inklusive:

* Hanterings certifikat som ger användar administratörs behörighet till prenumerations resurser. Mer information finns i [skapa och ladda upp ett hanterings certifikat för Azure](../cloud-services/cloud-services-certs-create.md).
* Åtkomst nycklar för tjänster som lagring. Mer information finns i [Om Azure Storage-konton](../storage/common/storage-create-storage-account.md).
* Autentiseringsuppgifter för fjärråtkomst för tjänster som Azure Virtual Machines.

Om mottagaren behöver begränsa åtkomsten till resurserna bör de överväga att uppdatera alla hemligheter som är kopplade till tjänsten. De flesta resurser kan uppdateras med hjälp av följande steg:

  1. Logga in på [Azure Portal](https://portal.azure.com).
  2. Välj **alla resurser**på menyn hubb.
  3. Välj resursen.
  4. På sidan resurs klickar du på **Inställningar**. Här kan du Visa och uppdatera befintliga hemligheter.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Måste jag betala för hela fakturerings perioden om jag tar över fakturerings ägandet för en prenumeration i mitten av fakturerings perioden?

Ditt konto ansvarar för betalning för all användning som rapporteras från överförings tiden. Det kan finnas en viss användning som ägde rum före överföringen men som rapporter ATS efteråt. Användningen ingår i kontots faktura.

### <a name="can-i-use-a-different-payment-method"></a>Kan jag använda en annan betalnings metod?

Ja. När du godkänner överförings förfrågan kan du välja en befintlig betalnings metod som är kopplad till ditt konto eller lägga till en ny betalnings metod.

## <a name="troubleshooting"></a>Felsökning

### <a id="no-button"></a>Varför visas inte knappen "överför prenumeration"?

Prenumerations överföringen via självbetjäning är inte tillgänglig för ditt fakturerings konto. För närvarande stöder vi inte överföring av fakturerings ägarskapet för prenumerationer i Enterprise-avtal-konton (EA) i Azure Portal. Dessutom stöder inte Microsoft kund avtals konton som skapas när du arbetar med en Microsoft-representant överföring av fakturerings ägande. 

### <a id="no-button"></a>Varför stöder inte prenumerations typen överföring? 

Alla typer av prenumerationer stöder inte överföring av ägarskaps ägarskap. Om du vill visa en lista över prenumerations typer som stöder överföringar, se [prenumerations typer som stöds](#supported-subscription-types)

### <a id="no-button"></a>Varför får jag ett fel meddelande om nekad åtkomst när jag försöker överföra fakturerings ägarskapet för en prenumeration? 

Du ser det här felet om du försöker överföra en prenumeration på Microsoft Azure plan och du inte har den behörighet som krävs. Om du vill överföra en prenumeration på Microsoft Azure plan måste du vara ägare eller deltagare i faktura avsnittet som prenumerationen faktureras till. Mer information finns i [avsnittet hantera prenumerationer för faktura](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en support förfrågan](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Granska och uppdatera tjänst administratör, medadministratörer och andra RBAC-roller. Läs mer i [lägga till eller ändra Azure-prenumerations administratörer](billing-add-change-azure-subscription-administrator.md) och [Hantera åtkomst med RBAC och Azure Portal](../role-based-access-control/role-assignments-portal.md).
