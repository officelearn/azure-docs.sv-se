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
ms.date: 07/30/2018
ms.author: spelluru
ms.openlocfilehash: dadc90e6a39b9e9689bab0249e6496fdea6f6205
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450223"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Självstudie: Åtkomst till ett klassrumslabb i Azure Lab Services
I den här självstudien ansluter du som student till en virtuell dator (VM) i ett klassrumslabb. 

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Använd registreringslänk 
> * Ansluta till den virtuella datorn

## <a name="use-the-registration-link"></a>Använd registreringslänken

1. Gå till **registrerings-URL:en** som du fick av läraren. 
2. Logga in på tjänsten med ditt skolkonto för att slutföra registreringen. 
3. När registreringen är klar kontrollerar du att du ser den virtuella datorn för det labb som du har åtkomst till. 
2. Vänta tills den virtuella datorn är klar och **starta** sedan den virtuella datorn.

    ![Starta den virtuella datorn](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

1. Välj **Anslut** i panelen som representerar den virtuella dator som du vill ha åtkomst till i labbet. 

    ![Ansluta till virtuell dator](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Spara RDP-filen på hårddisken och öppna den. 
3. Använd det **användarnamn** och **lösenord** som du fick av läraren och logga in på datorn. 

## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du åtkomst till ett klassrumslabb med registreringslänken du får från din lärare/professor.

Som labbägare behöver du se vem som registreras med ditt labb och spåra användningen av virtuella datorer. Det får du lära dig i nästa självstudie:

> [!div class="nextstepaction"]
> [Spåra användningen av ett labb](tutorial-track-usage.md) 