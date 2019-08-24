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
ms.date: 08/19/2019
ms.author: spelluru
ms.openlocfilehash: 8d3d08c772c28ff4e3a32e736253ba69b03684f0
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012245"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Få åtkomst till ett klassrumslabb i Azure Lab Services
Den här artikeln beskriver hur du registrerar dig för ett klass rums labb, visar alla labb som du kan komma åt, starta/stoppa en virtuell dator i labbet och ansluta till den virtuella datorn. 

## <a name="register-to-the-lab"></a>Registrera dig för labbet

1. Gå till **registrerings-URL:en** som du fick av läraren. Du behöver inte använda registrerings-URL:en efter att du har slutfört registreringen. Använd i stället URL:en: [https://labs.azure.com](https://labs.azure.com). Internet Explorer 11 stöds inte ännu. 
1. Logga in på tjänsten med ditt skolkonto för att slutföra registreringen. 
2. När registreringen är klar kontrollerar du att du ser den virtuella datorn för det labb som du har åtkomst till. 
3. Vänta tills den virtuella datorn är klar. Observera följande fält på panelen VM:
    1. Längst upp i panelen ser du **namnet på labbet**.
    1. Till höger visas ikonen som representerar den virtuella datorns **operativ system (OS)** . I det här exemplet är det Windows-operativsystem. 
    1. Du ser ikoner/knappar längst ned i panelen för att starta/stoppa den virtuella datorn och ansluta till den virtuella datorn. 
    1. Till höger om knapparna visas statusen för den virtuella datorn. Bekräfta att du ser statusen för den virtuella datorn har **stoppats**.

        ![Virtuell dator i stoppat tillstånd](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>Starta eller stoppa den virtuella datorn
1. **Starta** den virtuella datorn genom att välja den första knappen som visas i följande bild. Den här processen tar lite tid.  

    ![Starta den virtuella datorn](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Bekräfta att statusen för den virtuella datorn är inställd på att **köras**. 

    ![Virtuell dator i körnings läge](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Observera att ikonen för den första knappen har ändrats för att representera en **stopp** åtgärd. Du kan välja den här knappen för att stoppa den virtuella datorn. 

## <a name="connect-to-the-vm"></a>Anslut till VM:en

1. Välj den andra knappen som visas i följande bild för att **ansluta** till Labbets VM. 

    ![Ansluta till virtuell dator](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Gör något av följande: 
    1. För virtuella **Windows** -datorer sparar du **RDP** -filen på hård disken. Öppna RDP-filen för att ansluta till den virtuella datorn. Använd det **användar namn** och **lösen ord** som du får från din lärare/lärare för att logga in på datorn. 
    3. För virtuella **Linux** -datorer kan du använda **SSH** eller **RDP** (om det är aktiverat) för att ansluta till dem. Mer information finns i [aktivera anslutning till fjärr skrivbord för Linux-datorer](how-to-enable-remote-desktop-linux.md). 
    1. Om du använder en **Mac** för att ansluta till den virtuella labb datorn följer du anvisningarna i nästa avsnitt. 

## <a name="connect-to-a-vms-ui-using-rdp-on-a-mac"></a>Ansluta till en virtuell dators användar gränssnitt med RDP på en Mac
Det här avsnittet visar hur en student kan ansluta till en virtuell dators användar gränssnitt (UI) från en Mac med hjälp av RDP. Här följer de snabb steg som följer med information: 

1. Installera Microsoft Fjärrskrivbord på din Mac.
2. Anslut till ett användar gränssnitt för virtuella datorer från din Mac med RDP. 

### <a name="install-microsoft-remote-desktop-on-a-mac"></a>Installera Microsoft Fjärrskrivbord på en Mac
1. Öppna App Store på din Mac och Sök efter **Microsoft fjärrskrivbord**.

    ![Microsoft Fjärrskrivbord](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Installera den senaste versionen av Microsoft Fjärrskrivbord. 

### <a name="access-the-vms-ui-from-your-mac-using-rdp"></a>Få åtkomst till den virtuella datorns användar gränssnitt från din Mac med RDP
1. Öppna den **RDP** -fil som har laddats ned på datorn med **Microsoft fjärrskrivbord** installerat. Den ska börja ansluta till den virtuella datorn. 

    ![Anslut till VM-ANVÄNDARGRÄNSSNITTET](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Välj **Fortsätt** om du får följande fel meddelande. 

    ![Certifikat fel](../media/how-to-use-classroom-lab/certificate-error.png)
1. Du bör se den virtuella datorns användar gränssnitt. 

    > [!NOTE]
    > Följande exempel gäller för en virtuell CentOS Linux-dator. 

    ![ANVÄNDAR GRÄNSSNITT FÖR VIRTUELL DATOR](../media/how-to-use-classroom-lab/vm-ui.png)

## <a name="progress-bar"></a>Förloppsindikator 
Förlopps indikatorn i panelen visar antalet timmar som använts för antalet tilldelade [kvot timmar](how-to-configure-student-usage.md#set-quotas-for-users) . Den här gången är den ytterligare tid som tilldelas dig utöver den schemalagda tiden för labbet. Färgen på förlopps indikatorn och texten under förlopps indikatorn varierar enligt följande scenarier:

- Om en klass pågår (inom schemat för klassen) är förlopps indikatorn nedtonad för att representera kvot timmar som inte används. 

    ![Förlopps indikator i grå färg](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Om en kvot inte är tilldelad (noll timmar) visas bara texten som är **tillgänglig under klasser** i stället för förlopps indikatorn. 
    
    ![Status när ingen kvot har angetts](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Om du tog **slut på kvoten**är färgen på förlopps indikatorn **röd**. 

    ![Förlopps indikator i röd färg](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- Förlopps indikatorns färg är **blå** när den ligger utanför den schemalagda tiden för labbet och en del av kvot tiden har använts. 

    ![Förlopps indikator i blå färg](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>Visa alla klassrumslabb
När du registrerat dig till labben kan du visa alla klassrumslabb genom att utföra följande steg: 

1. Navigera till [https://labs.azure.com](https://labs.azure.com). Internet Explorer 11 stöds inte ännu. 
2. Logga in på tjänsten med hjälp av det användarkonto som du använde för att registrera dig till labbet. 
3. Bekräfta att du ser alla labb som du har åtkomst till. 

    ![Visa alla labb](../media/how-to-use-classroom-lab/all-labs.png)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
 