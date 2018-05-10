---
title: Hantera klassrum labb i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar och konfigurerar ett klassrum labb kan visa alla de klassrum övningarna, fler registreringen länka med lab-användare eller ta bort ett labb.
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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 17544275f921486529518e37eb171cd0b4f26791
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="manage-classroom-labs-in-azure-lab-services-formerly-azure-devtest-labs"></a>Hantera klassrum labb i Azure Lab Services (tidigare Azure DevTest Labs)
Den här artikeln beskriver hur du skapar och konfigurerar ett klassrum labb, visa alla klassrum labs eller ta bort ett klassrum labb.

## <a name="create-a-classroom-lab"></a>Skapa ett klassrum labb

1. Gå till [Azure Lab Services webbplats](https://labs.azure.com).
2. I den **nya labbet** och göra följande: 
    1. Ange en **namn** för klassrummet labbet. 
    2. Välj den **storlek** för den virtuella datorn som du tänker använda i klassrummet.
    3. Välj den **bild** för att skapa den virtuella datorn.
    4. Ange den **standardautentiseringsuppgifter** ska användas för att logga in på virtuella datorer i labbet.
    7. Välj **Spara**.

        ![Skapa ett klassrum labb](./media/how-to-manage-classroom-labs/new-lab-window.png)
1. Du ser den **startsidan** för labbet. 
    
    ![Startsidan för klassrummet labb](./media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurera användningsprincipen för

1. Välj **användningsprincipen**. 
2. I den **användningsprincipen**, inställningar, ange den **antal användare** får använda labbet.
3. Välj **Spara**. 

    ![Användningsprincipen](./media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Konfigurera mallen
En mall i ett labb är från som alla användare skapas virtuella datorer. Konfigurera mallen för virtuell dator så att den är konfigurerad med exakt vad du vill förse lab-användare. Du kan ange ett namn och en beskrivning av mallen som lab-användare ser och ange visas som ”offentliga” tillgängliggöra instanser av VM-mallen till lab-användare.  

## <a name="set-template-title-and-description"></a>Ange mall rubrik och beskrivning
1. I den **mallen** väljer **redigera** (pennikonen) för mallen. 
2. I den **användarens** och ange en **rubrik** för mallen.
3. Ange **beskrivning** för mallen.
4. Välj **Spara**.

    ![Klassrum lab beskrivning](./media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Offentliggöra instanser av mallen 
När du har angett en mall som ska visas **offentliga**, Azure Lab Services skapar virtuella datorer i labbet med hjälp av mallen. Antal virtuella datorer skapas i den här processen är samma som det maximala antalet användare som tillåts i labbet, där du kan ange i användningsprincipen labbet. Alla virtuella datorer har samma konfiguration som mall.  

1. Välj **synlighet** i den **mallen** avsnitt. 
2. I den **tillgänglighet** väljer **offentliga**.
    
    > [!IMPORTANT]
    > När en mall är offentligt tillgänglig, kan inte ändras dess åtkomst till privat. 
3. Välj **Spara**.

    ![Tillgänglighet](./media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>Skicka registrering länk för studenter

1. Välj **användarregistrering** panelen.
2. I den **användarregistrering** dialogrutan markerar du den **kopiera** knappen. Länken kopieras till Urklipp. Klistra in den i ett redigeringsprogram som e-post och skicka ett e-postmeddelande till studenter. 

    ![Student registrering länk](./media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>Visa alla klassrum labs
1. Gå till [Azure Lab Services-portalen](https://labs.azure.com).
2. Bekräfta att du ser alla labs i det valda labb-kontot. 

    ![Alla labs](./media/how-to-manage-classroom-labs/all-labs.png)
3. Använd den nedrullningsbara listrutan överst för att välja ett annat labb-konto. Du kan se labs i det valda labb-kontot. 

## <a name="delete-a-classroom-lab"></a>Ta bort ett klassrum labb
1. Välj tre punkter (...) i hörnet på panelen för labbet. 

    ![Välj labbet](./media/how-to-manage-classroom-labs/select-three-dots.png)
2. Välj **Ta bort**. 

    ![Knappen Ta bort](./media/how-to-manage-classroom-labs/delete-button.png)
3. På den **ta bort lab** dialogrutan **ta bort**. 

    ![Ta bort dialogruta](./media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>Nästa steg
Kom igång med att ställa in ett testlabb med Azure Lab Services:

- [Skapa ett klassrum labb](how-to-manage-classroom-labs.md)
- [Skapa en anpassad labb](tutorial-create-custom-lab.md)
