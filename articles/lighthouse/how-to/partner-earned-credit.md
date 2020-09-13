---
title: Länka ditt partner-ID för att aktivera intjänad partner kredit på delegerade resurser
description: Lär dig hur du associerar ditt partner-ID för att få partner intjänad kredit (PEC) för kund resurser som du hanterar via Azure Lighthouse.
ms.date: 09/04/2020
ms.topic: how-to
ms.openlocfilehash: 0a9e7f51e90b38bad24eada5a665ca60bf43452f
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89493376"
---
# <a name="link-your-partner-id-to-enable-partner-earned-credit-on-delegated-resources"></a>Länka ditt partner-ID för att aktivera intjänad partner kredit på delegerade resurser

Om du är medlem i [Microsoft Partner Network](https://partner.microsoft.com/)kan du länka ditt partner-ID med de autentiseringsuppgifter som används för att hantera delegerade kund resurser. På så sätt kan du spåra din påverkan på kund engagemang och få [partner intjänade krediter för hanterade tjänster (PEC)](/partner-center/partner-earned-credit).

Om du registrerar [kunder med hanterade tjänst erbjudanden i Azure Marketplace](publish-managed-services-offers.md)sker den här länken automatiskt med hjälp av det MPN-ID som är kopplat till det Partner Center-konto som används för att publicera erbjudandena. Ingen ytterligare åtgärd krävs för att kunna ta emot PEC för dessa kunder.

Om du registrerar [kunder med hjälp av Azure Resource Management-mallar](onboard-customer.md)måste du vidta åtgärder för att skapa den här länken. Detta görs genom att [du länkar ditt MPN-ID](../../cost-management-billing/manage/link-partner-id.md) med minst ett användar konto i din hanterings klient som har åtkomst till var och en av dina inbyggda prenumerationer.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Associera ditt partner-ID när du har publicerat nya kunder

När du registrerar kunder via Azure Resource Manager-mallar kan du använda följande process för att länka ditt partner-ID och aktivera partner intjänad kredit. Du måste känna till ditt [MPN-partner-ID](/partner-center/partner-center-account-setup#locate-your-mpn-id) för att kunna utföra dessa steg.

För enkelhetens skull rekommenderar vi att du skapar ett tjänst huvud konto i din klient organisation, kopplar det till ditt MPN-ID och sedan beviljar det åtkomst till varje kund som du har registrerat med en [inbyggd Azure-roll som är berättigad till PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

1. [Skapa ett tjänst huvud konto](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) i hanterings klienten. I det här exemplet ska vi namnge det här tjänstens huvud konto för PEC-Automation.
1. Med hjälp av tjänstens huvud konto [länkar du till ditt partner-ID](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) i hanterings klienten. Du behöver bara göra detta en gången.
1. När du registrerar [en kund som använder Azure Resource Manager mallar](onboard-customer.md)måste du ta med en auktorisering som innehåller PEC Automation-kontot som en användare med en [inbyggd Azure-roll som är berättigad till PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

Genom att följa dessa steg associeras alla kund innehavare som du hanterar med ditt partner-ID, så att du kan få PEC för dessa kunder. PEC Automation-kontot behöver inte autentisera eller utföra några åtgärder i kundens klient organisation.

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>Lägg till ditt partner-ID till tidigare registrerade kunder

Om du redan har registrerat en kund kanske du inte vill utföra en annan distribution för att lägga till tjänstens huvud namn för PEC Automation-kontot. I stället kan du associera MPN-ID: t med ett användar konto som redan har åtkomst att fungera i den kundens klient organisation. Se till att kontot har fått en [inbyggd Azure-roll som är berättigad till PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

När kontot har [länkats till ditt partner-ID](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) i hanterings klienten kan du få PEC för kunden.

## <a name="confirm-partner-earned-credit"></a>Bekräfta den intjänade partner krediten

Du kan [Visa PEC-information i Azure Portal](/partner-center/partner-earned-credit-explanation#azure-cost-management) och bekräfta vilka kostnader som har fått fördelen med PEC.

Om du har följt stegen ovan och inte ser associationen öppnar du en supportbegäran i Azure Portal.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att ansluta till [Microsoft Partner Network](/partner-center/mpn-overview).
- Lär dig [hur PEC beräknas och betalas](/partner-center/partner-earned-credit-explanation).
- [Publicera kunder](onboard-customer.md) till Azure-Lighthouse.
