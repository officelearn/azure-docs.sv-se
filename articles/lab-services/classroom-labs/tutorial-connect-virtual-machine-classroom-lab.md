---
title: Åtkomst till ett klassrumslabb i Azure Lab Services | Microsoft Docs
description: I självstudien får du åtkomst till virtuella datorer i ett klassrumslabb som har konfigurerats av en professor.
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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 27d79e28a986e929fb71dd77fc50b3c2cd32618f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77134040"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Självstudie: Åtkomst till ett klassrumslabb i Azure Lab Services
I den här självstudien ansluter du som student till en virtuell dator (VM) i ett klassrumslabb. 

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Registrera dig i labbet
> * Starta den virtuella datorn
> * Anslut till VM:en

## <a name="register-to-the-lab"></a>Registrera dig i labbet

1. Gå till **registrerings-URL:en** som du fick av läraren. Du behöver inte använda registrerings-URL:en efter att du har slutfört registreringen. Använd i stället [https://labs.azure.com](https://labs.azure.com)webbadressen: . Internet Explorer 11 stöds inte ännu. 
1. Logga in på tjänsten med ditt skolkonto för att slutföra registreringen. 

    > [!NOTE]
    > Ett Microsoft-konto krävs för att använda Azure Lab Services. Om du försöker använda ditt konto som inte kommer från Microsoft för att logga in på portalen följer du instruktionerna för att skapa ett Microsoft-konto som länkas till ditt konto som inte kommer från Microsoft. Följ sedan stegen för att slutföra registreringsprocessen. 
1. När registreringen är klar kontrollerar du att du ser den virtuella datorn för det labb som du har åtkomst till. 
1. Vänta tills den virtuella datorn är klar. Lägg märke till följande fält på vm-panelen:
    1. Högst upp på panelen ser du **namnet på labbet**.
    1. Till höger ser du ikonen som representerar **operativsystemet (OS)** för den virtuella datorn. I det här exemplet är det Windows OS. 
    1. Förloppsindikatorn på panelen visar antalet timmar som används mot antalet [kvottimmar](how-to-configure-student-usage.md#set-quotas-for-users) som tilldelats dig. Den här gången är den extra tid som tilldelats dig utöver den schemalagda tiden för labbet. 
    1. Du ser ikoner/knappar längst ned på panelen för att starta/stoppa den virtuella datorn och ansluta till den virtuella datorn. 
    1. Till höger om knapparna visas status för den virtuella datorn. Bekräfta att statusen för den virtuella datorn **är Stoppad**. 

        ![VM i stoppat tillstånd](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>Starta den virtuella datorn
1. **Starta** den virtuella datorn genom att välja den första knappen som visas i följande bild. Den här processen tar lite tid.  

    ![Starta den virtuella datorn](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Bekräfta att status för den virtuella datorn är inställd på **Kör**. 

    ![Vm i körläge](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Observera att ikonen för den första knappen har ändrats för att representera en **stoppåtgärd.** Du kan välja den här knappen för att stoppa den virtuella datorn. 

## <a name="connect-to-the-vm"></a>Anslut till VM:en

1. Välj den andra knappen som visas i följande bild för att **ansluta** till labbets virtuella dator. 

    ![Ansluta till virtuell dator](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Gör något av följande: 
    1. För virtuella **Windows-datorer** sparar du **RDP-filen** på hårddisken. Öppna RDP-filen för att ansluta till den virtuella datorn. Använd **användarnamnet** och **lösenordet** du får från din lärare/professor för att logga in på maskinen. 
    3. För **virtuella Linux-datorer** kan du använda **SSH** eller **RDP** (om det är aktiverat) för att ansluta till dem. Mer information finns i [Aktivera fjärrskrivbordsanslutning för Linux-datorer](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du åtkomst till ett klassrumslabb med registreringslänken du får från din lärare/professor.

Som labbägare behöver du se vem som har registrerats med ditt labb och spåra användningen av virtuella datorer. Gå vidare till nästa självstudie och lär dig hur du spårar användningen av labbet:

> [!div class="nextstepaction"]
> [Spåra användningen av ett labb](tutorial-track-usage.md) 
