---
title: Lägg till en intern support-instruktion i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du publicerar en intern support-instruktion i ett labb i Azure DevTest Labs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 2d12ca26fb2aa5abddcf44b2e634b2f08b1fb01b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787432"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Lägg till en intern support-instruktion i ett labb i Azure DevTest Labs

Azure DevTest Labs kan du anpassa ditt labb med en intern support-instruktion som ger användare tillgång till information om labbet. Du kan till exempel ange kontaktinformation så att användaren vet hur man når internt stöd när de behöver hjälp med felsökning eller få åtkomst till resurser i labbet. Du kan också innehålla länkar till interna webbplatser eller vanliga frågor och svar som gruppen kan komma åt innan du kontaktar supporten.

En intern support-instruktion är avsedd att kan du skriva in labb som normalt inte ändras för ofta. Meddela användare om lab information som är mer tillfälliga – till exempel de senaste uppdateringarna till principer för labbet – Se [efter meddelandet i ett labb](devtest-lab-announcements.md).

Du kan enkelt inaktivera eller redigera en instruktion stöd när det gäller inte längre.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Steg för att lägga till en instruktion för stöd till en befintlig labbet

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer **alla tjänster**, och välj sedan **DevTest Labs** från listan. (Ditt labb kanske redan visas på instrumentpanelen under **alla resurser**).
1. Lista över labs, Välj labbet där du vill lägga till en support-instruktion.  
1. På testmiljön **översikt** väljer **konfiguration och principer**.  

    ![Knapp för konfiguration och principer](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. Vänster under **inställningar**väljer **intern support**.

    ![Intern support-knappen](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Om du vill skapa en intern support meddelande för användare i den här övningen sägs aktiverad **Ja**.

1. I den **stöd meddelande** , ange instruktionen internt stöd som du vill presentera lab-användare. Stöd för meddelandet accepterar Markdown. När du anger meddelandetexten, du kan visa den **Preview** område längst ned på skärmen för att se hur meddelandet visas för användarna.

    ![Intern support skärmen för att skapa meddelandet.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Välj **spara** när support-instruktionen är redo att skicka.

Om du inte längre vill visa meddelandet stöd för labbet användare tillbaka till den **internt stöd** sidan och ange **aktiverad** till **nr**.

## <a name="steps-for-users-to-view-the-support-message"></a>Steg för användare att visa meddelandet stöd

1. Från den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040), Välj ett labb.

1. På testmiljön **översikt** väljer **intern support**.  

    ![Intern support-knappen](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Om ett meddelande om stöd är bokförd kan användaren visa den från fönstret intern support.

    ![Intern support fönstret visar support meddelande skickat](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* Internt supportvillkor används vanligtvis för att tillhandahålla supportinformation som inte ändras som ofta. Du kan också lära dig hur du [efter ett meddelande till ett labb](devtest-lab-announcements.md) informera användarna om tillfälliga ändringar eller uppdateringar av labbet.
* [Ange principer och scheman](devtest-lab-set-lab-policy.md) innehåller information om hur du kan använda andra begränsningar och konventioner på din prenumeration med hjälp av anpassade principer.