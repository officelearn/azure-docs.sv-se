---
title: Efter en announcment till ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du lägger till ett meddelande i ett labb i Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 3282a90069ecd119154df492ac6dc366d26b5300
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787474"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Efter ett meddelande till ett labb i Azure DevTest Labs

Du kan publicera ett anpassat meddelande i en befintlig labbet för att meddela användare om ändringar eller tillägg till labbet som en labb-administratör. Du kanske vill informera användarna om:

- Nya VM-storlekar som är tillgängliga
- Bilder som är för närvarande inte kan användas
- Uppdateringar av principer för labbet

När anslås, visas ett meddelande på översiktssidan för den testmiljön och användaren kan välja mer information.

Meddelande-funktionen är avsedd att användas för tillfälliga meddelanden.  Du kan enkelt inaktivera ett meddelande när den inte längre behövs.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Steg för att publicera ett meddelande i en befintlig labb

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer **alla tjänster**, och välj sedan **DevTest Labs** från listan. (Ditt labb kanske redan visas på instrumentpanelen under **alla resurser**).
1. Välj labbet som du vill publicera ett meddelande från listan över övningar.  
1. På testmiljön **översikt** väljer **konfiguration och principer**.  

    ![Knapp för konfiguration och principer](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Vänster under **inställningar**väljer **Lab meddelande**.

    ![Lab meddelande knappen](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Så här skapar du ett meddelande för användare i den här övningen, **aktiverad** till **Ja**.

1. Du kan ange en **förfallodatum** att ange ett datum och tid då meddelandet inte längre visas för användare. Om du inte anger ett förfallodatum, förblir tillkännagivandet tills du inaktivera den.

   > [!NOTE]
   > När meddelandet upphör att gälla den inte längre visas för användare, men finns kvar i den **Lab meddelande** fönstret. Du kan göra ändringar och återaktivera det så att den aktiveras igen.
   >
   >

1. Ange en **meddelande rubrik** och **meddelandetext**.

   Rubriken kan vara upp till 100 tecken och är visas för användaren på översiktssidan för den testmiljön. Om användaren väljer rubrik, visas meddelandetext.

   Texten som meddelande accepterar markdown. När du anger texten som meddelandet kan du visa meddelandet under exempel längst ned på skärmen.

    ![Lab meddelande skärmen för att skapa meddelandet.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Välj **spara** när ditt meddelande är redo att skicka.

Om du inte längre vill visa det här meddelandet för labbet användare tillbaka till den **Lab meddelande** sidan och ange **aktiverad** till **nr**. Om du har angett ett förfallodatum som har meddelandet inaktiverats automatiskt vid denna tidpunkt.

## <a name="steps-for-users-to-view-an-announcement"></a>Steg för användare att visa ett meddelande

1. Från den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040), Välj ett labb.

1. Om labbet har ett meddelande för det, visas ett meddelande längst upp i den testmiljön översiktssidan. Detta meddelande är meddelande titeln som angavs när ett meddelande har skapats.

    ![Lab meddelande på sidan Översikt](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. Användaren kan välja i meddelandet för att visa hela meddelandet.

    ![Mer information för labb-meddelande](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* Om du ändrar eller ange en princip för labbet kanske du vill publicera ett meddelande för att informera användare. [Ange principer och scheman](devtest-lab-set-lab-policy.md) innehåller information om att tillämpa begränsningar och konventioner för din prenumeration med hjälp av anpassade principer.
* Utforska den [DevTest Labs Azure Resource Manager QuickStart mallgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
