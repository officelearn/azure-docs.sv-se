---
title: Få åtkomst till ett klassrumslabb i Azure Lab Services | Microsoft Docs
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
ms.openlocfilehash: 8ea1afbfb90a55ec1c34f5c59cf38ea5417118ff
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54390899"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Få åtkomst till ett klassrumslabb i Azure Lab Services
Artikeln beskriver hur du får åtkomst till ett klassrumslabb, ansluter till den virtuella datorn i labbet och stoppar den virtuella datorn. 

## <a name="register-to-a-lab"></a>Registrera dig till ett labb
1. Gå till **registrerings-URL:en** som du fick av läraren. 
2. Logga in på tjänsten med ditt skolkonto för att slutföra registreringen. 
3. När registreringen är klar kontrollerar du att du ser den virtuella datorn för det labb som du har åtkomst till. 
2. Vänta tills den virtuella datorn är klar och **starta** sedan den virtuella datorn. Den här processen tar lite tid.  

    ![Starta den virtuella datorn](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)


## <a name="view-all-the-classroom-labs"></a>Visa alla klassrumslabb
När du registrerat dig till labben kan du visa alla klassrumslabb genom att utföra följande steg: 

1. Navigera till [https://labs.azure.com](https://labs.azure.com). 
2. Logga in på tjänsten med hjälp av det användarkonto som du använde för att registrera dig till labbet. 
3. Bekräfta att du ser alla labb som du har åtkomst till. 

    ![Visa alla labb](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Ansluta till den virtuella datorn i ett klassrumslabb

1. Starta den virtuella datorn om den inte redan startats, och välj **Starta**.
2. Välj **Anslut** i panelen som representerar den virtuella dator som du vill ha åtkomst till i labbet. 

    ![Visa alla labb](../media/how-to-use-classroom-lab/connect-button.png)
3. Spara RDP-filen (för virtuell Windows-dator) på hårddisken och öppna den. 
4. Använd det **användarnamn** och **lösenord** som du fick av läraren och logga in på datorn. 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Stoppa den virtuella datorn i ett klassrumslabb

Om du vill stoppa den virtuella datorn väljer du **Stoppa** på panelen. När den virtuella datorn har stoppats aktiveras knappen **Starta** i panelen. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
 