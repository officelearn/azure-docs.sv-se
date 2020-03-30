---
title: Godkänna just-in-time-åtkomst
description: Beskriver hur konsumenter av Azure Managed Applications godkänner begäranden om just-in-time-åtkomst till ett hanterat program.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651025"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Konfigurera och godkänna just-in-time-åtkomst för Azure Managed Applications

Som konsument av ett hanterat program kanske du inte är bekväm med att ge utgivaren permanent åtkomst till den hanterade resursgruppen. Azure Managed Applications ger dig större kontroll över beviljandet av åtkomst till hanterade resurser, och tillhandahåller en funktion som kallas JIT-åtkomst (just-in-time), som för närvarande förhandsgranskas. Det gör att du kan godkänna när och hur länge utgivaren har åtkomst till resursgruppen. Utgivaren kan göra nödvändiga uppdateringar under den tiden, men när den tiden är över upphör utgivarens åtkomst att gälla.

Arbetsflödet för att bevilja åtkomst är:

1. Utgivaren lägger till ett hanterat program på marketplace och anger att JIT-åtkomst är tillgänglig.

1. Under distributionen aktiverar du JIT-åtkomst för din instans av det hanterade programmet.

1. Efter distributionen kan du ändra inställningarna för JIT-åtkomst.

1. Utgivaren skickar en begäran om åtkomst.

1. Du godkänner begäran.

Den här artikeln fokuserar på de åtgärder som konsumenterna vidtar för att aktivera åtkomst till gemensamma utredningsgrupper och godkänna begäranden. Mer information om hur du publicerar ett hanterat program med JIT-åtkomst finns [i Begär just-in-time-åtkomst i Azure Managed Applications](request-just-in-time-access.md).

> [!NOTE]
> Om du vill använda just-in-time-åtkomst måste du ha en [Azure Active Directory P2-licens](../../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Aktivera under distributionen

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Hitta en marketplace-post för ett hanterat program med JIT aktiverat. Välj **Skapa**.

1. Samtidigt som du tillhandahåller värden **JIT Configuration** för det nya hanterade programmet kan du aktivera eller inaktivera JIT-åtkomst för det hanterade programmet. Välj **Ja** för **aktivera JIT Access**. Det här alternativet är markerat som standard för hanterade program som definierats med JIT aktiverat på marketplace.

   ![Konfigurera åtkomst](./media/approve-just-in-time-access/configure-jit-access.png)

   Du kan bara aktivera JIT-åtkomst under distributionen. Om du väljer **Nej**får utgivaren permanent åtkomst till den hanterade resursgruppen. Du kan inte aktivera JIT-åtkomst senare.

1. Om du vill ändra standardinställningarna för godkännande väljer du **Anpassa JIT-konfiguration**.

   ![Anpassa åtkomst](./media/approve-just-in-time-access/customize-jit-access.png)

   Som standard har ett hanterat program med JIT aktiverat följande inställningar:

   * Godkännandeläge – automatiskt
   * Maximal åtkomsttid – 8 timmar
   * Godkännare – ingen

   När godkännandeläget är inställt på **automatisk**får godkännarna ett meddelande för varje begäran, men begäran godkänns automatiskt. När den är inställd på **manuell**får godkännarna ett meddelande för varje begäran och en av dem måste godkänna den.

   Den maximala varaktigheten för aktivering anger den maximala tid en utgivare kan begära åtkomst till den hanterade resursgruppen.

   Godkännare listan är Azure Active Directory-användare som kan godkänna JIT-åtkomstbegäranden. Om du vill lägga till en godkännare väljer du **Lägg till godkännare** och söker efter användaren.

   När du har uppdaterat inställningen väljer du **Spara**.

## <a name="update-after-deployment"></a>Uppdatera efter distribution

Du kan ändra värdena för hur begäranden godkänns. Men om du inte har aktiverat JIT-åtkomst under distributionen kan du inte aktivera den senare.

Så här ändrar du inställningarna för ett distribuerat hanterat program:

1. Välj hantera-programmet i portalen.

1. Välj **JIT-konfiguration** och ändra inställningarna efter behov.

   ![Ändra åtkomstinställningar](./media/approve-just-in-time-access/change-settings.png)

1. Välj **Spara** när du är klar.

## <a name="approve-requests"></a>Godkänn förfrågningar

När utgivaren begär åtkomst får du ett meddelande om begäran. Du kan godkänna JIT-åtkomstbegäranden antingen direkt via det hanterade programmet eller i alla hanterade program via Azure AD Privileged Identity Management-tjänsten. Om du vill använda just-in-time-åtkomst måste du ha en [Azure Active Directory P2-licens](../../active-directory/privileged-identity-management/subscription-requirements.md).

Så här godkänner du begäranden via det hanterade programmet:

1. Välj **JIT Access** för det hanterade programmet och välj **Godkänn begäranden**.

   ![Godkänn förfrågningar](./media/approve-just-in-time-access/approve-requests.png)
 
1. Välj den begäran som ska godkännas.

   ![Välj begäran](./media/approve-just-in-time-access/select-request.png)

1. Ange orsaken till godkännandet i formuläret och välj **Godkänn**.

Så här godkänner du begäranden via Azure AD Privileged Identity Management:

1. Välj **Alla tjänster** och börja söka efter Azure AD **Privileged Identity Management**. Välj det bland de tillgängliga alternativen.

   ![Sök efter tjänst](./media/approve-just-in-time-access/search.png)

1. Välj **Godkänn begäranden**.

   ![Välj godkänna begäranden](./media/approve-just-in-time-access/select-approve-requests.png)

1. Välj **Azure-hanterade program**och välj begäran att godkänna.

   ![Välj begäranden](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du publicerar ett hanterat program med JIT-åtkomst finns [i Begär just-in-time-åtkomst i Azure Managed Applications](request-just-in-time-access.md).
