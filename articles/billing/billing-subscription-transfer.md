---
title: "Överföra ägarskap för Azure-prenumeration till ett annat konto | Microsoft Docs"
description: "Beskriver hur du överför en Azure-prenumeration till en annan användare och några vanliga frågor (FAQ) om processen"
keywords: "överföra prenumerationen för överföring av azure-prenumeration, azure, gå azure-prenumeration till ett annat konto, azure ändra prenumerationen ägare och överföra azure-prenumeration till ett annat konto"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/15/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9beef44b3fbaf26d49757544f32b97c7ef2cf425
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Överför ägarskapet för en Azure-prenumeration till ett annat konto

Du kan överföra prenumerationen till en annan användare mitt konto. Använd denna funktion för att räcka över prenumeration fakturering ägarskap till någon annan, ändra konto för inloggning eller flytta prenumerationen till en annan katalog. Om du vill ändra prenumerationen till ett annat erbjudande [växla din Azure-prenumeration till ett annat erbjudande](billing-how-to-switch-azure-offer.md).

> [!IMPORTANT]
> 
> För närvarande stöds inte prenumerationsöverföringar för en kostnadsfri utvärderingsversion eller [Azure i Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) prenumerationer. En lösning finns [flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

<a id="supported"></a>

## <a name="whats-supported"></a>Det här stöds:

Självbetjäning prenumerationsöverföring är tillgänglig för erbjudanden och prenumeration typ som anges i följande tabell. För att överföra andra prenumerationer som [sponsring](https://azure.microsoft.com/offers/ms-azr-0036p/) eller stöder planer, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

| Erbjudandets namn                                                                             | Erbjudandets nummer |
|----------------------------------------------------------------------------------------|--------------|
| [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*|MS-AZR - 0017P        |
| [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)          | MS-AZR - 0025P        |
| [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p/)                     | MS-AZR - 0062P        |
| [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/)                      | MS-AZR - 0003P        |
| [Betala per användning utveckling och testning](https://azure.microsoft.com/offers/ms-azr-0023p/)             | MS-AZR - 0023P        |
| [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)           | MS-AZR - 0063P        |
| [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR - 0064P        |
| [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)         | MS-AZR - 0059P        |
| [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)    | MS-AZR - 0060P        |

\*[Via EA-portalen](#EA)

## <a name="transfer-ownership-of-an-azure-subscription"></a>Överför ägarskapet för en Azure-prenumeration

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Logga in på [Azure Kontocenter](https://account.windowsazure.com/Subscriptions) som kontoadministratör. Om du vill veta vem som är kontoadministratör för prenumerationen [vanliga frågor och](#faq).

1. Välj prenumerationen för att överföra.

1. Kontrollera att din prenumeration är berättigad för självbetjäning överföring genom att kontrollera den **erbjuder** och **erbjuder ID** med den [stöds erbjudanden listan](#supported).

   ![Kontrollera erbjudande-ID för prenumerationen i Account Center](./media/billing-subscription-transfer/image0.png)
1. Klicka på **överföra äganderätten till prenumerationen**.

   ![Fliken abonnemang för Azure-konto](./media/billing-subscription-transfer/image1.png)
1. Ange mottagaren.

   ![Överföra prenumerationen dialogrutan](./media/billing-subscription-transfer/image2.PNG)
1. Mottagaren hämtar automatiskt ett e-postmeddelande med en länk för godkännande.

   ![E-post i prenumerationen överföring till mottagare](./media/billing-subscription-transfer/image3.png)
1. Mottagaren klickar på länken och följer instruktionerna, inklusive att ange sin betalningsinformation.

   ![Prenumerationen överföring webbsida](./media/billing-subscription-transfer/image4.png)

   ![Webbsida för överföring till andra prenumeration](./media/billing-subscription-transfer/image5.png)
1. Lyckades! Prenumerationen överförs nu.

<a id="EA"></a>

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>Överför ägarskapet för prenumerationen för kunder med Enterprise-avtal (EA)

Företagsadministratören kan överföra ägarskap för prenumerationer inom en registrering. Om du vill komma igång finns [överföra ägarskap för kontot](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) i EA-portalen.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Nästa steg efter att acceptera ägarskap för en prenumeration

1. Du är nu kontoadministratör. Granska och uppdatera tjänstadministratören och Medadministratörer. Hantera administratörer i den [klassiska Azure-portalen](https://manage.windowsazure.com) genom att gå till inställningar. [Lär dig mer om administratörsroller](billing-add-change-azure-subscription-administrator.md).
1. Du kan också använda rollbaserad åtkomstkontroll (RBAC) för din prenumeration och tjänster. Besök [Azure Portal](https://portal.azure.com). [Mer information om RBAC](../active-directory/role-based-access-control-configure.md)
1. Uppdatera autentiseringsuppgifterna som associeras med den här prenumerationens tjänster, inklusive:
   1. Av hanteringscertifikat som beviljar användaren administratörsrättigheter att prenumerationsresurser. Mer information finns i [skapa och ladda upp ett certifikat för Azure](../cloud-services/cloud-services-certs-create.md)
   1. Snabbtangenter för tjänster som lagring. Mer information finns i [om Azure storage-konton](../storage/common/storage-create-storage-account.md)
   1. Remote Access-autentiseringsuppgifter för tjänster som Azure virtuella datorer. 
1. [Uppdatera fakturaaviseringar för den här prenumerationen](billing-set-up-alerts.md) på den [Azure Kontocenter](https://account.windowsazure.com/Subscriptions). 
1. Om du arbetar med en partner Överväg att uppdatera partner-ID för den här prenumerationen. Du kan uppdatera partner-ID i den [Azure Kontocenter](https://account.windowsazure.com/Subscriptions).

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

### <a name="whoisaa"></a>Vem är kontoadministratör för prenumerationen?

Kontoadministratören är den person som registrerade sig för eller har köpt Azure-prenumerationen. De har behörighet att komma åt den [Kontocenter](https://account.azure.com/Subscriptions) och utföra olika hanteringsuppgifter som att skapa prenumerationer, avbryta prenumerationer, ändra faktureringen för en prenumeration eller ändra den tjänstadministratör. Om du inte vet som kontoadministratör för en prenumeration, kan du använda följande steg för att ta reda på.

1. Besök den [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration som du vill kontrollera och se **inställningar**.
1. Välj **egenskaper**. Kontoadministratören för prenumerationen visas i den **kontoadministratören** rutan.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Överförs allt? Inklusive resursgrupper, virtuella datorer, diskar och andra tjänster som körs?

Ja, alla resurser som virtuella datorer, diskar och webbplatser överföring till den nya ägaren. Men alla [administratörsroller](billing-add-change-azure-subscription-administrator.md) och [rollbaserad åtkomstkontroll (RBAC)](../active-directory/role-based-access-control-configure.md) principer som du har konfigurerat överför inte i olika kataloger.

### <a id="no-button"></a>Varför visas inte knappen ”överföra prenumerationen”?

Självbetjäning prenumerationsöverföring är tyvärr inte tillgänglig för ditt erbjudande eller land. Att överföra din prenumeration, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Resulterar en prenumerationsöverföring i avbrott i tjänsten?

Det finns ingen inverkan på tjänsten. Överföra prenumerationen avbryter prenumerationen under det aktuella kontot Administratör och skapar en prenumeration under mottagarens konto. Den nya prenumerationen är kopplad till de underliggande Azure-tjänsterna. Prenumerations-ID är densamma.

### <a name="how-do-i-use-this-process-to-change-the-directory-for-subscription"></a>Hur använder den här processen för att ändra katalogen för prenumerationen?

En Azure-prenumeration har skapats i den katalog som kontoadministratör tillhör. Om du vill ändra katalogen överföra prenumerationen till ett användarkonto i målkatalogen. När användaren har slutfört stegen för att godkänna överföring, flyttas automatiskt prenumerationen till målkatalogen.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-organization-do-they-continue-to-have-access-to-my-resources"></a>Om jag tar över faktureringsägarskapet för en prenumeration från en annan organisation måste de fortsätter att ha åtkomst till mina resurser?

Om prenumerationen överförs till en annan innehavare förlora de användare som associeras med den tidigare innehavaren åtkomst till prenumerationen. Även om en användare inte är en tjänstadministratör eller en medadministratör längre, kan de fortfarande ha tillgång till prenumerationen med andra säkerhetsmekanismer, inklusive:

* Av hanteringscertifikat som beviljar användaren administratörsrättigheter att prenumerationsresurser. Mer information finns i [skapa och ladda upp ett Hanteringscertifikat för Azure](../cloud-services/cloud-services-certs-create.md).
* Snabbtangenter för tjänster som lagring. Mer information finns i [om Azure storage-konton](../storage/common/storage-create-storage-account.md).
* Remote Access-autentiseringsuppgifter för tjänster som Azure virtuella datorer.

Om mottagaren behöver begränsa åtkomsten till sina resurser bör uppdatera alla hemligheter som är associerad med tjänsten. De flesta resurser kan uppdateras med hjälp av följande steg:

  1. Gå till [Azure-portalen](https://portal.azure.com).
  2. På navmenyn väljer **alla resurser**.
  3. Välj resursen.
  4. I resursbladet klickar du på **inställningar**. Här kan du visa och uppdatera befintliga hemligheter.

### <a name="if-i-transfer-the-subscription-in-the-middle-of-the-billing-cycle-does-the-recipient-pay-for-the-entire-billing-cycle"></a>Om jag överföra prenumerationen mitt faktureringsperioden mottagaren betala för hela fakturering cykel.

Avsändaren ansvarar för betalning för användning som rapporterades fram till att överföringen är klar. Mottagaren är ansvarig för användning som rapporterats från tiden för överföringen och senare. Det kan finnas vissa användningen som har ägt rum före överföringen men rapporterades efteråt. Användningen är inkluderad i mottagarens faktura.

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>Mottagaren ha åtkomst till användnings- och faktureringshistorik?

  Den enda informationen som är tillgängliga för mottagaren är den sista faktura eller om prenumerationen överförs innan den första fakturan har genererats Aktuellt saldo. Resten av användnings- och faktureringshistorik överför inte med prenumerationen.

### <a name="can-the-offer-be-changed-during-a-transfer"></a>Kan erbjudandet ändras under en överföring?

Erbjudandet måste vara samma. Om du vill ändra erbjudandet [växla din Azure-prenumeration till ett annat erbjudande](billing-how-to-switch-azure-offer.md).

### <a name="can-i-transfer-a-subscription-to-a-user-account-in-another-country"></a>Kan jag överföra en prenumeration till ett användarkonto i ett annat land?

Nej, överföra en prenumeration till ett användarkonto i ett annat land stöds inte. Mottagarens användarkontot måste vara i samma land.

### <a name="can-the-recipient-use-a-different-payment-method"></a>Mottagaren kan använda en annan betalningsmetod?

Ja. Men prenumeration faktureringshistorik delas upp mellan två konton.  

### <a name="is-the-payment-method-impacted-after-i-transferred-an-azure-subscription"></a>Påverkas betalningsmetoden när jag överförde en Azure-prenumeration?

Om du accepterar en prenumerationsöverföring måste ett kreditkort eller liknande betalningsmetod anges för prenumerationen. Om Daniel överför en prenumeration till Jane och Jane accepterar överföringen, måste Jane ange en annan betalningsmetod betala för prenumerationen. När överföringen är klar, debiteras Jane för prenumerationen inte Bob.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Hur migrerar data och tjänster för min Azure-prenumeration till ny prenumeration?

Om du inte kan överföra ägarskapet för prenumerationen kan du manuellt migrera dina resurser. Se [flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.