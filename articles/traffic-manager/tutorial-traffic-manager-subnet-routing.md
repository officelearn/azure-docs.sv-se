---
title: Tutorial - Configure subnet traffic routing with Azure Traffic Manager
description: This tutorial explains how to configure Traffic Manager to route traffic from user subnets to specific endpoints.
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: allensu
ms.openlocfilehash: 00bc453ebb0e467f48bd886fc7c6b6c422693864
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420258"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Tutorial: Direct traffic to specific endpoints based on user subnet using Traffic Manager

Den här artikeln beskriver hur du konfigurerar trafikroutningsmetoden för undernät. Med trafikroutningsmetoden för **undernät** kan du mappa en uppsättning IP-adressintervall till specifika slutpunkter. När en begäran tas emot av Traffic Manager undersöks käll-IP-adressen för begäran och slutpunkten som är kopplad till den returneras.

I de här självstudierna används undernätsroutning, vilket innebär att trafik dirigeras antingen till en intern webbplats eller en produktionswebbplats beroende på IP-adressen för användarens fråga.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa två virtuella datorer som kör en grundläggande webbplats i IIS
> * Skapa två virtuella testdatorer för att visa hur Traffic Manager fungerar i praktiken
> * Konfigurera DNS-namn för de virtuella datorer som kör IIS
> * Skapa en Traffic Manager-profil för trafikroutning som baseras på användarens undernät
> * Lägg till VM-slutpunkter i Traffic Manager-profilen
> * Se hur Traffic Manager fungerar i praktiken

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill se hur Traffic Manager fungerar i praktiken behöver du använda följande i den här självstudien:

- två grundläggande webbplatser som körs i olika Azure-regioner – **USA, östra** (fungerar som intern webbplats) och **Europa, västra** (fungerar som produktionswebbplats).
- två virtuella testdatorer för att testa Traffic Manager – en virtuell dator i **USA, östra** och den andra virtuella datorn i **Europa, västra**.

De virtuella testdatorerna används för att illustrera hur Traffic Manager dirigerar trafik till den interna webbplatsen eller produktionswebbplatsen baserat på undernätet som användarfrågan kommer från.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

### <a name="create-websites"></a>Skapa webbplatser

I det här avsnittet skapar du två webbplatsinstanser som tillhandahåller två tjänstslutpunkter för Traffic Manager-profilen i två Azure-regioner. Att skapa två webbplatser omfattar följande steg:

1. Skapa två virtuella datorer för att köra en grundläggande webbplats – en i **USA, östra** och den andra i **Europa, västra**.
2. Installera IIS-servern på de båda virtuella datorerna och uppdatera standardwebbsidan som beskriver namnet på den virtuella datorn som en användare är ansluten när webbplatsen besöks.

#### <a name="create-vms-for-running-websites"></a>Skapa virtuella datorer för att köra webbplatser

In this section, you create two VMs *myIISVMEastUS* and *myIISVMWestEurope* in the **East US** and **West Europe** Azure regions.

1. On the upper, left corner of the Azure portal, select **Create a resource** > **Compute** > **Windows Server 2019 Datacenter**.
2. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:

   - **Subscription** > **Resource Group**: Select **Create new** and then type **myResourceGroupTM1**.
   - **Instance Details** > **Virtual machine name**: Type *myIISVMEastUS*.
   - **Instance Details** > **Region**:  Select **East US**.
   - **Administrator Account** > **Username**:  Enter a user name of your choosing.
   - **Administrator Account** > **Password**:  Enter a password of your choosing. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Inbound Port Rules** > **Public inbound ports**: Select **Allow selected ports**.
   - **Inbound Port Rules** > **Select inbound ports**: Select **RDP** and **HTTP** in the pull down box.

3. Select the **Management** tab, or select **Next: Disks**, then **Next: Networking**, then **Next: Management**. Under **Övervakning** anger du **Startdiagnostik** till **Av**.
4. Välj **Granska + skapa**.
5. Review the settings, and then click **Create**.  
6. Follow the steps to create a second VM named *myIISVMWestEurope*, with a **Resource group** name of *myResourceGroupTM2*, a **location** of *West Europe*, and all the other settings the same as *myIISVMEastUS*.
7. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Installera IIS och anpassa standardwebbsidan

