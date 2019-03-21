---
title: Överföra ägarskap för Azure-prenumeration till ett annat konto | Microsoft Docs
description: Beskriver hur du överför en Azure-prenumeration till en annan användare och några vanliga frågor (och svar FAQ) om processen
keywords: överföra prenumerationen för överföring av azure-prenumeration, azure, flytta azure-prenumeration till ett annat konto, azure ändra Prenumerationens ägare, överföra azure-prenumeration till ett annat konto
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6d8baa5f10aeba179ef141cfea86fded8cf44dfc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878218"
---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Överföra ägarskapet för en Azure-prenumeration till ett annat konto

Överföra prenumerationen till en annan användare i Kontocentret för att ändra kontoadministratör och överlämna faktureringsägarskapet för prenumerationen. Om du vill ändra din prenumeration till ett annat erbjudande [växla din Azure-prenumeration till ett annat erbjudande](billing-how-to-switch-azure-offer.md).

> [!IMPORTANT]
> 
> Om du överför en prenumeration på en ny Azure AD-klient, alla rolltilldelningar i [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) tas bort permanent från käll-klient och migreras inte till Målklienten.

## <a name="transfer-ownership-of-an-azure-subscription"></a>Överföra ägarskapet för en Azure-prenumeration

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Logga in på [Azure Kontocenter](https://account.windowsazure.com/Subscriptions) som kontoadministratör Om du vill ta reda på vem som är kontoadministratör för prenumerationen kan du se [vanliga frågor och svar](#faq).

1. Välj prenumerationen du överför.

1. Kontrollera att din prenumeration är berättigad för självbetjäning överföring genom att markera den **erbjuder** och **erbjudande-ID** med den [stöds erbjudanden lista](#supported).

   ![Kontrollera erbjudande-ID för prenumerationen i Kontocenter](./media/billing-subscription-transfer/image0.png)
1. Klicka på **överföra äganderätten till prenumerationen**.

   ![Prenumerationsfliken i Azure-konto](./media/billing-subscription-transfer/image1.png)
1. Ange mottagaren.

   > [!IMPORTANT]
   > 
   > Om du överför en prenumeration på en ny Azure AD-klient, alla rolltilldelningar i [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) tas bort permanent från käll-klient och migreras inte till Målklienten.

   ![Överför prenumeration dialogrutan](./media/billing-subscription-transfer/image2.PNG)

1. Mottagaren hämtar automatiskt ett e-postmeddelande med en länk för godkännande.

   ![Prenumeration överföring e-postmeddelande till mottagare](./media/billing-subscription-transfer/image3.png)
1. Mottagaren klickar på länken och följer instruktionerna, inklusive att ange sin betalningsinformation.

   ![Första prenumeration överföring-webbsida](./media/billing-subscription-transfer/image4.png)

   ![Webbsida för överföring till andra prenumeration](./media/billing-subscription-transfer/image5.png)
1. Lyckades! Prenumerationen överförs nu.

<a id="EA"></a>

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>Överföra äganderätten till prenumerationen för kunder med Enterprise Agreement (EA)

Enterprise-administratören kan överföra ägarskapet för prenumerationer i en registrering. Kom igång genom att se [överföra ägarskap för kontot](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) i EA-portalen.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Nästa steg efter att du godkänt ägarskapet för prenumerationen

1. Du är nu kontoadministratör Granska och uppdatera tjänstadministratören och Medadministratörer andra RBAC-roller. Mer information finns i [Lägg till eller ändra Azure-prenumerationsadministratörer](billing-add-change-azure-subscription-administrator.md) och [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md).
1. Uppdatera autentiseringsuppgifter som är associerade med den här prenumerationens tjänster, inklusive:
   1. Av hanteringscertifikat som och ger användaren administratörsbehörighet till prenumerationsresurser. Mer information finns i [skapa och ladda upp ett certifikat för Azure](../cloud-services/cloud-services-certs-create.md)
   1. Snabbtangenter för tjänster som lagring. Mer information finns i [om Azure storage-konton](../storage/common/storage-create-storage-account.md)
   1. Fjärrautentiseringsuppgifter åtkomst för tjänster som Azure Virtual Machines. 
1. Om du arbetar med en partner Överväg gå att uppdatera partner-ID för den här prenumerationen. Du kan uppdatera partner-ID i den [Azure-portalen](https://portal.azure.com).

<a id="supported"></a>

## <a name="whats-supported"></a>Vad som stöds:

Prenumerationsöverföring med självbetjäning är tillgänglig för erbjudanden och prenumerationstyper som visas i följande tabell. För närvarande du kan inte överföra en kostnadsfri utvärderingsversion eller [Azure i Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) prenumerationer. Du hittar en lösning i [flytta resurser till ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md). Om du vill överföra andra prenumerationer, som [sponsring](https://azure.microsoft.com/offers/ms-azr-0036p/) eller supportavtal, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

| Erbjudandets namn                                                                             | Erbjudandets nummer |
|----------------------------------------------------------------------------------------|--------------|
| [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*|MS-AZR-0017P        |
| [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)          | MS-AZR-0025P        |
| [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p/)                     | MS-AZR-0062P        |
| [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/)                      | MS-AZR-0003P        |
| [Betala per användning – utveckling/testning](https://azure.microsoft.com/offers/ms-azr-0023p/)             | MS-AZR-0023P        |
| [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)           | MS-AZR-0063P        |
| [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P        |
| [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)         | MS-AZR-0059P        |
| [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)    | MS-AZR-0060P        |

\* [Via EA-portalen](#EA)

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

### <a name="whoisaa"></a> Vem är kontoadministratör för prenumerationen?

Kontoadministratören är den person som har registrerat sig för eller har köpt Azure-prenumeration. De har behörighet att komma åt den [Kontocenter](https://account.azure.com/Subscriptions) och utföra olika hanteringsuppgifter som att skapa prenumerationer, avbryta prenumerationer, ändra faktureringen för en prenumeration eller ändra tjänstadministratör. Om du inte vet som är kontoadministratör för en prenumeration, tar du reda på det med hjälp av följande steg.

1. Gå till [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration som du vill kontrollera och tittar sedan under **inställningar**.
1. Välj **egenskaper**. Kontoadministratör för prenumerationen visas i den **kontoadministratören** box.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Allt överföra? Inklusive resursgrupper, virtuella datorer, diskar och andra tjänster som körs?

Alla resurser som virtuella datorer, diskar och webbplatser överföring till den nya ägaren. Men alla [administratörsroller](billing-add-change-azure-subscription-administrator.md) och [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md) principer som du har konfigurerat överförs inte över olika kataloger. Dessutom [appregistreringar](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) och andra tjänster för klientspecifik överförs inte tillsammans.

### <a id="no-button"></a> Varför visas inte knappen ”överföra prenumerationen”?

Prenumerationsöverföring med självbetjäning är tyvärr inte tillgängligt för ditt erbjudande eller ditt land. Att överföra din prenumeration [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Resulterar en överföring av prenumeration i något avbrott av tjänsten?

Det finns ingen inverkan på tjänsten. Överför prenumerationen avbryter prenumerationen under det aktuella kontot Administratör och skapar en prenumeration under mottagarens konto. Den nya prenumerationen är kopplad till de underliggande Azure-tjänsterna. Prenumerations-ID förblir densamma.

### <a name="how-do-i-use-this-process-to-change-the-directory-for-subscription"></a>Hur kan jag använda den här processen för att ändra katalogen för prenumeration?

En Azure-prenumeration har skapats i den katalog som kontoadministratör tillhör. Om du vill ändra katalog, överföra prenumerationen till ett användarkonto i målkatalogen. När användaren slutför stegen för att acceptera överföringen, flyttas automatiskt prenumerationen till målkatalogen.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-organization-do-they-continue-to-have-access-to-my-resources"></a>Om jag tar över faktureringsägarskapet för en prenumeration från en annan organisation de fortsätter att ha åtkomst till mina resurser?

Om prenumerationen överförs till en annan klientorganisation, förlora användare som är associerade med den tidigare klienten åtkomst till prenumerationen. Även om en användare inte är en tjänstadministratör eller medadministratör längre, kan de fortfarande ha åtkomst till prenumerationen via andra sätt, inklusive:

* Av hanteringscertifikat som och ger användaren administratörsbehörighet till prenumerationsresurser. Mer information finns i [skapa och ladda upp ett Hanteringscertifikat för Azure](../cloud-services/cloud-services-certs-create.md).
* Snabbtangenter för tjänster som lagring. Mer information finns i [Om Azure Storage-konton](../storage/common/storage-create-storage-account.md).
* Fjärrautentiseringsuppgifter åtkomst för tjänster som Azure Virtual Machines.

Om mottagaren behöver att begränsa åtkomsten till sina resurser, bör de uppdatera alla hemligheter som är kopplade till tjänsten. De flesta resurser kan uppdateras med hjälp av följande steg:

  1. Gå till [Azure-portalen](https://portal.azure.com).
  2. På navmenyn väljer **alla resurser**.
  3. Välj resursen.
  4. Resursbladet klickar du på **inställningar**. Här kan du visa och uppdatera befintliga hemligheter.

### <a name="if-i-transfer-the-subscription-in-the-middle-of-the-billing-cycle-does-the-recipient-pay-for-the-entire-billing-cycle"></a>Om jag överföra prenumerationen mitt faktureringsperioden mottagarens betalar för hela faktureringen cykel.

Avsändaren ansvarar för betalningen för eventuell användning som rapporterades fram till att överföringen är klar. Mottagaren är ansvarig för användning som rapporteras från tidpunkten för överföring och senare. Det kan finnas vissa användning som ägde rum före överföringen men rapporterades efteråt. Användningen är inkluderat i mottagarens faktura.

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>Mottagaren hanteringskonfigurationstjänsten har tillgång till användnings- och faktureringshistorik?

  Den enda informationen som är tillgängliga för mottagaren är den senaste fakturan eller om prenumerationen har överförts innan första fakturan har genererats Aktuellt saldo. Resten av användnings- och faktureringshistorik överförs inte med prenumerationen.

### <a name="can-the-offer-be-changed-during-a-transfer"></a>Erbjudandet kan ändras under en överföring?

Erbjudandet måste vara samma. Om du vill ändra ditt erbjudande, se [växla din Azure-prenumeration till ett annat erbjudande](billing-how-to-switch-azure-offer.md).

### <a name="can-i-transfer-a-subscription-to-a-user-account-in-another-country"></a>Kan jag överföra en prenumeration till ett användarkonto i ett annat land?

Nej, överföra en prenumeration till ett användarkonto i ett annat land stöds inte. Mottagarens konto måste vara i samma land.

### <a name="can-the-recipient-use-a-different-payment-method"></a>Mottagaren kan använda en annan betalningsmetod?

Ja. Men prenumerations faktureringshistorik delas upp mellan två konton.  

### <a name="is-the-payment-method-impacted-after-i-transferred-an-azure-subscription"></a>Påverkas betalningsmetoden när jag överförde en Azure-prenumeration?

För att godkänna en prenumerationsöverföring, anges ett kreditkort eller liknande betalningsmetod för att betala för prenumerationen. Till exempel om Bob överför en prenumeration till Jane och Jane accepterar överföringen, ange Jane en betalningsmetod till betala för prenumerationen. När överföringen är klar, debiteras Jane för prenumerationen inte Bob.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Hur migrerar data och tjänster för min Azure-prenumeration till ny prenumeration?

Om du inte kan överföra äganderätten till prenumerationen kan du migrera dina resurser manuellt. Se [flytta resurser till ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).