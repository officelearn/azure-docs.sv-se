---
title: Hantera användare
description: Beskriver hur du hanterar användare
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 37dacf0adfc9e3dcbab963cef50e2ee5209df314
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852359"
---
# <a name="manage-users"></a>Hantera användare

Azure FarmBeats innehåller användar hantering för personer som är en del av din Azure Active Directory (Azure AD). Du kommer att kunna lägga till användare till din Azure FarmBeats-instans för att få åtkomst till API: erna, Visa de genererade kartorna och sensorn för telemetri från Server gruppen

## <a name="prerequisites"></a>Krav

- En Azure FarmBeats-distribution krävs. Mer information om hur du ställer in Azure-FarmBeats finns i [distribuera FarmBeats](prepare-for-deployment.md) .
- E-post-ID för de användare som du vill lägga till eller ta bort från din Azure FarmBeats-instans.

## <a name="manage-azure-farmbeats-users"></a>Hantera Azure FarmBeats-användare

Azure FarmBeats använder Azure AD för autentisering, åtkomst kontroll och roller. Användare i Azure AD-klienten kan läggas till som användare i Azure-FarmBeats.

> [!NOTE]
> Om användaren som du försöker lägga till inte finns i Azure AD-klienten följer du anvisningarna i avsnittet **Lägg till Azure AD-användare** för att slutföra installationen, innan du fortsätter med att ställa in dem som Azure FarmBeats-användare.

**roles**

Det finns två typer av användar roller som stöds i Azure dataFarmBeatss idag:

 - **Admin** – all åtkomst till Azure FarmBeats-API: er för data hubb. Användare med den här rollen kan fråga alla data Hubbs objekt i Azure FarmBeats, utföra alla åtgärder från FarmBeats-acceleratorn.
 - **Skrivskyddad** – skrivskyddad åtkomst till FarmBeats-API: er för data hubb. Användare kan visa API: er för data hubb, Accelerator-instrumentpaneler och kartor. En användare med rollen "skrivskyddad" kommer inte att kunna utföra några åtgärder som att generera kartor, associera enheter eller skapa grupper.


## <a name="add-user-to-azure-farmbeats"></a>Lägg till användare i Azure-FarmBeats

För att lägga till en användare i Azure FarmBeats 
1.  Logga in på acceleratorn och klicka på ikonen Inställningar
2.  Klicka på Access Control

    ![Taktslag i projekt grupp](./media/create-farms/settings-users-1.png)

3.  Ange e-post-ID: t för den användare som du vill ge åtkomst till
4.  Välj önskad roll – administratör eller skrivskyddad
5.  Klicka på Lägg till roll

De tillagda användarna kommer nu att kunna komma åt Azure-FarmBeats (både data hubb och Accelerator).

## <a name="delete-user-from-azure-farmbeats"></a>Ta bort användare från Azure-FarmBeats

Om du vill ta bort en användare från Azure FarmBeats-systemet kan du
1.  Logga in på acceleratorn och klicka på ikonen Inställningar
2.  Klicka på Access Control
3.  Klicka på ikonen Ta bort nära e-postadressen för den användare som du vill ta bort

Användaren tas bort från systemet. Följande meddelande visas för att bekräfta att åtgärden har slutförts


![Taktslag i projekt grupp](./media/create-farms/manage-users-2.png)


## <a name="add-azure-ad-users"></a>Lägg till Azure AD-användare

> [!NOTE]
> Du måste utföra följande steg, endast om användaren som du försöker ge åtkomst till Azure-FarmBeats inte finns i Azure AD-klienten. Om användaren redan finns behöver du inte utföra stegen nedan

FarmBeats-användare måste finnas i Azure AD-klienten innan du kan tilldela dem till program och roller. Använd följande steg för att lägga till användare i Azure AD:
1.  Logga in på [Azure-portalen](https://portal.azure.com/).
2.  Välj ditt konto i det övre högra hörnet och växla till den Azure AD-klient som är kopplad till FarmBeats
3.  Välj **Azure Active Directory > användare**. Du ser en lista över användare i din katalog.
4.  Om du vill lägga till användare i katalogen väljer du **ny användare**. För externa användare väljer du **ny gäst användare**.

    ![Taktslag i projekt grupp](./media/create-farms/manage-users-3.png)

5.  Fyll i de obligatoriska fälten för den nya användaren. Välj **Skapa**.

Besök [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/) -dokumentationen om du vill ha mer information om hur du hanterar användare i Azure AD.

## <a name="next-steps"></a>Nästa steg

Du har distribuerat Azure-FarmBeats. Nu kan du lära dig hur du [skapar Server grupper](manage-farms.md#create-farms).