In this section, you install the IIS server on the two VMs - *myIISVMEastUS* & *myIISVMWestEurope*, and then update the default website page. Den anpassade webbsidan visar namnet på den virtuella datorn som du ansluter till när du besöker webbplatsen från en webbläsare.

1. Klicka på **Alla resurser** i den vänstra menyn och från resurslistan klickar du sedan på *myIISVMEastUS* som finns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** klickar du på **Anslut**. I **Connect to virtual machine** (Anslut till virtuell dator) väljer du **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.
6. Navigera till **Windows Administrationsverktyg**>**Serverhanteraren** på serverdatorn.
7. Launch Windows PowerShell on VM *myIISVMEastUS*, and using the following commands to install IIS server and update the default htm file.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Close the RDP connection with *myIISVMEastUS* VM.
9. Repeat steps 1-6 with by creating an RDP connection with the VM *myIISVMWestEurope* within the *myResourceGroupTM2* resource group to install IIS and customize its default web page.
10. Launch Windows PowerShell on *myIISVMWestEurope* VM, and using the following commands to install IIS server and update the default htm file.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurera DNS-namnen för de virtuella datorer som kör IIS

Traffic Manager dirigerar användartrafik baserat på tjänstslutpunkternas DNS-namn. In this section, you configure the DNS names for the IIS servers - *myIISVMEastUS* and *myIISVMWestEurope*.

1. Klicka på **Alla resurser** i den vänstra menyn och från resurslistan väljer du sedan *myIISVMEastUS* som finns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** under **DNS-namn** väljer du **Konfigurera**.
3. På sidan **Konfiguration**, under DNS-namnetiketten, lägger du till ett unikt namn och sedan väljer du **Spara**.
4. Repeat steps 1-3, for the VM named *myIISVMWestEurope* that is located in the *myResourceGroupTM2* resource group.

### <a name="create-test-vms"></a>Skapa virtuella testdatorer

In this section, you create a VM (*myVMEastUS* and *myVMWestEurope*) in each Azure region (**East US** and **West Europe**). You will use these VMs to test how Traffic Manager routes user traffic based on the subnet of the user's query.

1. On the upper, left corner of the Azure portal, select **Create a resource** > **Compute** > **Windows Server 2019 Datacenter**.
2. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:

   - **Subscription** > **Resource Group**: Select **myResourceGroupTM1**.
   - **Instance Details** > **Virtual machine name**: Type *myVMEastUS*.
   - **Instance Details** > **Region**:  Select **East US**.
   - **Administrator Account** > **Username**:  Enter a user name of your choosing.
   - **Administrator Account** > **Password**:  Enter a password of your choosing. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Inbound Port Rules** > **Public inbound ports**: Select **Allow selected ports**.
   - **Inbound Port Rules** > **Select inbound ports**: Select **RDP** in the pull down box.

