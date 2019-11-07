---
title: Åtkomst till ett klassrumslabb i Azure Lab Services | Microsoft Docs
description: I självstudien får du åtkomst till virtuella datorer i ett klassrumslabb som har konfigurerats av en lärare.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 30983d141f087a46e420f7ea457fba181956c28e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577812"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Självstudie: Åtkomst till ett klassrumslabb i Azure Lab Services
I den här självstudien ansluter du som student till en virtuell dator (VM) i ett klassrumslabb. 

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Registrera dig för labbet
> * Starta den virtuella datorn
> * Anslut till VM:en

## <a name="register-to-the-lab"></a>Registrera dig för labbet

1. Gå till **registrerings-URL:en** som du fick av läraren. Du behöver inte använda registrerings-URL:en efter att du har slutfört registreringen. Använd i stället URL:en: [https://labs.azure.com](https://labs.azure.com). Internet Explorer 11 stöds inte ännu. 
1. Logga in på tjänsten med ditt skolkonto för att slutföra registreringen. 

    > [!NOTE]
    > En Microsoft-konto krävs för att använda Azure Lab Services. Om du försöker använda en icke-Microsoft-konto, t. ex. Yahoo eller Google-konton för att logga in på portalen, följer du anvisningarna för att skapa ett Microsoft-konto som kommer att länkas till din icke-Microsoft-konto. Följ sedan stegen för att slutföra registrerings processen. 
1. När registreringen är klar kontrollerar du att du ser den virtuella datorn för det labb som du har åtkomst till. 
1. Vänta tills den virtuella datorn är klar. Observera följande fält på panelen VM:
    1. Längst upp i panelen ser du **namnet på labbet**.
    1. Till höger visas ikonen som representerar den virtuella datorns **operativ system (OS)** . I det här exemplet är det Windows-operativsystem. 
    1. Förlopps indikatorn i panelen visar antalet timmar som använts för antalet tilldelade [kvot timmar](how-to-configure-student-usage.md#set-quotas-for-users) . Den här gången är den ytterligare tid som tilldelas dig utöver den schemalagda tiden för labbet. 
    1. Du ser ikoner/knappar längst ned i panelen för att starta/stoppa den virtuella datorn och ansluta till den virtuella datorn. 
    1. Till höger om knapparna visas statusen för den virtuella datorn. Bekräfta att du ser statusen för den virtuella datorn har **stoppats**. 

        ![Virtuell dator i stoppat tillstånd](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>Starta den virtuella datorn
1. **Starta** den virtuella datorn genom att välja den första knappen som visas i följande bild. Den här processen tar lite tid.  

    ![Starta den virtuella datorn](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Bekräfta att statusen för den virtuella datorn är inställd på att **köras**. 

    ![Virtuell dator i körnings läge](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Observera att ikonen för den första knappen har ändrats för att representera en **stopp** åtgärd. Du kan välja den här knappen för att stoppa den virtuella datorn. 

## <a name="connect-to-the-vm"></a>Anslut till VM:en

1. Välj den andra knappen som visas i följande bild för att **ansluta** till Labbets VM. 

    ![Ansluta till VM](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Gör något av följande: 
    1. För virtuella **Windows** -datorer sparar du **RDP** -filen på hård disken. Öppna RDP-filen för att ansluta till den virtuella datorn. Använd det **användar namn** och **lösen ord** som du får från din lärare/lärare för att logga in på datorn. 
    3. För virtuella **Linux** -datorer kan du använda **SSH** eller **RDP** (om det är aktiverat) för att ansluta till dem. Mer information finns i [aktivera anslutning till fjärr skrivbord för Linux-datorer](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du åtkomst till ett klassrumslabb med registreringslänken du får från din lärare/professor.

Som labbägare behöver du se vem som har registrerats med ditt labb och spåra användningen av virtuella datorer. Gå vidare till nästa självstudie och lär dig hur du spårar användningen av labbet:

> [!div class="nextstepaction"]
> [Spåra användningen av ett labb](tutorial-track-usage.md) 
