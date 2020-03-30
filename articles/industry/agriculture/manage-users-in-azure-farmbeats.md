---
title: Hantera användare i Azure FarmBeats
description: I den här artikeln beskrivs hur du hanterar användare i Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 6ccc29422c6abf1120d13c05d10125efd0871ca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502061"
---
# <a name="manage-users"></a>Hantera användare

Azure FarmBeats innehåller användarhantering för personer som ingår i din Azure Active Directory -instans (Azure AD). Du kan lägga till användare i Azure FarmBeats-instansen för att komma åt API:erna, visa genererade kartor och komma åt sensortelemetri från servergruppen.

## <a name="prerequisites"></a>Krav

- Azure FarmBeats-installation krävs. Mer information finns i [Installera Azure FarmBeats](install-azure-farmbeats.md).
- E-post-ID:t för de användare som du vill lägga till eller ta bort från din Azure FarmBeats-instans.

## <a name="manage-azure-farmbeats-users"></a>Hantera Azure FarmBeats-användare

Azure FarmBeats använder Azure AD för autentisering, åtkomstkontroll och roller. Du kan lägga till användare i Azure AD-klienten som användare i Azure FarmBeats.

> [!NOTE]
> Om en användare inte är en Azure AD-klientanvändare följer du instruktionerna i avsnittet **Lägg till Azure AD-användare** för att slutföra installationen.

Azure FarmBeats stöder två typer av användarroller:

 - **Admin**: Fullständig åtkomst till Azure FarmBeats Datahub API:er. Användare i den här rollen kan fråga alla Azure FarmBeats Datahub-objekt och utföra alla åtgärder från FarmBeats Accelerator.
 - **Skrivskyddad:** Skrivskyddad åtkomst till FarmBeats Datahub API:er. Användare kan visa Datahub API:er, acceleratorinstrumentpanelerna och kartorna. Användare med skrivskyddad åtkomst kan inte utföra åtgärder som att generera kartor, associera enheter eller skapa grupper.

## <a name="add-users-to-azure-farmbeats"></a>Lägga till användare i Azure FarmBeats

Så här lägger du till användare i Azure FarmBeats:

1. Logga in på Accelerator och välj sedan ikonen **Inställningar.**
2. Välj **åtkomstkontroll**.

    ![Fönstret Inställningar för grupper](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Ange e-post-ID för den användare som du vill bevilja åtkomst till.
4. Välj önskad roll, **Admin** eller **Skrivskyddad**.
5. Välj **Lägg till roll**.

Den tillagda användaren kan nu komma åt Azure FarmBeats (både Datahub och Accelerator).

## <a name="delete-users-from-azure-farmbeats"></a>Ta bort användare från Azure FarmBeats

Så här tar du bort användare från Azure FarmBeats-systemet:

1. Logga in på Accelerator och välj sedan ikonen **Inställningar.**
2. Välj **åtkomstkontroll**.
3. Välj **Ta bort**.

   Användaren tas bort från systemet. Följande bekräftelsemeddelande visas:

   ![Bekräftelsemeddelande för Azure FarmBeats](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Lägga till Azure AD-användare

> [!NOTE]
> Azure FarmBeats-användare måste finnas i Azure AD-klienten innan du tilldelar dem till program och roller. Om en användare inte finns i Azure AD-klienten följer du instruktionerna i det här avsnittet. Hoppa över instruktionerna om en användare redan finns i Azure AD-klienten.

Följ stegen för att lägga till användare i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Längst upp till höger väljer du ditt konto och växlar sedan till Azure AD-klienten som är associerad med FarmBeats.
3. Välj Azure Active**Directory-användare** **Azure Active Directory** > .

    En lista över Azure AD-användare visas.

4. Om du vill lägga till en användare i katalogen väljer du **Ny användare**. Om du vill lägga till en extern användare väljer du **Ny gästanvändare**.

    ![Fönstret "Alla användare"](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Välj den nya användarens namn och fyll sedan i de obligatoriska fälten för den användaren.
6. Välj **Skapa**.

Information om hur du hanterar Azure AD-användare finns [i Lägga till eller ta bort användare i Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/).

## <a name="next-steps"></a>Nästa steg

Du har lagt till användare i din Azure FarmBeats-instans. Nu lär du dig att [skapa och hantera gårdar](manage-farms-in-azure-farmbeats.md#create-farms).
