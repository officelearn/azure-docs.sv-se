---
title: Publicera en announcment till ett labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du lägger till ett meddelande till ett labb i Azure DevTest Labs
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611213"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Publicera ett meddelande till ett labb i Azure DevTest Labs

Som en labb-administratör kan publicera du ett anpassat meddelande i en befintlig labbet för att meddela användare om de senaste ändringarna eller tillägg till labbet. Du kanske exempelvis vill informera användarna om:

- Nya VM-storlekar som är tillgängliga
- Bilder som är för närvarande inte kan användas
- Uppdateringar av principer för labbet

När publicerats meddelandet visas på översiktssidan i labbet och användaren kan välja för mer information.

Meddelande-funktionen är avsedd att användas för tillfälliga meddelanden.  När den inte längre behövs kan du enkelt inaktivera ett meddelande.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Steg för att publicera ett meddelande i en befintlig labb

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer **alla tjänster**, och välj sedan **DevTest Labs** i listan. (Labbet kanske redan visas på instrumentpanelen under **alla resurser**).
1. I listan över labbar Välj labb där du vill publicera ett meddelande.  
1. På testmiljön **översikt** Välj **konfiguration och principer**.  

    ![Knappen för konfiguration och principer](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. Till vänster under **inställningar**väljer **Lab meddelande**.

    ![Lab meddelande knappen](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Så här skapar du ett meddelande för användare i den här övningen, **aktiverad** till **Ja**.

1. Du kan ange en **förfallodatum** att ange ett datum och tid då meddelandet inte längre visas för användare. Om du inte anger ett förfallodatum, förblir meddelandet tills du inaktiverar den.

   > [!NOTE]
   > När meddelandet har gått ut, den inte längre visas för användare, men finns fortfarande i den **Lab meddelande** fönstret. Du kan göra ändringar och återaktivera det att aktivera den igen.
   >
   >

1. Ange en **meddelande rubrik** och **meddelandetext**.

   Rubriken får innehålla högst 100 tecken och visas för användaren på översiktssidan för den testmiljön. Om användaren väljer rubriken, visas meddelandet texten.

   Meddelandetext accepterar markdown. När du anger meddelandetext, kan du visa meddelandet under exempel längst ned på skärmen.

    ![Lab meddelande skärmen för att skapa meddelandet.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Välj **spara** när ditt meddelande är redo för publicering.

När du inte längre vill visa det här meddelandet till labbanvändare tillbaka till den **Lab meddelande** sidan och ange **aktiverad** till **nr**. Om du har angett ett förfallodatum inaktiveras meddelandet automatiskt vid denna tidpunkt.

## <a name="steps-for-users-to-view-an-announcement"></a>Steg för användare att visa ett meddelande

1. Från den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040), Välj ett labb.

1. Om labbet har ett meddelande som publicerats för den, visas ett meddelande längst ned i labbet översiktssidan. Detta meddelande om information är meddelande rubriken som angavs när meddelandet skapades.

    ![Lab meddelande-översikt](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. Användaren kan välja meddelandet för att visa hela meddelandet.

    ![Mer information på labb-meddelandet](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* Om du ändrar eller skapa en princip för labbet, kanske du vill publicera ett meddelande för att informera användare. [Ange principer och scheman](devtest-lab-set-lab-policy.md) innehåller information om hur du tillämpar begränsningar och konventioner i hela prenumerationen genom att använda anpassade principer.
* Utforska den [DevTest Labs Azure Resource Manager QuickStart mallgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
