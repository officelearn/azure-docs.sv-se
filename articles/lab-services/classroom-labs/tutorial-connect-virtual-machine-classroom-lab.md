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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 3be1da54b16a24ce3c4431dfe26eb778cea5c83d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60694682"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Självstudier: Få åtkomst till ett klassrumslabb i Azure Lab Services
I den här självstudien ansluter du som student till en virtuell dator (VM) i ett klassrumslabb. 

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Använd registreringslänk 
> * Ansluta till den virtuella datorn

## <a name="use-the-registration-link"></a>Använd registreringslänken

1. Gå till **registrerings-URL:en** som du fick av läraren. Du behöver inte använda registrerings-URL:en efter att du har slutfört registreringen. Använd i stället URL:en: [https://labs.azure.com](https://labs.azure.com). Observera att Internet Explorer 11 inte stöds ännu. 
1. Logga in på tjänsten med ditt skolkonto för att slutföra registreringen. 
2. När registreringen är klar kontrollerar du att du ser den virtuella datorn för det labb som du har åtkomst till. 
3. Vänta tills den virtuella datorn är klar och **starta** sedan den virtuella datorn. Den här processen tar lite tid.  

    ![Starta den virtuella datorn](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

1. Välj **Anslut** i panelen för den virtuella datorn för det labb som du vill komma åt. 

    ![Ansluta till virtuell dator](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Gör något av följande steg: 
    1. För **Windows** virtuella datorer, spara den **RDP** filen till hårddisken. Öppna RDP-filen för att ansluta till den virtuella datorn. Använd den **användarnamn** och **lösenord** du får från educator/professor att logga in på datorn. 
    3. För **Linux** virtuella datorer som du kan använda **SSH** eller **RDP** (om den är aktiverad) att ansluta till dem. Mer information finns i [aktivera anslutning till fjärrskrivbord för Linux-datorer](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du åtkomst till ett klassrumslabb med registreringslänken du får från din lärare/professor.

Som labbägare behöver du se vem som har registrerats med ditt labb och spåra användningen av virtuella datorer. Gå vidare till nästa självstudie och lär dig hur du spårar användningen av labbet:

> [!div class="nextstepaction"]
> [Spåra användningen av ett labb](tutorial-track-usage.md) 
