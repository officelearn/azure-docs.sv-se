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
ms.date: 11/14/2018
ms.author: spelluru
ms.openlocfilehash: 49d5761e3e37e1265938d1f1b27324de667a13ca
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51707103"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Självstudie: Spåra användning av ett labb i Azure Lab Services
Den här kursen visar hur en labbskapare/ägare kan spåra användningen av ett labb.

I de här självstudierna gör du följande:

> [!div class="checklist"]
> * Visa användare som har registrerats med ditt labb
> * Visa användningen av virtuella datorer i labbet
> * Hantera virtuella studentdatorer 


## <a name="view-users-registered-with-the-lab"></a>Visa användare som har registrerats med labbet

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). 
2. Välj **Logga in** och ange dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton.
3. På sidan **My labs** (Mina labb) väljer du det labb som du vill spåra användningen för. 
4. Välj fliken **Användare**. Du kan se studenter som har registrerats med ditt labb. Välj **Registreringslänk**, kopiera länken och skicka den till nya studenter som ännu inte har registrerats med ditt labb. 

    ![Registrerade användare](../media/tutorial-track-usage/registered-users.png)

## <a name="view-the-usage-of-vms-in-the-lab"></a>Visa användningen av virtuella datorer i labbet 

1. Välj **Virtuella datorer** på menyn till vänster. 
2. Kontrollera att du ser status för virtuella datorer och det antal timmar som de virtuella datorerna har körts. Den tid som du tillbringar på en virtuell studentdator räknas inte mot den användningstid som visas i den sista kolumnen. 

    ![VM-användning](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Hantera virtuella studentdatorer 
När du hovrar musen över en rad i listan med virtuella datorer ser du kontroller för att utföra följande uppgifter (enligt bilden i föregående avsnitt): 

- Ansluta till en virtuell dator
- Starta en virtuell dator
- Stoppa en virtuell dator
- Ta bort en virtuell dator



## <a name="next-steps"></a>Nästa steg
Mer information om klassrumslabb finns i artiklarna under [Instruktionsguider](how-to-manage-lab-accounts.md).
