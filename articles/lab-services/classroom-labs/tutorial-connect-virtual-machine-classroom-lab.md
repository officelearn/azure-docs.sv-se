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
ms.openlocfilehash: 1328835714086dcec71b0e9dd4d1916794f557a6
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54390169"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Självstudier: Få åtkomst till ett klassrumslabb i Azure Lab Services
I den här självstudien ansluter du som student till en virtuell dator (VM) i ett klassrumslabb. 

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Använd registreringslänk 
> * Ansluta till den virtuella datorn

## <a name="use-the-registration-link"></a>Använd registreringslänken

1. Gå till **registrerings-URL:en** som du fick av läraren. 
2. Logga in på tjänsten med ditt skolkonto för att slutföra registreringen. 
3. När registreringen är klar kontrollerar du att du ser den virtuella datorn för det labb som du har åtkomst till. 
2. Vänta tills den virtuella datorn är klar och **starta** sedan den virtuella datorn. Den här processen tar lite tid.  

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