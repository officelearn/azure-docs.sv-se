---
title: Lägga till en intern supportpolicy i ett labb i Azure DevTest Labs
description: Lär dig hur du publicerar en intern supportförklaring i ett labb i Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: c9900333-6c5e-4d7d-b0f4-889015e9550c
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 591dcec36dc62143901d3b49db24196e84d58c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170357"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Lägga till en intern supportpolicy i ett labb i Azure DevTest Labs

Med Azure DevTest Labs kan du anpassa ditt labb med en intern supportpolicy som ger användarna supportinformation om labbet. Du kan till exempel ange kontaktinformation så att en användare vet hur han eller hon når internt stöd när de behöver hjälp med felsökning eller åtkomst till resurser i labbet. Du kan också tillhandahålla länkar till interna webbplatser eller vanliga frågor som ditt team kan komma åt innan du kontaktar supporten.

En intern supportförklaring är avsedd att låta dig publicera labbinformation som vanligtvis inte ändras för ofta. Information om hur du meddelar användarna om labbinformation som är mer tillfällig – till exempel de senaste uppdateringarna av labbprinciper – finns [i Publicera meddelande i ett labb](devtest-lab-announcements.md).

Du kan enkelt inaktivera eller redigera ett supportutdrag när det inte längre är tillämpligt.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Steg för att lägga till en supportförklaring i ett befintligt labb

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer du **Alla tjänster**och väljer sedan **DevTest Labs** i listan. (Ditt labb kanske redan visas på instrumentpanelen under **Alla resurser**).
1. Välj det labb där du vill lägga till en supportutdrag i listan över labb.  
1. I labbets **översiktsområde** väljer du **Konfiguration och principer**.  

    ![Knappen Konfiguration och principer](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. Välj **Internt stöd**till vänster under **INSTÄLLNINGAR**.

    ![Knappen Internt stöd](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Om du vill skapa ett internt supportmeddelande för användarna i det här labbet anger du Aktiverad till **Ja**.

1. I fältet **Supportmeddelande** anger du den interna supportsats som du vill presentera för labbanvändarna. Supportmeddelandet accepterar Markdown. När du anger meddelandetexten kan du visa **förhandsgranskningsområdet** längst ned på skärmen för att se hur meddelandet visas för användarna.

    ![Intern supportskärm för att skapa meddelandet.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Välj **Spara** när supportutdraget är klart att publiceras.

När du inte längre vill visa det här supportmeddelandet för labbanvändare går du tillbaka till sidan **Intern support** och anger **Aktiverad** till **Nej**.

## <a name="steps-for-users-to-view-the-support-message"></a>Steg för användare att visa supportmeddelandet

1. Välj ett labb på [Azure-portalen.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. På labbets **översiktsområde** väljer du **Internt stöd**.  

    ![Knappen Internt stöd](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Om ett supportmeddelande har publicerats kan användaren visa det från fönstret Internt support.

    ![Internt supportfönster med supportmeddelande publicerat](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* Interna supportsatser används vanligtvis för att tillhandahålla supportinformation som inte ändras så ofta. Du kan också lära dig hur du [lägger upp ett meddelande i ett labb](devtest-lab-announcements.md) för att informera användarna om tillfälliga ändringar eller uppdateringar av labbet.
* [Ange principer och scheman](devtest-lab-set-lab-policy.md) innehåller information om hur du kan tillämpa andra begränsningar och konventioner i din prenumeration med hjälp av anpassade principer.