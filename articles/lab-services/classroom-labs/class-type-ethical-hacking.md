---
title: Konfigurera ett etiskt hackningslabb med Azure Lab Services | Microsoft-dokument
description: Lär dig hur du konfigurerar ett labb med Azure Lab Services för att lära ut etiska dataintrång.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 2b600edc4c360a2b2990be34e44bb8fbd1c8f721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133172"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Inrätta ett labb för att lära etiska hacking klass 
Den här artikeln visar hur du ställer in en klass som fokuserar på kriminalteknik sidan av etiska hacking. Penetrationstester, en metod som används av den etiska hacking community, inträffar när någon försöker få tillgång till systemet eller nätverket för att visa sårbarheter som en skadlig angripare kan utnyttja. 

I en etisk hacking klass, kan eleverna lära sig moderna tekniker för att försvara sig mot sårbarheter. Varje elev får en virtuell dator värd för Windows Server som har två kapslade virtuella datorer – en virtuell dator med [Metasploitable3-avbildning](https://github.com/rapid7/metasploitable3) och en annan dator med [Kali Linux-avbildning.](https://www.kali.org/) Den virtuella den metasploitable virtuella datorn används för att utnyttja ändamål och Kali virtuell maskin ger tillgång till de verktyg som behövs för att utföra kriminaltekniska uppgifter.

Denna artikel har två huvudavsnitt. Det första avsnittet beskriver hur du skapar klassrumslabbet. Det andra avsnittet beskriver hur du skapar malldatorn med kapslad virtualisering aktiverad och med de verktyg och bilder som behövs. I det här fallet en Metasploitable bild och en Kali Linux-avbildning på en maskin som har Hyper-V aktiverat för att vara värd för avbildningarna.

## <a name="lab-configuration"></a>Labbkonfiguration
För att konfigurera det här labbet behöver du en Azure-prenumeration för att komma igång. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. När du har fått en Azure-prenumeration kan du antingen skapa ett nytt labbkonto i Azure Lab Services eller använda ett befintligt konto. Se följande självstudie för att skapa ett nytt labbkonto: [Handledning för att konfigurera ett labbkonto](tutorial-setup-lab-account.md).

Följ [den här självstudien](tutorial-setup-classroom-lab.md) om du vill skapa ett nytt labb och sedan använda följande inställningar:

| Storlek för virtuell dator | Bild |
| -------------------- | ----- | 
| Medium (kapslad virtualisering) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Mallmaskin 

När malldatorn har skapats startar du datorn och ansluter till den för att slutföra följande tre huvuduppgifter. 
 
1. Ställ in datorn för kapslad virtualisering. Det gör att alla lämpliga windows-funktioner, som Hyper-V, och ställer in nätverk för Hyper-V-bilder för att kunna kommunicera med varandra och Internet.
2. Ställ in [Kali](https://www.kali.org/) Linux-avbildningen. Kali är en Linux-distribution som innehåller verktyg för penetrationstestning och säkerhetsgranskning.
3. Ställ in metasploitable bilden. I det här exemplet används [metasploitable3-avbildningen.](https://github.com/rapid7/metasploitable3) Den här bilden skapas för att avsiktligt ha säkerhetsproblem.

Ett skript som automatiserar de uppgifter som beskrivs ovan finns på [Lab Services Ethical Hacking Scripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking).

### <a name="prepare-template-machine-for-nested-virtualization"></a>Förbereda malldatorn för kapslad virtualisering
Följ instruktionerna i [den här artikeln](how-to-enable-nested-virtualization-template-vm.md) för att förbereda mallens virtuella dator för kapslad virtualisering. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Konfigurera en kapslad virtuell dator med Kali Linux Image
Kali är en Linux-distribution som innehåller verktyg för penetrationstestning och säkerhetsgranskning.

1. Ladda ner [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)bild från .  
    1. Ladda ner **Kali Linux Hyper-V 64 Bitar** för Hyper-V.
    1. Extrahera 0,7z-filen.  Om du inte redan har 7 zip, ladda ner den från [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html). Kom ihåg platsen för den extraherade mappen när du behöver den senare.
2. Öppna **Hyper-V Manager** från administrationsverktyg.
1. Välj **Åtgärd**och välj sedan **Importera virtuell dator**. 
1. På sidan **Hitta mapp** i guiden Importera **virtuell dator** väljer du platsen för den extraherade mappen som innehåller Kali Linux-avbildningen.

    ![Dialogrutan Sök efter mapp](../media/class-type-ethical-hacking/locate-folder.png)
1. På sidan **Välj virtuell dator** väljer du Kali Linux-avbildningen.  I det här fallet är bilden **kali-linux-2019.3-hyperv**.

    ![Välj kali-bild](../media/class-type-ethical-hacking/select-kali-image.png)
1.  På sidan **Välj importtyp** väljer du **Kopiera den virtuella datorn (skapa ett nytt unikt ID)**.

    ![Välj importtyp](../media/class-type-ethical-hacking/choose-import-type.png)
1. Acceptera standardinställningarna på **Välj mappar för filer med virtuella datorer** och välj mappar för att lagra virtuella **hårddiskar** sidor, och välj sedan **Nästa**.
1. På sidan **Anslut nätverk** väljer du **LabServicesSwitch** som skapats tidigare i avsnittet Förbered mall för **kapslad virtualisering** i den här artikeln och väljer sedan **Nästa**.

    ![Anslut nätverkssida](../media/class-type-ethical-hacking/connect-network.png)
1. Välj **Slutför** på sidan **Sammanfattning.** Vänta tills kopierings- och importåtgärderna har slutförts. Kali Linux virtuella maskinen kommer nu att finnas tillgänglig i Hyper-V.
1. Välj -> **Åtgärdsstart**i **Action** **Hyper-V-hanteraren**och välj sedan -> **Åtgärdsanslutning** för att ansluta till den virtuella datorn. **Action**  
12. Standardanvändarnamnet `root` är och `toor`lösenordet är . 

    > [!NOTE]
    > Om du behöver låsa upp bilden trycker du på CTRL och drar musen uppåt.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Konfigurera en kapslad virtuell dator med metasploabilabel avbildning  
Rapid7 Metasploitable image är en bild som avsiktligt konfigureras med säkerhetsproblem. Du ska använda den här bilden för att testa och hitta problem. Följande instruktioner visar hur du använder en förskapad Metasploitable bild. Men om en nyare version av metasploitable [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3)bilden behövs, se .

1. Navigera [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)till . Fyll i formuläret för att hämta bilden och välj **knappen Skicka.**
1. Välj knappen **Hämta metasploabla nu.**
1. När zip-filen hämtas extraherar du zip-filen och kommer ihåg platsen.
1. Konvertera den extraherade vmdk-filen till en vhdx-fil så att du kan använda med Hyper-V. Det gör du genom att öppna PowerShell med administratörsbehörighet och navigera till mappen där vmdk-filen finns och följ följande instruktioner:
    1. Hämta [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497)och kör filen mvmc_setup.msi när du uppmanas att göra det.
    1. Importera PowerShell-modulen.  Standardplatsen där modulen är installerad är C:\Program Files\Microsoft Virtual Machine Converter\

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. Konvertera vmdk till en vhd-fil som kan användas av Hyper-V. Den här åtgärden kan ta flera minuter.
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. Kopiera de nyskapade metasploitable.vhdx till C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\. 
1. Skapa en ny virtuell Hyper-V-dator.
    1. Öppna **Hyper-V-hanteraren**.
    1. Välj **Åtgärd** -> **ny** -> virtuell**dator**.
    1. Klicka på **Nästa**på sidan **Innan du börjar** i guiden Ny virtuell **dator**.
    1. På sidan **Ange namn och plats** anger du **Metasploitable** för **namnet**och väljer **Nästa**.

        ![Guiden Ny vm-avbildning](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. På sidan **Ange generering** godkänner du standardinställningarna och väljer **Nästa**.
    1. På sidan **Tilldela minne** anger du **512 MB** för **startminnet**och väljer **Nästa**. 

        ![Tilldela minnessida](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. Lämna anslutningen som **Ansluten**på sidan **Konfigurera nätverk** . Du konfigurerar nätverkskortet senare.
    1. På sidan **Anslut virtuell hårddisk** väljer du Använd en befintlig virtuell **hårddisk**. Bläddra till platsen för filen **metasploitable.vhdx** som skapades i föregående steg och välj **Nästa**. 

        ![Sidan Ansluta disk för virtuellt nätverk](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. På sidan **Slutför guiden Ny virtuell dator** och välj **Slutför**.
    1. När den virtuella datorn har skapats väljer du den i Hyper-V-hanteraren. Slå inte på maskinen än.  
    1. Välj **Åtgärdsinställningar** -> **Settings**.
    1. Välj **Lägg till maskinvara**i dialogrutan Inställningar för **metasploabla** . 
    1. Välj **Äldre nätverkskort**och välj **Lägg till**.

        ![Sida för nätverkskort](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. På sidan **Äldre nätverkskort** väljer du **LabServicesSwitch** för inställningen **Virtuell växel** och väljer **OK**. LabServicesSwitch skapades när malldatorn för Hyper-V förbereddes i avsnittet **Förbered mall för kapslad virtualisering.**

        ![Sida med äldre nätverkskort](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Metasploitable bilden är nu klar för användning. Välj -> **Åtgärdsstart**i **Action** **Hyper-V-hanteraren**och välj sedan -> **Åtgärdsanslutning** för att ansluta till den virtuella datorn. **Action**  Standardanvändarnamnet är **msfadmin** och lösenordet är **msfadmin**. 


Mallen är nu uppdaterad och har bilder som behövs för en etisk hacking penetrationstestning klass, en bild med verktyg för att göra penetrationstester och en annan bild med säkerhetsproblem att upptäcka. Mallbilden kan nu publiceras i klassen. Välj knappen **Publicera** på mallsidan om du vill publicera mallen i labbet.
  

## <a name="cost"></a>Kostnad  
Om du vill uppskatta kostnaden för det här labbet kan du använda följande exempel: 
 
För en klass på 25 elever med 20 timmars schemalagd lektionstid och 10 timmars kvot för läxor eller uppgifter, skulle priset för labbet vara: 

25 studenter * (20 + 10) timmar * 55 lab enheter * 0,01 USD per timme = 412,50 USD. 

Mer information om priser finns i [Azure Lab Services Prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Slutsats
Denna artikel gick igenom stegen för att skapa ett labb för etiska hacking klass. Den innehåller steg för att ställa in kapslad virtualisering för att skapa två virtuella datorer inuti den virtuella värddatorn för penetrerande testning.

## <a name="next-steps"></a>Nästa steg
Nästa steg är vanliga för att konfigurera ett labb:

- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users). 