3. Select the **Management** tab, or select **Next: Disks**, then **Next: Networking**, then **Next: Management**. Under **Övervakning** anger du **Startdiagnostik** till **Av**.
4. Välj **Granska + skapa**.
5. Review the settings, and then click **Create**.  
6. Follow the steps to create a second VM named *myVMWestEurope*, with a **Resource group** name of *myResourceGroupTM2*, a **location** of *West Europe*, and all the other settings the same as *myVMEastUS*.
7. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager-profil som gör det möjligt att returnera specifika slutpunkter baserat på käll-IP-adressen för begäran.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Traffic Manager-profil** > **Skapa**.
2. I **Skapa Traffic Manager-profil** anger eller väljer du följande information, accepterar standardinställningarna för återstående inställningar och väljer sedan **Skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                   | Namnet måste var unikt inom trafficmanager.net-zonen och generera DNS-namnet, trafficmanager.net, som används för att öppna din Traffic Manager-profil.                                   |
    | Routningsmetod          | Välj routningsmetoden för **undernät**.                                       |
    | Prenumeration            | Välj din prenumeration.                          |
    | Resursgrupp          | Välj **Befintlig** och ange *myResourceGroupTM1*. |
    | |                              |
    |

    ![Skapa en Traffic Manager-profil](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Add the two VMs running the IIS servers - *myIISVMEastUS* & *myIISVMWestEurope* to route user traffic based on the subnet of the user's query.

1. I portalens sökfält söker du efter det Traffic Manager-profilnamn som du skapade i föregående avsnitt och väljer profilen i det resultat som visas.
2. I **Traffic Manager-profilen** går du till avsnittet **Inställningar** och klickar på **Slutpunkter** och klickar sedan på **Lägg till**.
3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Typ                    | Azure-slutpunkt                                   |
    | Namn           | myInternalWebSiteEndpoint                                        |
    | Målresurstyp           | Offentlig IP-adress                          |
    | Målresurs          | **Välj en offentlig IP-adress** för att visa en lista över resurser med offentliga IP-adresser i samma prenumeration. I **Resurs** väljer du den offentliga IP-adressen med namnet *myIISVMEastUS-ip*. Det här är den offentliga IP-adressen för virtuella datorer med IIS i USA, östra.|
    |  Inställningar för undernätsroutning    |   Add the IP address of *myVMEastUS* test VM. Any user query originating from this VM will be directed to the *myInternalWebSiteEndpoint*.    |

4. Repeat steps 2 and 3 to add another endpoint named *myProdWebsiteEndpoint* for the public IP address *myIISVMWestEurope-ip* that is associated with the IIS server VM named *myIISVMWestEurope*. For **Subnet routing settings**, add the IP address of the test VM - *myVMWestEurope*. Alla användarfrågor från den här virtuella testdatorn dirigeras till slutpunkten – *myProdWebsiteEndpoint*.
5. När båda slutpunkterna har lagts till visas de i **Traffic Manager-profilen** tillsammans med sin övervakningsstatus, som är **Online**.

## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil

I det här avsnittet testar du hur Traffic Manager dirigerar användartrafik från ett visst undernät till en specifik slutpunkt. Om du vill se hur Traffic Manager fungerar i praktiken gör du följande:

1. Fastställ DNS-namnet för din Traffic Manager-profil.
2. Se hur Traffic Manager fungerar i praktiken:
    - From the test VM (*myVMEastUS*) that is located in the **East US** region, in a web browser, browse to the DNS name of your Traffic Manager profile.
    - From the test VM (*myVMWestEurope*) that is located in the **West Europe** region, in a web browser, browse to the DNS name of your Traffic Manager profile.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Fastställ DNS-namnet på Traffic Manager-profilen

I den här självstudien använder du för enkelhetens skull DNS-namnet för Traffic Manager-profilen för att besöka webbplatserna.

Så här kan du fastställa DNS-namnet i Traffic Manager-profilen:

1. I portalens sökfält söker du efter det **Traffic Manager-profil**namn som du skapade i föregående avsnitt. I det resultat som visas klickar du på Traffic Manager-profilen.
2. Klicka på **Översikt**.
3. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil. I Produktionsdistributioner konfigurerar du ett anpassat domännamn så att den pekar till Traffic Manager-domännamnet, med hjälp av en DNS CNAME-post.

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken

I det här avsnittet får du se Traffic Manager i arbete.

1. Välj **Alla resurser** på menyn till vänster och klicka i listan över resurser på *myVMEastUS* som vinns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** klickar du på **Anslut**. I **Connect to virtual machine** (Anslut till virtuell dator) väljer du **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.
6. I en webbläsare på den virtuella datorn *myVMEastUS* anger du DNS-namnet i Traffic Manager-profilen för at visa din webbplats. Since the VM *myVMEastUS* IP address is associated with the endpoint *myInternalWebsiteEndpoint*, the web browser launches the Test website server - *myIISVMEastUS*.

7. Next, connect to the VM *myVMWestEurope* located in **West Europe** using steps 1-5 and browse to the Traffic Manager profile domain name from this VM. Since the VM *myVMWestEurope* IP address is associated with the endpoint *myProductionWebsiteEndpoint*, the web browser launches the Test website server - *myIISVMWestEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Ta bort Traffic Manager-profilen

När resursgrupperna inte längre behövs kan du ta bort dem (**ResourceGroupTM1** och **ResourceGroupTM2**). Om du vill göra det markerar du resursgruppen (**ResourceGroupTM1** eller **ResourceGroupTM2**) och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [routningsmetoden för viktad trafik](traffic-manager-configure-weighted-routing-method.md).
- Lär dig mer om [prioritetsroutningsmetoden](traffic-manager-configure-priority-routing-method.md).
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
