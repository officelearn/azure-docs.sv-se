---
title: Lägg till en intern support-instruktion i ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur man publicerar en intern support-instruktion i ett labb i Azure DevTest Labs
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38696258"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Lägg till en intern support-instruktion i ett labb i Azure DevTest Labs

Azure DevTest Labs kan du anpassa ditt labb med en intern support-instruktion som förser användare med information om labbet. Du kan till exempel ange kontaktinformation så att användaren känner till hur man når intern support när de behöver hjälp med felsökning eller åtkomst till resurser i laboratoriet. Du kan också innehålla länkar till interna webbplatser eller vanliga frågor och svar som ditt team kan använda innan du kontaktar support.

En intern support-instruktion är avsedd att kan du publicera lab information som vanligtvis inte ändras för ofta. Meddela användare om lab information som är mer tillfällig – till exempel de senaste uppdateringarna för principer för labbet – Se [publicera ett meddelande i ett labb](devtest-lab-announcements.md).

Du kan enkelt inaktivera eller redigera ett supportmeddelande när det gäller inte längre.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Steg för att lägga till ett supportmeddelande i en befintlig labb

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer **alla tjänster**, och välj sedan **DevTest Labs** i listan. (Labbet kanske redan visas på instrumentpanelen under **alla resurser**).
1. I listan över labbar Välj labb där du vill lägga till ett supportmeddelande.  
1. På testmiljön **översikt** Välj **konfiguration och principer**.  

    ![Knappen för konfiguration och principer](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. Till vänster under **inställningar**väljer **intern support**.

    ![Intern support-knappen](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Om du vill skapa en intern support-meddelande för användare i den här övningen inställd på aktiverad **Ja**.

1. I den **Support meddelande** anger intern support-instruktionen som du vill att presentera dina labbanvändare. Stöd för meddelandet accepterar Markdown. När du anger meddelandetexten, du kan visa den **förhandsversion** området längst ned på skärmen för att se hur meddelandet visas för användarna.

    ![Intern support skärmen för att skapa meddelandet.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Välj **spara** när ditt supportmeddelande är redo för publicering.

När du inte längre vill visa meddelandet stöd för labb användarna tillbaka till den **intern support** sidan och ange **aktiverad** till **nr**.

## <a name="steps-for-users-to-view-the-support-message"></a>Steg för användare att läsa meddelandet support

1. Från den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040), Välj ett labb.

1. På testmiljön **översikt** Välj **intern support**.  

    ![Intern support-knappen](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Om en support-meddelande har publicerats kan användaren visa den från fönstret intern support.

    ![Intern support fönster som visar support-meddelande skickat](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* Intern supportvillkor används vanligtvis för att tillhandahålla supportinformation som inte ändras som ofta. Du kan också lära dig hur du [publicera ett meddelande till ett labb](devtest-lab-announcements.md) informera användarna om tillfälliga ändringar eller uppdateringar till labbet.
* [Ange principer och scheman](devtest-lab-set-lab-policy.md) innehåller information om hur du kan använda andra begränsningar och konventioner i din prenumeration med hjälp av anpassade principer.