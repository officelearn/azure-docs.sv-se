---
title: Konfigurera inställningar i klassrum labs av Azure Lab Services | Microsoft Docs
description: Lär dig hur du konfigurerar antalet användare för labbet, få dem registrerad labbet, antalet timmar som de kan använda den virtuella datorn och mycket mer.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2018
ms.author: spelluru
ms.openlocfilehash: a91d5826f52b2beb05f2d9a08f3646bd776b294e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142479"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurera inställningar och principer
Den här artikeln beskriver hur du konfigurerar antalet användare för labbet, få dem registrerad labbet, antalet timmar som de kan använda den virtuella datorn och mycket mer. 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>Ange hur många användare som tillåts i laboratoriet

1. Välj **Användningsprincip**. 
2. I inställningarna för **Användningsprincip** anger du det **antal användare** som får använda labbet.
3. Välj **Spara**. 

    ![Användningsprincip](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-users"></a>Skicka registreringslänk till användare

1. Växla till vyn **Instrumentpanel**. 
2. Välj panelen **Användarregistrering**.

    ![Länk för elevregistrering](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. I dialogrutan **Användarregistrering** väljer du knappen **Kopiera**. Länken kopieras till Urklipp. Klistra in den i ett e-postredigeringsprogram och skicka ett e-postmeddelande till eleven. Studenter använder länken för att gå till sidan som hjälper dem med registrera med labbet. 

    ![Länk för elevregistrering](../media/tutorial-setup-classroom-lab/registration-link.png)
2. I dialogrutan **Användarregistrering** väljer du **Stäng**. 

## <a name="view-users-registered-with-the-lab"></a>Visa användare som har registrerats med labbet

Välj **användare** på den vänstra menyn för att se en lista över användare som har registrerats med labbet. 

![Lista över användare som har registrerats med labbet](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>Ange kvoter per användare

1. Välj **användarinställningar** på den vänstra menyn.
2. Välj **Quta per användare** panelen. 
3. Välj **begränsa antalet timmar som en användare kan använda en virtuell dator**. 
4. För **hur många timmar du vill ge till varje användare?** anger hur många timmar och väljer **spara**. 

    ![Antal timmar per användare](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. Du ser hur många timmar på den **kvot per användare** panelen nu. 

    ![Kvot per användare](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="manage-user-vms"></a>Hantera användarnas VM: ar
När studenter register med Azure Lab Services med hjälp av registreringen länka angivna dem ska du se de virtuella datorerna har tilldelats till studenter på **virtuella datorer** fliken. 

![Virtuella datorer som tilldelats studenter](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Du kan utföra följande uppgifter på en student VM: 

- Stoppa en virtuell dator om Virtuellt datorn körs. 
- Starta en virtuell dator om den virtuella datorn har stoppats. 
- Anslut till den virtuella datorn. 
- Ta bort den virtuella datorn. 
- Visa antalet timmar som den virtuella datorn på användare. 


## <a name="next-steps"></a>Nästa steg
Kom igång med att konfigurera ett testlabb med Azure Lab Services:

- [Konfigurera ett klassrumslabb](how-to-manage-classroom-labs.md)
- [Konfigurera ett labb](../tutorial-create-custom-lab.md)
