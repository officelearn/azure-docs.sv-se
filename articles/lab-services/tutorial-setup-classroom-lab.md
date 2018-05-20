---
title: Konfigurera ett klassrumslabb med Azure Lab Services | Microsoft Docs
description: I självstudien konfigurerar du ett labb som ska användas i ett klassrum.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/19/2018
ms.author: spelluru
ms.openlocfilehash: 3e9f8d118c4413ec93b7dffcfa41b6ad4381b052
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Självstudie: Konfigurera ett klassrumslabb 
I självstudien konfigurerar du ett klassrumslabb med en uppsättning virtuella datorer som används av eleverna i klassrummet.  

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett klassrumslabb
> * Konfigurera klassrumslabbet
> * Skicka en registreringslänk till eleverna

## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com).
2. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på klassrumslabbet. 
    2. Välj **storlek** för den virtuella dator som du tänker använda i klassrummet.
    3. Välj **avbildning** som ska användas för att skapa den virtuella datorn.
    4. Ange **standardautentiseringsuppgifter** för inloggning på de virtuella datorerna i labbet. 
    7. Välj **Spara**.

        ![Skapa ett klassrumslabb](./media/tutorial-setup-classroom-lab/new-lab-window.png)
1. **Startsidan** för labbet visas. 
    
    ![Startsida för klassrumslabb](./media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurera användningsprincip

1. Välj **Användningsprincip**. 
2. I inställningarna för **Användningsprincip** anger du det **antal användare** som får använda labbet.
3. Välj **Spara**. 

    ![Användningsprincip](./media/tutorial-setup-classroom-lab/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Konfigurera mallen 
Alla användarnas virtuella maskiner skapas från en mall i ett labb. Konfigurera mallen för virtuella datorer så att den är konfigurerad med exakt det som du vill förse labbanvändarna med. Du kan ange ett namn och en beskrivning av mallen som labbanvändarna ser, samt ange synligheten till ”Offentlig” för att tillgängliggöra instanser av VM-mallen för labbanvändarna. 

### <a name="set-title-and-description"></a>Ange rubrik och beskrivning
1. I avsnittet **Mall** väljer du **Redigera** (pennikonen) för mallen. 
2. I fönstret **Användarvy** anger du en **rubrik** för mallen.
3. Ange en **beskrivning** för mallen.
4. Välj **Spara**.

    ![Beskrivning av klassrumslabb](./media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Göra instanser av mallen offentliga
När du har angett synligheten för en mall till **Offentlig**, skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är samma som det maximala antalet användare som tillåts i labbet, vilket du kan ange i användningsprincipen för labbet. Alla virtuella datorer har samma konfiguration som mallen. 

1. Välj **Synlighet** i avsnittet **Mall**. 
2. På sidan **Tillgänglighet** väljer du **Offentlig**.
    
    > [!IMPORTANT]
    > När en mall har gjorts offentligt tillgänglig, kan åtkomsten inte ändras till privat. 
3. Välj **Spara**.

    ![Tillgänglighet](./media/tutorial-setup-classroom-lab/public-access.png)

## <a name="send-registration-link-to-students"></a>Skicka en registreringslänk till eleverna

1. Välj panelen **Användarregistrering**.
2. I dialogrutan **Användarregistrering** väljer du knappen **Kopiera**. Länken kopieras till Urklipp. Klistra in den i ett e-postredigeringsprogram och skicka ett e-postmeddelande till eleven. 

    ![Länk för elevregistrering](./media/tutorial-setup-classroom-lab/registration-link.png)

## <a name="next-steps"></a>Nästa steg
I självstudien skapade du ett klassrumslabb och konfigurerade labbet. Om du vill veta hur en elev kan få åtkomst till en virtuell dator i labbet med hjälp av registreringslänken, går du vidare till nästa självstudie:

> [!div class="nextstepaction"]
> [Ansluta till en virtuell dator i klassrumslabbet](tutorial-connect-virtual-machine-classroom-lab.md)

