---
title: Azure Stack-Snabbstart – skapa en virtuell Windows-dator
description: Azure Stack-Snabbstart – skapa en Windows-VM med portalen
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: mabrigg
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 7277aeb97409815e2e218da8f233cd836bccc72b
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022424"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Snabbstart: skapa en Windows server-dator med Azure Stack-portalen

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan skapa en virtuell Windows Server 2016-dator med hjälp av Azure Stack-portalen. Följ stegen i den här artikeln för att skapa och använda en virtuell dator.

> [!NOTE]  
> Skärmbilder i den här artikeln har uppdaterats för att matcha användargränssnittet som introducerades i Azure Stack-version 1808. 1808 lägger till stöd för användning av *hanterade diskar* förutom ohanterade diskar. Om du använder en tidigare version, kommer vissa bilder som valet av disk vara annorlunda än vad som visas i den här artikeln.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Logga in på Azure Stack-portalen

Logga in på Azure Stack-portalen. Adressen för Azure Stack portal beror på vilken Azure Stack-produkt som du vill ansluta till:

* För Azure Stack Development Kit (ASDK) går du till: https://portal.local.azurestack.external.
* Gå till den URL som tillhandahålls av Azure Stack-operatör för ett integrerat Azure Stack-system.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **nya** > **Compute** > **Windows Server 2016 Datacenter – betalning som du-användning** > **skapa**. Om du inte ser **Windows Server 2016 Datacenter – betalning som du-användning** post, kontakta din Azure Stack-operator. Be att de lägger till det till marketplace som beskrivs i den [Lägg till Windows Server 2016 VM-avbildning till Azure Stack marketplace](../azure-stack-add-default-image.md) artikeln.

    ![Steg för att skapa en Windows-dator i portalen](media/azure-stack-quick-windows-portal/image01.png)
2. Under **grunderna**, ange ett **namn**, **användarnamn**, och **lösenord**. Välj en **prenumeration**. Skapa en **resursgrupp**, eller välj en befintlig en, Välj en **plats**, och klicka sedan på **OK**.

    ![Konfigurera grundläggande inställningar](media/azure-stack-quick-windows-portal/image02.png)
3. Under **storlek** Välj **Standard D1**, och klicka sedan på **Välj**.  
    ![Välj storlek för virtuell dator](media/azure-stack-quick-windows-portal/image03.png)

4. På den **inställningar** sidan, gör ändringarna av standardinställningarna.
   - Från och med Azure Stack-version 1808, kan du konfigurera **Storage** där du kan välja att använda *hanterade diskar*. Före version 1808 kan endast ohanterade diskar användas.  
   ![Konfigurera inställningar för virtuella datorer](media/azure-stack-quick-windows-portal/image04.png)  
   När dina konfigurationer är klar, Välj **OK** att fortsätta.

5. Under **sammanfattning**, klickar du på **OK** att skapa den virtuella datorn.
    ![Visa sammanfattning och skapa virtuell dator](media/azure-stack-quick-windows-portal/image05.png)

6. Om du vill se din nya virtuella dator, klickar du på **alla resurser**, Sök efter namnet på virtuella datorn och sedan klickar du på namnet i sökresultatet.
    ![Se virtuell dator](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort den virtuella datorn och dess resurser när du är klar med hjälp av den virtuella datorn. Om du vill göra det väljer du resursgruppen på sidan virtuell dator och klickar på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har distribuerat du en grundläggande Windows Server-datorn. Om du vill veta mer om Azure Stack-datorer kan fortsätta att [överväganden för virtuella datorer i Azure Stack](azure-stack-vm-considerations.md).
