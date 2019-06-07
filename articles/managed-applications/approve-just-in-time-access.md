---
title: Konfigurera och godkänna just-in-time-åtkomst för Azure Managed Applications
description: Beskriver hur användare av Azure Managed Applications godkänna förfrågningar för just-in-time-åtkomst till ett hanterat program.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 55fbf7292ab894cad3de3de9e96ddc96fe0b79b3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481721"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Konfigurera och godkänna just-in-time-åtkomst för Azure Managed Applications

Som konsumenter av ett hanterat program, kanske du inte redo att ge utgivaren permanent åtkomst till den hanterade resursgruppen. För att ge dig större kontroll över beviljar åtkomst till hanterade resurser, får Azure Managed Applications du en funktion som kallas just-in-time (JIT) åtkomst, vilket är en förhandsversion. Det gör att du kan godkänna när och hur lång tid utgivaren har åtkomst till resursgruppen. Utgivaren kan göra nödvändiga uppdateringar under den tiden, men när denna tid är över, utgivarens åtkomst går ut.

Arbetsflöde för att bevilja åtkomst är:

1. Utgivaren lägger till ett hanterat program på Marketplace och anger att JIT-åtkomst är tillgänglig.

1. Under distributionen kan aktivera du JIT-åtkomst för din instans av det hanterade programmet.

1. Efter distributionen kan ändra du inställningarna för JIT-åtkomst till.

1. Utgivaren skickar en begäran om åtkomst.

1. Du har godkänt förfrågan.

Den här artikeln fokuserar på de åtgärder som konsumenter vidta för att aktivera JIT-åtkomst och godkänna förfrågningar. Läs om hur du publicerar ett hanterat program med JIT-åtkomst i [begär just-in-time-åtkomst i Azure Managed Applications](request-just-in-time-access.md).

> [!NOTE]
> För att använda just-in-time-åtkomst, måste du ha en [Azure Active Directory P2-licens](../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Aktivera under distributionen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Hitta en marketplace-post för ett hanterat program med JIT aktiverad. Välj **Skapa**.

1. Få tillgång till värden för det nya hantera programmet den **JIT Configuration** steg kan du aktivera eller inaktivera JIT-åtkomst för det hanterade programmet. Välj **Ja** för **aktivera JIT-åtkomst till**. Det här alternativet väljs som standard för hanterade program som definierats med JIT aktiverad i marketplace.

   ![Konfigurera åtkomst](./media/approve-just-in-time-access/configure-jit-access.png)

   Du kan bara aktivera JIT-åtkomst till under distributionen. Om du väljer **nr**, utgivaren hämtar permanent åtkomst till den hanterade resursgruppen. Du kan inte aktivera JIT-åtkomst till senare.

1. Om du vill ändra standardinställningarna för godkännande, Välj **anpassa JIT Configuration**.

   ![Anpassa åtkomst](./media/approve-just-in-time-access/customize-jit-access.png)

   Ett hanterat program med JIT aktiverad har som standard följande inställningar:

   * Läge för administratörstillåtelse – automatisk
   * Varaktighet för maximal åtkomst – 8 timmar
   * Godkännare – ingen

   När läge för administratörstillåtelse är inställd på **automatisk**, godkännarna ta emot ett meddelande för varje begäran men begäran har godkänts automatiskt. När värdet **manuell**, godkännarna får ett meddelande för varje begäran, och en av dem måste godkänna den.

   Aktiveringens maximala varaktighet Anger längsta tid som en utgivare kan begära för åtkomst till den hanterade resursgruppen.

   Listan granskare är Azure Active Directory-användare som kan godkänna begäranden för JIT-åtkomst. Om du vill lägga till en godkännare, Välj **Lägg till godkännare** och söker efter användaren.

   När du har uppdaterat inställningen, Välj **spara**.

## <a name="update-after-deployment"></a>Uppdatera efter distributionen

Du kan ändra värdena för hur begäranden godkänns. Men om du inte aktiverar JIT-åtkomst till under distributionen kan aktivera du inte den senare.

Ändra inställningarna för ett hanterat distribuerade program:

1. Välj hantera programmet i portalen.

1. Välj **JIT Configuration** och ändra inställningarna efter behov.

   ![Ändra inställningar för åtkomst](./media/approve-just-in-time-access/change-settings.png)

1. Välj **Spara** när du är klar.

## <a name="approve-requests"></a>Godkänn förfrågningar

När utgivaren begär åtkomst, meddelas du i begäran. Du kan godkänna förfrågningar om JIT direkt via det hanterade programmet eller i alla hanterade program via Azure AD Privileged Identity Management-tjänsten. För att använda just-in-time-åtkomst, måste du ha en [Azure Active Directory P2-licens](../active-directory/privileged-identity-management/subscription-requirements.md).

Godkänna förfrågningar via det hanterade programmet:

1. Välj **JIT-åtkomst till** för det hanterade programmet och välj **godkänna begäranden**.

   ![Godkänn förfrågningar](./media/approve-just-in-time-access/approve-requests.png)
 
1. Välj begäran att godkänna.

   ![Välj begäran](./media/approve-just-in-time-access/select-request.png)

1. I formuläret, ange orsak för godkännande och välj **Godkänn**.

Godkänna förfrågningar via Azure AD Privileged Identity Management:

1. Välj **alla tjänster** och börja söka efter **Azure AD Privileged Identity Management**. Välj det bland de tillgängliga alternativen.

   ![Sök efter service](./media/approve-just-in-time-access/search.png)

1. Välj **godkänna förfrågningar**.

   ![Välj Godkänn ansökningar](./media/approve-just-in-time-access/select-approve-requests.png)

1. Välj **Azure hanterade program**, och välj begäran att godkänna.

   ![Välj ansökningar](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Nästa steg

Läs om hur du publicerar ett hanterat program med JIT-åtkomst i [begär just-in-time-åtkomst i Azure Managed Applications](request-just-in-time-access.md).
