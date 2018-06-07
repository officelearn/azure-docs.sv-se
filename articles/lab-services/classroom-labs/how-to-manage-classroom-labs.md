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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 705209becff7c8ad20e7d09f056aa1ae1577b7ae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655583"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Hantera klassrum labb i Azure Lab Services 
Den här artikeln beskriver hur du skapar och konfigurerar ett klassrum labb, visa alla klassrum labs eller ta bort ett klassrum labb.

## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com).
2. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på klassrumslabbet. 
    2. Välj **storlek** för den virtuella dator som du tänker använda i klassrummet.
    3. Välj **avbildning** som ska användas för att skapa den virtuella datorn.
    4. Ange den **standardautentiseringsuppgifter** ska användas för att logga in på virtuella datorer i labbet.
    7. Välj **Spara**.

        ![Skapa ett klassrumslabb](../media/how-to-manage-classroom-labs/new-lab-window.png)
1. **Startsidan** för labbet visas. 
    
    ![Startsida för klassrumslabb](../media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurera användningsprincip

1. Välj **Användningsprincip**. 
2. I inställningarna för **Användningsprincip** anger du det **antal användare** som får använda labbet.
3. Välj **Spara**. 

    ![Användningsprincip](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Konfigurera mallen
En mall i ett labb är en basavbildning av en virtuell dator som alla användares virtuella datorer skapas från. Konfigurera mallen för virtuella datorer så att den är konfigurerad med exakt det som du vill förse labbanvändarna med. Du kan ange ett namn och en beskrivning av mallen som visas för labbanvändarna. Konfigurera synligheten för mallen som offentlig så att instanser av den virtuella malldatorn är tillgängliga för labbanvändarna.  

### <a name="set-template-title-and-description"></a>Ange mall rubrik och beskrivning
1. I avsnittet **Mall** väljer du **Redigera** (pennikonen) för mallen. 
2. I fönstret **Användarvy** anger du en **rubrik** för mallen.
3. Ange en **beskrivning** för mallen.
4. Välj **Spara**.

    ![Beskrivning av klassrumslabb](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Göra instanser av mallen offentliga 
När du har angett synligheten för en mall till **Offentlig**, skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är samma som det högsta antalet användare som tillåts i labbet. Du kan definiera antalet i användningsprincipen för labbet. Alla virtuella datorer har samma konfiguration som mallen.  

1. Välj **Synlighet** i avsnittet **Mall**. 
2. På sidan **Tillgänglighet** väljer du **Offentlig**.
    
    > [!IMPORTANT]
    > När en mall har gjorts offentligt tillgänglig, kan åtkomsten inte ändras till privat. 
3. Välj **Spara**.

    ![Tillgänglighet](../media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>Skicka en registreringslänk till eleverna

1. Välj panelen **Användarregistrering**.
2. I dialogrutan **Användarregistrering** väljer du knappen **Kopiera**. Länken kopieras till Urklipp. Klistra in den i ett e-postredigeringsprogram och skicka ett e-postmeddelande till eleven. 

    ![Länk för elevregistrering](../media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>Visa alla klassrum labs
1. Gå till [Azure Lab Services-portalen](https://labs.azure.com).
2. Bekräfta att du ser alla labs i det valda labb-kontot. 

    ![Alla labs](../media/how-to-manage-classroom-labs/all-labs.png)
3. Använd den nedrullningsbara listrutan överst för att välja ett annat labb-konto. Du kan se labs i det valda labb-kontot. 

## <a name="delete-a-classroom-lab"></a>Ta bort ett klassrum labb
1. Välj tre punkter (...) i hörnet på panelen för labbet. 

    ![Välja labbet](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Välj **Ta bort**. 

    ![Knappen Ta bort](../media/how-to-manage-classroom-labs/delete-button.png)
3. På den **ta bort lab** dialogrutan **ta bort**. 

    ![Ta bort dialogruta](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>Nästa steg
Kom igång med att konfigurera ett testlabb med Azure Lab Services:

- [Konfigurera ett klassrumslabb](how-to-manage-classroom-labs.md)
- [Skapa ett labb](../tutorial-create-custom-lab.md)
