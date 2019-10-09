---
title: Konfigurera ett etiska hackning-labb med Azure Lab Services | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb med Azure Lab Services för att under Visa etiska hackning.
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
ms.openlocfilehash: df24f846f1600685803fdd485f1810d66e32ae37
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028674"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Konfigurera ett labb för att lära dig etiska hackning-klassen 
Den här artikeln visar hur du konfigurerar en klass som fokuserar på data utredning sida av etiska hackning. Testning av inträngning, en övning som används av den etiska hackning-communityn, inträffar när någon försöker få åtkomst till systemet eller nätverket för att Visa sårbarheter som en skadlig angripare kan utnyttja. 

I en etisk hackning-klass kan studenter lära sig moderna tekniker för att försvara mot sårbarheter. Varje elev får en virtuell Windows Server-värd dator som har två kapslade virtuella datorer – en virtuell dator med **Metaspoiltable** -avbildning och en annan dator med [Kali Linux](https://www.kali.org/) -avbildning. Den virtuella Metasploitable-datorn används för att utnyttja och Kali virtuell dator ger till gång till de verktyg som behövs för att köra kriminal tekniska-uppgifter.

Den här artikeln innehåller två huvud avsnitt. Det första avsnittet beskriver hur du skapar klass rummets labb. I det andra avsnittet beskrivs hur du skapar en mall med kapslad virtualisering aktive rad och de verktyg och bilder som behövs. I det här fallet en Metasploitable avbildning och en Kali Linux-avbildning på en dator som har Hyper-V aktiverat som värd för avbildningarna.

## <a name="lab-configuration"></a>Labb konfiguration
Du måste ha en Azure-prenumeration för att komma igång för att kunna konfigurera det här labbet. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. När du har skaffat en Azure-prenumeration kan du antingen skapa ett nytt labb konto i Azure Lab Services eller använda ett befintligt konto. Se följande självstudie för att skapa ett nytt labb konto: [Självstudie för att konfigurera ett labb konto](tutorial-setup-lab-account.md).

Följ [den här självstudien](tutorial-setup-classroom-lab.md) för att skapa ett nytt labb och Använd sedan följande inställningar:

| Storlek på virtuell dator | Image |
| -------------------- | ----- | 
| Medium (kapslad virtualisering) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Mall dator 

När du har skapat mallen startar du datorn och ansluter till den för att slutföra följande tre huvudsakliga uppgifter. 
 
1. Konfigurera datorn för kapslad virtualisering. Det möjliggör alla lämpliga Windows-funktioner, till exempel Hyper-V, och konfigurerar nätverk för Hyper-V-avbildningar för att kunna kommunicera med varandra och Internet.
2. Konfigurera [Kali](https://www.kali.org/) Linux-avbildningen. Kali är en Linux-distribution som innehåller verktyg för inträngande testning och säkerhets granskning.
3. Konfigurera Metasploitable-avbildningen. I det här exemplet används [Metasploitable3](https://github.com/rapid7/metasploitable3) -avbildningen. Den här avbildningen skapas för att syftet ska kunna innehålla säkerhets risker.

### <a name="prepare-template-machine-for-nested-virtualization"></a>Förbered mall för kapslad virtualisering
Följ anvisningarna i [den här artikeln](how-to-enable-nested-virtualization-template-vm.md) för att förbereda din virtuella mall för en kapslad virtualisering. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Konfigurera en kapslad virtuell dator med Kali Linux-avbildning
Kali är en Linux-distribution som innehåller verktyg för inträngande testning och säkerhets granskning.

1. Ladda ned bild från [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/).  
    1. Ladda ned **Kali Linux Hyper-V 64-biten** för Hyper-v.
    1. Extrahera. 7z-filen.  Om du inte redan har 7 zip laddar du ned det från [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html). Kom ihåg platsen för den extraherade mappen eftersom du behöver den senare.
2. Öppna **Hyper-V Manager** från administrations verktyg.
1. Välj **åtgärd**och välj sedan **Importera virtuell dator**. 
1. På sidan **hitta mapp** i guiden **Importera virtuell dator** väljer du platsen för den extraherade mappen som innehåller Kali Linux-avbildningen.

    ![Dialog rutan Leta upp mapp](../media/class-type-ethical-hacking/locate-folder.png)
1. På sidan **Välj virtuell dator** väljer du Linux-avbildningen Kali.  I det här fallet är avbildningen **Kali – Linux-2019,3-HyperV**.

    ![Välj Kali-avbildning](../media/class-type-ethical-hacking/select-kali-image.png)
1.  På sidan **Välj import typ** väljer **du kopiera den virtuella datorn (skapa ett nytt unikt ID)** .

    ![Välj import typ](../media/class-type-ethical-hacking/choose-import-type.png)
1. Godkänn standardinställningarna på **Välj mappar för filer för virtuella datorer** och **Välj mappar för att lagra sidor för virtuella hård diskar** och välj sedan **Nästa**.
1. På sidan **Anslut nätverk** väljer du **LabServicesSwitch** som skapades tidigare i avsnittet **förbereda mall för kapslad virtualisering** i den här artikeln och väljer sedan **Nästa**.

    ![Sidan Anslut nätverk](../media/class-type-ethical-hacking/connect-network.png)
1. Välj **Slutför** på sidan **Sammanfattning** . Vänta tills kopierings-och import åtgärderna har slutförts. Den virtuella Linux-datorn Kali är nu tillgänglig i Hyper-V.
1. Från **Hyper-V Manager**väljer du **åtgärd** -> **Start**och väljer sedan **åtgärd** -> **Anslut** för att ansluta till den virtuella datorn.  
12. Standard användar namnet är `root` och lösen ordet är `toor`. 

    > [!NOTE]
    > Om du behöver låsa upp bilden trycker du på CTRL-tangenten och drar musen uppåt.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Konfigurera en kapslad virtuell dator med Metasploitable-avbildning  
Rapid7 Metasploitable-avbildningen är en avbildning som har kon figurer ATS med säkerhets problem. Du använder den här bilden för att testa och hitta problem. Följande instruktioner visar hur du använder en redan skapad Metasploitable-avbildning. Men om en nyare version av Metasploitable-avbildningen behövs, se [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3).

1. Navigera till [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html). Fyll i formuläret för att ladda ned avbildningen och välj knappen **Skicka** .
1. Välj knappen **Hämta Metasploitable nu** .
1. När zip-filen har laddats ned extraherar du zip-filen och kommer ihåg platsen.
1. Konvertera den extraherade VMDK-filen till en VHDX-fil så att du kan använda med Hyper-V. Det gör du genom att öppna PowerShell med administratörs behörighet och navigera till den mapp där VMDK-filen finns och följa de här anvisningarna:
    1. Hämta [konverteraren för Microsoft Virtual Machine](https://www.microsoft.com/download/details.aspx?id=42497)och kör mvmc_setup. msi-filen när du uppmanas till det.
    1. Importera PowerShell-modulen.  Standard platsen där modulen är installerad är C:\Program\Microsoft Virtual Machine Converter \

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. Omvandla VMDK till en VHD-fil som kan användas av Hyper-V. Den här åtgärden kan ta flera minuter.
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. Kopiera den nyligen skapade metasploitable. vhdx till C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\. 
1. Skapa en ny virtuell Hyper-V-dator.
    1. Öppna **Hyper-V Manager**.
    1. Välj **åtgärd** -> **ny** -> **virtuell dator**.
    1. På sidan **innan du börjar** i **guiden Ny virtuell dator**klickar du på **Nästa**.
    1. På sidan **Ange namn och plats** anger du **Metasploitable** som **namn**och väljer **Nästa**.

        ![Guiden ny virtuell dator avbildning](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. På sidan **Ange generation** accepterar du standardvärdena och väljer **Nästa**.
    1. På sidan **tilldela minne** anger du **512 MB** som **Start minne**och väljer **sedan nästa**. 

        ![Sidan tilldela minne](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. På sidan **Konfigurera nätverk** låter du anslutningen vara **ansluten**. Du konfigurerar nätverkskortet senare.
    1. På sidan **Anslut virtuell hård disk** väljer du **Använd en befintlig virtuell hård disk**. Bläddra till platsen för **metasploitable. vhdx** -filen som skapades i föregående steg och välj **Nästa**. 

        ![Sidan Anslut virtuell nätverks disk](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. På sidan **Slutför guiden Ny virtuell dator** och välj **Slutför**.
    1. När den virtuella datorn har skapats väljer du den i Hyper-V Manager. Aktivera inte datorn ännu.  
    1. Välj **åtgärd** -> **Inställningar**.
    1. I dialog rutan **Inställningar för Metasploitable** väljer du **Lägg till maskin vara**. 
    1. Välj **bakåtkompatibelt**nätverkskort och välj **Lägg till**.

        ![Sidan nätverkskort](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. På sidan **bakåtkompatibelt** nätverkskort väljer du **LabServicesSwitch** för den **virtuella växel** inställningen och väljer **OK**. LabServicesSwitch skapades när du förbereder mallen för Hyper-V i avsnittet **Förbered mall för kapslad virtualisering** .

        ![Sidan bakåtkompatibelt nätverkskort](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Metasploitable-avbildningen är nu redo att användas. Från **Hyper-V Manager**väljer du **åtgärd** -> **Start**och väljer sedan **åtgärd** -> **Anslut** för att ansluta till den virtuella datorn.  Standard användar namnet är **msfadmin** och lösen ordet är **msfadmin**. 


Mallen har nu uppdaterats och innehåller bilder som behövs för en etisk hackning-inträngning test klass, en bild med verktyg för att göra inträngande testning och en annan bild med säkerhets problem som kan upptäckas. Nu kan du publicera mallfilen till-klassen. Klicka på knappen **publicera** på mallsida för att publicera mallen i labbet.
  

## <a name="cost"></a>Kostnad  
Om du vill uppskatta kostnaden för det här labbet kan du använda följande exempel: 
 
För en klass av 25 studenter med 20 timmar schemalagda klass tider och 10 timmars kvot för läxor eller tilldelningar, skulle priset för labbet vara: 

25 studenter * (20 + 10) timmar * 55 lab-enheter * 0,01 USD per timme = 412,50 USD. 

Mer information om priser finns i [Azure Lab Services prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Sammanfattning
Den här artikeln gick dig genom stegen för att skapa ett labb för en etisk hackning-klass. Den innehåller steg för att konfigurera kapslad virtualisering för att skapa två virtuella datorer i den virtuella värd datorn för att tränga in test.

## <a name="next-steps"></a>Nästa steg
Nästa steg är vanliga för att ställa in labb:

- [Lägga till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](tutorial-setup-classroom-lab.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-postregistrering länkar till studenter](tutorial-setup-classroom-lab.md#send-an-email-with-the-registration-link). 

