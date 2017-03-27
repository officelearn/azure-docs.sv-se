---
title: "Azure snabbstart – skapa virtuell Windows-dator med Portal | Microsoft Docs"
description: "Azure snabbstart – skapa virtuell Windows-dator med Portal"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/21/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: a13ac5ab425ccbbe53d77cb9f5a8ebf02d009370
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Skapa en virtuell Windows-dator med Azure Portal

Det går att skapa virtuella datorer via Azure Portal. Den här metoden ger ett webbläsarbaserat användargränssnitt för att skapa och konfigurera virtuella datorer och alla relaterade resurser. Den här snabbstarten beskriver hur man skapar en virtuell dator med Azure Portal. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

2. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.

3. Välj **Beräkna** på bladet **Nytt**, välj **Windows Server 2016 Datacenter** på bladet **Beräkna** och klicka sedan på knappen **Skapa**.

4. Fyll i formuläret **Grundinställningar** för den virtuella datorn. Användarnamnet och lösenordet som anges här används för att logga in på den virtuella datorn. Skapa en ny för **Resursgrupp**. En resursgrupp är en logisk behållare där Azure-resurser skapas och hanteras gemensamt. När du är klar klickar du på **OK**.

    ![Ange grundläggande information om de virtuella datorerna på portalens blad](./media/virtual-machine-quick-start/create-windows-vm-portal-basic-blade.png)  

5. Välj en storlek för den virtuella datorn och klicka på **Välj**. 

6. På inställningsbladet väljer du **Ja** under **Use managed disks** (Använd hanterade diskar), låter standardinställningarna vara kvar för resten och klickar på **OK**.

7. På sammanfattningssidan klickar du på **Ok** för att starta distributionen av den virtuella datorn.

8. Klicka på den virtuella datorn om du vill övervaka distributionsstatus. Den virtuella datorn finns på instrumentpanelen för Azure Portal eller genom att välja **Virtuella datorer** på den vänstra menyn. När den virtuella datorn har skapats ändras statusen från till **Distribuerar** till **Körs**.

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

När distributionen har slutförts kan du skapa en fjärrskrivbordsanslutning till den virtuella datorn.

1. Klicka på knappen **Anslut** på bladet för den virtuella datorn. En protokollfil för fjärrskrivbord (.rdp-fil) skapas och hämtas.

    ![Portal 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

2. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om du uppmanas till detta klickar du på **Anslut**. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) från Mac App Store.

3. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn och klicka sedan på **Ok**.

4. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

## <a name="delete-virtual-machine"></a>Ta bort en virtuell dator

Ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra det väljer du resursgruppen på bladet för den virtuella datorn och klickar på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

[Självstudier om att installera en roll och konfigurera brandvägg](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Utforska exempel på distribution av virtuella datorer med CLI](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
