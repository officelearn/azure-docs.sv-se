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
ms.openlocfilehash: 2e34aef9a6f184c60fbb23023f798941a1fc11da
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894436"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Självstudier: Få åtkomst till ett klassrumslabb i Azure Lab Services
I den här självstudien ansluter du som student till en virtuell dator (VM) i ett klassrumslabb. 

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Använd registreringslänk 
> * Ansluta till den virtuella datorn

## <a name="use-the-registration-link"></a>Använd registreringslänken

1. Gå till **registrerings-URL:en** som du fick av läraren. Du behöver inte använda registrerings-URL:en efter att du har slutfört registreringen. Använd i stället URL:en: [https://labs.azure.com](https://labs.azure.com). 
1. Logga in på tjänsten med ditt skolkonto för att slutföra registreringen. 
2. När registreringen är klar kontrollerar du att du ser den virtuella datorn för det labb som du har åtkomst till. 
3. Vänta tills den virtuella datorn är klar och **starta** sedan den virtuella datorn. Den här processen tar lite tid.  

    ![Starta den virtuella datorn](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

1. Välj **Anslut** i panelen för den virtuella datorn för det labb som du vill komma åt. 

    ![Ansluta till virtuell dator](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Spara RDP-filen till hårddisken och öppna den (förutsatt att det är en virtuell Windows-dator)
3. Använd det **användarnamn** och **lösenord** som du fick av läraren och logga in på datorn. 

## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du åtkomst till ett klassrumslabb med registreringslänken du får från din lärare/professor.

Som labbägare behöver du se vem som har registrerats med ditt labb och spåra användningen av virtuella datorer. Gå vidare till nästa självstudie och lär dig hur du spårar användningen av labbet:

> [!div class="nextstepaction"]
> [Spåra användningen av ett labb](tutorial-track-usage.md) 