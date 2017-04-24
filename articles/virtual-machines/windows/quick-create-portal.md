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
ms.date: 04/13/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 8a86cf64dcd65e74285a1073f7494eba0708ddcd
ms.lasthandoff: 04/15/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Skapa en virtuell Windows-dator med Azure Portal

Det går att skapa virtuella datorer via Azure Portal. Den här metoden ger ett webbläsarbaserat användargränssnitt för att skapa och konfigurera virtuella datorer och alla relaterade resurser. Den här snabbstarten beskriver hur man skapar en virtuell dator med Azure Portal. När distributionen är klar kan vi ansluta till servern och installera IIS.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

2. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.

3. Välj **Beräkna** på bladet **Nytt**, välj **Windows Server 2016 Datacenter** på bladet **Beräkna** och klicka sedan på knappen **Skapa**.

4. Fyll i formuläret **Grundinställningar** för den virtuella datorn. Användarnamnet och lösenordet som anges här används för att logga in på den virtuella datorn. Skapa en ny för **Resursgrupp**. En resursgrupp är en logisk behållare där Azure-resurser skapas och hanteras gemensamt. När du är klar klickar du på **OK**.

    ![Ange grundläggande information om de virtuella datorerna på portalens blad](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)  

5. Välj en storlek för den virtuella datorn. Om du vill se fler storlekar väljer du **Visa alla** eller så ändrar du filtret för **disktyper som stöds**. 

    ![Skärmbild som visar storlekar på virtuella datorer](./media/quick-create-portal/create-windows-vm-portal-sizes.png)  

6. På inställningsbladet väljer du **Ja** under **Use managed disks** (Använd hanterade diskar), låter standardinställningarna vara kvar för resten och klickar på **OK**.

7. På sammanfattningssidan klickar du på **Ok** för att starta distributionen av den virtuella datorn.

8. Klicka på den virtuella datorn om du vill övervaka distributionsstatus. Den virtuella datorn finns på instrumentpanelen för Azure Portal eller genom att välja **Virtuella datorer** på den vänstra menyn. När den virtuella datorn har skapats ändras statusen från till **Distribuerar** till **Körs**.

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik 

Du måste öppna port 80 för webbtrafik om du vill tillåta trafik för IIS. Det här steget vägleder dig genom processen med att skapa en regel för en nätverkssäkerhetsgrupp (NSG) för att tillåta inkommande anslutningar på port 80.

1. På bladet för den virtuella datorn i avsnittet **Essentials** klickar du på namnet på **Resursgruppen**.
2. Gå till bladet för resursgruppen och klicka på **Nätverkssäkerhetsgrupp** i listan över resurser. NSG-namnet bör vara namnet på den virtuella datorn med ”- nsg” tillagt i slutet.
3. Klicka på rubriken **Ingående säkerhetsregel** så öppnas listan med regler för inkommande trafik. Du bör se en regel för RDP i listan redan.
4. Klicka på **+ Lägg till** så öppnas bladet **Lägg till ingående säkerhetsregel**.
5. Skriv **IIS** i fältet **Namn**. Kontrollera att 80 har angetts för **Portintervall** och att **Tillåt** har angetts för **Åtgärd**. Klicka på **OK**.


## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

När distributionen har slutförts kan du skapa en fjärrskrivbordsanslutning till den virtuella datorn.

1. Klicka på knappen **Anslut** på bladet för den virtuella datorn. En protokollfil för fjärrskrivbord (.rdp-fil) skapas och hämtas.

    ![Portal 9](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png) 

2. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om du uppmanas till detta klickar du på **Anslut**. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) från Mac App Store.

3. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn och klicka sedan på **Ok**.

4. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen.


## <a name="install-iis-using-powershell"></a>Installera IIS med PowerShell

Öppna en PowerShell-kommandotolk på den virtuella datorn och kör följande kommando för att installera IIS och aktivera regeln för att tillåta webbtrafik i den lokala brandväggen:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

Du kan använda en webbläsare som du själv väljer för att visa standardvälkomstsidan för IIS när IIS är installerat och port 80 nu är öppen på den virtuella datorn från Internet. Hämta den **offentliga IP-adressen** från bladet för den virtuella datorn och använd den för att besöka standardwebbsidan. 

![Standardwebbplatsen i IIS](./media/quick-create-powershell/default-iis-website.png) 

## <a name="delete-virtual-machine"></a>Ta bort en virtuell dator

Ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra det väljer du resursgruppen på bladet för den virtuella datorn och klickar på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

[Självstudier om att installera en roll och konfigurera brandvägg](hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Utforska exempel på distribution av virtuella datorer med CLI](cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

