---
title: Spåra användning av ett labb i Azure Lab Services | Microsoft Docs
description: I den här självstudien spårar du som labbskapare/ägare användningen av ditt labb.
services: lab-services
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
ms.openlocfilehash: 55c7e2f30324fe7878e38f1129c63c6e0c51a75f
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134840"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Självstudie: Spåra användning av ett labb i Azure Lab Services
Den här kursen visar hur en labbskapare/ägare kan spåra användningen av ett labb.

I de här självstudierna gör du följande:

> [!div class="checklist"]
> * Visa användare som har registrerats med ditt labb
> * Visa användningen av virtuella datorer i labbet
> * Hantera virtuella studentdatorer 


## <a name="view-registered-users"></a>Visa registrerade användare

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). 
2. Välj **Logga in** och ange dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton.
3. På sidan **My labs** (Mina labb) väljer du det labb som du vill spåra användningen för. 
4. Välj **Användare** på den vänstra menyn eller i panelen **Användare**. Du kan se studenter som har registrerats med ditt labb.  

    ![Registrerade användare](../media/tutorial-track-usage/registered-users.png)

    Mer information om hur du lägger till och hanterar användare för labbet finns i [lägga till och hantera labb användare](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms"></a>Visa användningen av virtuella datorer

1. Välj **Virtuella datorer** på menyn till vänster. 
2. Kontrollera att du ser status för virtuella datorer och det antal timmar som de virtuella datorerna har körts. Den tid som en labbägare tillbringar på en virtuell studentdator räknas inte mot den användningstid som visas i den sista kolumnen. 

    ![VM-användning](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Hantera virtuella studentdatorer 
På den här sidan kan du starta, stoppa eller återställa elev-VM: ar med hjälp av kontrollerna i kolumnen **tillstånd** eller i verktygsfältet.

![Kontroller för virtuella datorer](../media/tutorial-track-usage/vm-controls.png)

Mer information om hur du hanterar en pool för virtuella datorer för labbet finns i [Konfigurera och hantera en pool för virtuella datorer](how-to-set-virtual-machine-passwords.md).

## <a name="next-steps"></a>Nästa steg
Mer information om klassrumslabb finns i artiklarna under [Instruktionsguider](how-to-manage-lab-accounts.md).
