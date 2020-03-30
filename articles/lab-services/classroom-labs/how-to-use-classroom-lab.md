---
title: Få åtkomst till ett klassrumslabb i Azure Lab Services | Microsoft Docs
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
ms.topic: how-to
ms.custom: mvc
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: db1e60ccd7e05cb8b6923d6b2ec008cdfb76eaa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501927"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Få åtkomst till ett klassrumslabb i Azure Lab Services
I den här artikeln beskrivs hur du registrerar dig i ett klassrumslabb, visar alla labb som du kan komma åt, startar/stoppar en virtuell dator i labbet och ansluter till den virtuella datorn. 

## <a name="register-to-the-lab"></a>Registrera dig i labbet

1. Gå till **registrerings-URL:en** som du fick av läraren. Du behöver inte använda registrerings-URL:en efter att du har slutfört registreringen. Använd i stället [https://labs.azure.com](https://labs.azure.com)webbadressen: . Internet Explorer 11 stöds inte ännu. 
1. Logga in på tjänsten med ditt skolkonto för att slutföra registreringen. 

    > [!NOTE]
    > Ett Microsoft-konto krävs för att använda Azure Lab Services. Om du försöker använda ditt konto som inte kommer från Microsoft för att logga in på portalen följer du instruktionerna för att skapa ett Microsoft-konto som länkas till ditt konto som inte kommer från Microsoft. Följ sedan stegen för att slutföra registreringsprocessen. 
1. När registreringen är klar kontrollerar du att du ser den virtuella datorn för det labb som du har åtkomst till. 
1. Vänta tills den virtuella datorn är klar. Lägg märke till följande fält på vm-panelen:
    1. Högst upp på panelen ser du **namnet på labbet**.
    1. Till höger ser du ikonen som representerar **operativsystemet (OS)** för den virtuella datorn. I det här exemplet är det Windows OS. 
    1. Du ser ikoner/knappar längst ned på panelen för att starta/stoppa den virtuella datorn och ansluta till den virtuella datorn. 
    1. Till höger om knapparna visas status för den virtuella datorn. Bekräfta att statusen för den virtuella datorn **är Stoppad**.

        ![VM i stoppat tillstånd](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>Starta eller stoppa den virtuella datorn
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
    1. Om du använder en **Mac** för att ansluta till labbet VM följer du instruktionerna i nästa avsnitt. 

## <a name="progress-bar"></a>Förloppsindikator 
Förloppsindikatorn på panelen visar antalet timmar som används mot antalet [kvottimmar](how-to-configure-student-usage.md#set-quotas-for-users) som tilldelats dig. Den här gången är den extra tid som tilldelats dig utöver den schemalagda tiden för labbet. Färgen på förloppsindikatorn och texten under förloppsindikatorn varierar enligt följande scenarier:

- Om en klass pågår (inom schemat för klassen) är förloppsindikatorn nedtonad för att representera kvottimmar som inte används. 

    ![Förloppsindikator i grå färg](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Om en kvot inte har tilldelats (noll timmar) visas texten **Tillgänglig endast under klasser** i stället för förloppsindikatorn. 
    
    ![Status när ingen kvot har angetts](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Om du har på **kvot**är förloppsindikatorns färg **röd**. 

    ![Förloppsindikator i röd färg](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- Färgen på förloppsindikatorn är **blå** när den ligger utanför den schemalagda tiden för labbet och en del av kvottiden har använts. 

    ![Förloppsindikator i blå färg](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>Visa alla klassrumslabb
När du registrerat dig till labben kan du visa alla klassrumslabb genom att utföra följande steg: 

1. Navigera [https://labs.azure.com](https://labs.azure.com)till . Internet Explorer 11 stöds inte ännu. 
2. Logga in på tjänsten med hjälp av det användarkonto som du använde för att registrera dig till labbet. 
3. Bekräfta att du ser alla labb som du har åtkomst till. 

    ![Visa alla labb](../media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
 