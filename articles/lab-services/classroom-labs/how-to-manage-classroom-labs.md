---
title: Hantera klassrum labb i Azure Lab Services | Microsoft Docs
description: Lär dig mer om att skapa och konfigurera ett klassrumslabb, visa alla labb för klassrum, dela registreringslänken med en lab-användare eller ta bort ett labb.
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
ms.date: 06/07/2019
ms.author: spelluru
ms.openlocfilehash: 6ba41132c93ebdb2578bafb100416ca3fe579298
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123290"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Hantera klassrum labb i Azure Lab Services 
Den här artikeln beskriver hur du skapar och tar bort ett klassrumslabb. Den också visar hur du visar alla klassrum-labb i ett labbkonto. 

## <a name="prerequisites"></a>Nödvändiga komponenter
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste du vara medlem i rollen **Lab Creator** i labbkontot. Det konto som du använde för att skapa ett labbkonto läggs automatiskt till i den här rollen. Labbägaren kan lägga till andra användare till rollen Lab Creator genom att använda stegen i följande artikel: [Add a user to the Lab Creator role](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) (Lägga till en användare till rollen Lab Creator).

## <a name="create-a-classroom-lab"></a>Skapa ett klassrumslabb

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). Observera att Internet Explorer 11 inte stöds ännu. 
2. Välj **logga in**. Välj eller ange en **användar-ID** som är medlem av den **Labbskaparen** roll i laboratoriet kontot och ange lösenord. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
3. Utför följande i fönstret **Nytt labb**: 
    1. Ange ett **namn** på ditt labb. 
    2. Ange maximalt **antal virtuella datorer** i labbet. Du kan öka eller minska antalet virtuella datorer i labbet senare. 
    6. Välj **Spara**.

        ![Skapa ett klassrumslabb](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. På sidan för att **välja specifikationer för virtuell dator** utför du följande steg:
    1. Välj en **storlek** för virtuella datorer (VM) som skapas i labbet. För närvarande **små**, **medel**, **medel (virtualisering)** , **stora**, och **GPU** storlekarna är tillåtna. Mer information finns i [VM-storlekar](#vm-sizes) avsnittet.
    1. Välj den **region** där du vill att de virtuella datorerna ska skapas. 
    1. Välj den **VM-avbildning**  som ska användas för att skapa virtuella datorer i labbet. Om du väljer en Linux-avbildning, ser du ett alternativ för att aktivera anslutning till fjärrskrivbord för den. Mer information finns i [aktivera anslutning till fjärrskrivbord för Linux](how-to-enable-remote-desktop-linux.md).
    1. Välj **Nästa**.

        ![Ange VM-specifikationer](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. På sidan **Ange autentiseringsuppgifter** anger du standardautentiseringsuppgifter för alla virtuella datorer i labbet. 
    1. Ange **namnet på användaren** för alla virtuella datorer i labbet.
    2. Ange **lösenordet** för användaren. 

        > [!IMPORTANT]
        > Anteckna namnet och lösenordet. De kommer inte att visas igen.
    3. Inaktivera **Använd samma lösenord för alla virtuella datorer** om du vill studenter att ange sina egna lösenord. Det här steget är **valfritt**. 

        En lärare kan du använda samma lösenord för alla virtuella datorer i labbet, eller att tillåta studenter att ange lösenord för sina virtuella datorer. Den här inställningen aktiveras som standard för alla Windows och Linux-avbildningar förutom Ubuntu. När du väljer **Ubuntu** VM, den här inställningen är inaktiverad, så att eleverna uppmanas att ange ett lösenord när de loggar in för första gången.
    1. Välj **Skapa**. 

        ![Ange autentiseringsuppgifter](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. På sidan **Konfigurera mall** kan du se statusen för labbskapandeprocessen. Det tar upp till 20 minuter att skapa mallen i labbet. En mall i ett labb är en basavbildning av en virtuell dator som alla användares virtuella datorer skapas från. Konfigurera mallen för virtuella datorer så att den är konfigurerad med exakt det som du vill förse labbanvändarna med.  

    ![Konfigurera mall](../media/tutorial-setup-classroom-lab/configure-template.png)
7. När konfigurationen av mallen har slutförts visas följande sida: 

    ![Konfigurera mallsidan när det är klart](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Följande steg är valfria i den här självstudien: 
    2. Anslut till mallen för den virtuella datorn genom att välja **Anslut**. Om det är en Linux VM-mallen kan välja du om du vill ansluta med SSH eller RDP (om RDP har aktiverats).
    1. Välj **Återställ lösenord** att återställa lösenordet för den virtuella datorn. 
    1. Installera och konfigurera programvaran på mallen för den virtuella datorn. 
    1. **Stoppa** den virtuella datorn.  
    1. Ange en **beskrivning** för mallen
9. Välj **Nästa** på mallsidan. 
10. På sidan **Publicera mallen** utför du någon av följande åtgärder. 
    1. Om du vill publicera mallen omedelbart markerar du kryssrutan för *I understand I can't modify the template after publishing (Jag förstår att jag inte kan ändra mallen efter publicering). Den här processen kan bara göras en gång och den kan ta upp till en timme*. Välj sedan **Publicera**.  Publicera mallen så att instanser av den virtuella malldatorn är tillgängliga för labbanvändarna.

        > [!WARNING]
        > När du väl har publicerat kan du inte ångra publiceringen. 
    2. Om du vill publicera senare väljer **Spara till senare**. Du kan publicera mallen för den virtuella datorn när guiden har slutförts. Mer information om hur du konfigurerar och publicerar när guiden slutförts, finns mer information om hur du konfigurerar och publicerar när guiden slutförts, finns i mallen i avsnittet Publicera den [hantera klassrum labs](how-to-manage-classroom-labs.md) artikeln.

        ![Publicera mall](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Du ser **förloppet för publiceringen**  av mallen. Den här processen kan ta upp till en timma. 

    ![Publicera mall – förlopp](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Du ser följande sida när mallen har publicerats. Välj **Done** (Klar).

    ![Publicera mall – lyckades](../media/tutorial-setup-classroom-lab/publish-success.png)
1. **Instrumentpanelen** för labbet visas. 
    
    ![Instrumentpanel för klassrumslabb](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Växla till sidan **Virtuella datorer** och bekräfta att du ser virtuella datorer som är i tillståndet **Otilldelad**. De här virtuella datorerna har inte tilldelats till studenter ännu. De måste vara i tillstånd **Stoppad**. Du kan starta en virtuell dator för studenter, ansluta till den virtuella datorn, stoppa den virtuella datorn och ta bort den virtuella datorn på den här sidan. Du kan starta dem på den här sidan eller låta studenterna starta de virtuella datorerna. 

    ![Virtuella datorer i stoppat tillstånd](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

### <a name="vm-sizes"></a>VM-storlekar  

| Storlek | Kärnor | RAM | Beskrivning | 
| ---- | ----- | --- | ----------- | 
| Liten | 2 | 3,5 GB | Den här storleken är bäst lämpade för kommandoraden, öppna webbläsare, webbservrar för låg trafik, små till medelstora databaser. |
| Medel | 4 | 7 GB | Den här storleken är bäst lämpade för relationsdatabaser, minnesintern cachelagring och analys | 
| Medel (kapslad virtualisering) | 4 | 16 GB | Den här storleken är bäst lämpade för relationsdatabaser, minnesintern cachelagring och analys. Den här storleken har även stöd för kapslad virtualisering. <p>Den här storleken kan användas i situationer där varje elev behöver flera virtuella datorer. Lärare kan använda kapslad virtualisering för att ställa in några små kapslade virtuella datorer i den virtuella datorn. </p> |
| Stor | 8 | 32 GB | Den här storleken är bäst lämpade för program som behöver snabbare processorer, bättre lokala diskprestanda, stora databaser, stort minne, cacheminnen. Den här storleken stöder också kapslad virtualisering |  
| GPU | 12 | 112 GB | Den här storleken är bäst lämpade för arbetsbelastningar för beräkningsintensiva, grafik och visualisering | 

## <a name="view-all-classroom-labs"></a>Visa alla klassrum labs
1. Gå till [Azure Lab Services portal](https://labs.azure.com).
2. Välj **logga in**. Välj eller ange en **användar-ID** som är medlem av den **Labbskaparen** roll i laboratoriet kontot och ange lösenord. Azure Lab Services har stöd för organisationskonton och Microsoft-konton. 
3. Kontrollera att du ser alla labs i den valda labbkonto. 

    ![Alla labs](../media/how-to-manage-classroom-labs/all-labs.png)
3. Använd den nedrullningsbara listan högst upp för att välja ett annat labbkonto. Du kan se labs i den valda labbkonto. 

## <a name="delete-a-classroom-lab"></a>Ta bort ett klassrumslabb
1. Välj tre punkter (...) i hörnet på panelen för övningen. 

    ![Välja labbet](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Välj **Ta bort**. 

    ![Ta bort knapp](../media/how-to-manage-classroom-labs/delete-button.png)
3. På den **Delete lab** dialogrutan **ta bort**. 

    ![Ta bort dialogrutan](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>Växla till en annan klassrumslabb
Om du vill växla till en annan klassrumslabb från aktuellt, Välj den nedrullningsbara listan över labbar i labbkonto högst upp.

![Välj labbet från listrutan överst](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som en lab-användare åtkomst till labb för klassrum](how-to-use-classroom-lab.md)

