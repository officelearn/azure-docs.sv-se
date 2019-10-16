---
title: Aktivera miljöer med flera virtuella datorer i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar en miljö med flera virtuella datorer i en mall för virtuella datorer i ett klass rums labb i Azure Lab Services.
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
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 9a86ba803f899e78b2ba9640e6cc317966969e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332338"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Skapa en miljö med flera virtuella datorer i en mall för ett klass rums labb
För närvarande Azure Lab Services kan du konfigurera en virtuell mall för en virtuell dator i ett labb och göra en enskild kopia tillgänglig för varje användare. Men om du är en lärare som underhåller en IT-klass om hur du konfigurerar brand väggar eller servrar kan du behöva ge varje studenter en miljö där flera virtuella datorer kan kommunicera med varandra över ett nätverk.

Kapslad virtualisering gör att du kan skapa en miljö med flera virtuella datorer i en labbs mall för virtuella datorer. Genom att publicera mallen får du varje användare i labbet med en virtuell dator som är konfigurerad med flera virtuella datorer i den.

## <a name="what-is-nested-virtualization"></a>Vad är kapslad virtualisering?
Kapslad virtualisering gör att du kan skapa virtuella datorer i en virtuell dator. Kapslad virtualisering görs via Hyper-V och är bara tillgänglig på virtuella Windows-datorer.

Mer information om kapslad virtualisering finns i följande artiklar:

- [Kapslad virtualisering i Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Så här aktiverar du kapslad virtualisering i en virtuell Azure-dator](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Använd kapslad virtualisering i Azure Lab Services
De viktiga stegen är:

1. Skapa en **Windows** -mall med **stor** storlek för labbet. 
2. Anslut till den och [Aktivera kapslad virtualisering](../../virtual-machines/windows/nested-virtualization.md).


Följande procedur ger dig detaljerade anvisningar: 

1. Skapa ett labb konto om du inte redan har ett. Mer information finns i [Självstudier: Konfigurera ett labb konto med Azure Lab Services](tutorial-setup-lab-account.md).
1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). Observera att Internet Explorer 11 inte stöds ännu. 
2. Välj **Logga in** och ange dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
3. Välj **nytt labb**. 
    
    ![Skapa ett klassrumslabb](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på ditt labb. 
    2. Välj **stor (kapslad virtualisering)** eller **medium (kapslad virtualisering)** för den **virtuella dator storleken**.
    6. Välj en Windows- **avbildning** som du vill använda. Kapslad virtualisering är bara tillgängligt på Windows-datorer. 
    4. Välj sedan **Nästa**. 

        ![Skapa ett klassrumslabb](../media/how-to-enable-multi-vm-environment/new-lab-window.png)
    1. På sidan **autentiseringsuppgifter för virtuell dator** anger du standardautentiseringsuppgifterna för alla virtuella datorer i labbet. Ange **namn** och **lösen ord** för användaren och välj sedan **Nästa**.  

        ![Nytt labb fönster](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Anteckna namnet och lösenordet. De kommer inte att visas igen.
    3. På sidan **labb principer** anger du det antal timmar som tilldelas för varje användare (**kvot för varje användare**) utanför den schemalagda tiden för labbet och väljer sedan **Slutför**. 

        ![Kvot för varje användare](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Du bör se följande skärm bild som visar status för den mall för att skapa mallen. Det tar upp till 20 minuter att skapa mallen i labbet. 

    ![Status för skapande av mall för virtuell dator](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
1. På sidan **mall** väljer du **Anpassa mall** i verktygsfältet. 

    ![Knappen anpassa mall](../media/how-to-create-manage-template/customize-template-button.png)
2. I dialog rutan **Anpassa mall** väljer du **Fortsätt**. När du har startat mallen och gjort ändringar kommer den inte längre att ha samma konfiguration som de virtuella datorer som senast publicerades till användarna. Ändringar i mallen visas inte på användarnas befintliga virtuella datorer förrän du har publicerat dem igen.

    ![Dialog rutan Anpassa](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Klicka på knappen **Anslut till mall** i verktygsfältet för att ansluta till mallen VM för att konfigurera kapslad virtualisering. och följ instruktionerna. Om det är en Windows-dator visas ett alternativ för att ladda ned RDP-filen. 

    ![Ansluta till den virtuella malldatorn](../media/how-to-create-manage-template/connect-template-vm.png) 
9. I den virtuella dator mal len konfigurerar du kapslad virtualisering och konfigurerar ett virtuellt nätverk med flera virtuella datorer. Detaljerade steg-för-steg-instruktioner finns i [så här aktiverar du kapslad virtualisering i en virtuell Azure-dator](../../virtual-machines/windows/nested-virtualization.md). Här är en snabb sammanfattning av stegen: 
    1. Aktivera Hyper-V-funktionen i den virtuella dator mal len.
    2. Konfigurera ett internt virtuellt nätverk med Internet anslutning för de kapslade virtuella datorerna
    3. Skapa virtuella datorer via Hyper-V Manager
    4. Tilldela en IP-adress till de virtuella datorerna
10. Välj **publicera** i verktygsfältet på sidan **mall** . 

    ![Knappen publicera mall](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > När du väl har publicerat kan du inte ångra publiceringen. 
8. På sidan **publicera mall** anger du det antal virtuella datorer som du vill skapa i labbet och väljer sedan **publicera**. 

    ![Publicera mall – antal virtuella datorer](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Du ser **statusen för att publicera** mallen på sidan. Den här processen kan ta upp till en timma. 

    ![Publicera mall – förlopp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)


## <a name="next-steps"></a>Nästa steg

Nu får varje användare en enda virtuell dator som innehåller en miljö med flera virtuella datorer i den. Information om hur du lägger till användare i labbet och skickar registrerings länken till dem finns i följande artikel: [Lägg till användare i labbet](tutorial-setup-classroom-lab.md#add-users-to-the-lab).