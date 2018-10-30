---
title: Självstudie – Dirigera trafik till viktade slutpunkter med hjälp av Azure Traffic Manager | Microsoft Docs
description: Den här artikel beskriver hur du dirigerar trafik till viktade slutpunkter med Traffic Manager.
services: traffic-manager
author: KumudD
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: kumud
ms.openlocfilehash: 171a9cfe5b5b63efde31c37cd49ef548f1e4ebcc
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649443"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-using-traffic-manager"></a>Självstudie: Styr trafikroutning med viktade slutpunkter med hjälp av Traffic Manager 

Den här självstudie beskriver hur du använder Traffic Manager till att styra routningen av användartrafik mellan slutpunkter med hjälp av routningsmetoden Viktat. I den här routningsmetoden tilldelar du viktar till varje slutpunkt i Traffic Manager-profilkonfigurationen utifrån den vikt som tilldelas till varje slutpunkt. Vikten är ett heltal mellan 1 och 1 000. Ju högre viktningsvärde som tilldelas till en slutpunkt desto högre prioritet har det.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa två virtuella datorer som kör en grundläggande webbplats i IIS
> * Skapa två virtuella testdatorer för att visa hur Traffic Manager fungerar i praktiken
> * Konfigurera DNS-namn för de virtuella datorer som kör IIS
> * Skapa en Traffic Manager-profil
> * Lägg till VM-slutpunkter i Traffic Manager-profilen
> * Se hur Traffic Manager fungerar i praktiken

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter
Om du vill se hur Traffic Manager fungerar i praktiken behöver du använda följande i den här självstudien:
- två instanser av grundläggande webbplatser i olika Azure-regioner – **USA, östra** och **Europa, västra**.
- två virtuella testdatorer för att testa Traffic Manager – en virtuell dator i **USA, östra** och den andra virtuella datorn i **Europa, västra**. De virtuella testdatorerna används till att illustrera hur Traffic Manager dirigerar användartrafik som har högre vikt tilldelad till sin slutpunkt.

### <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

### <a name="create-websites"></a>Skapa webbplatser

I det här avsnittet skapar du två webbplatsinstanser som tillhandahåller två tjänstslutpunkter för Traffic Manager-profilen i två Azure-regioner. Skapa de två webbplatserna genom att göra följande:
1. Skapa två virtuella datorer för att köra en grundläggande webbplats – en i **USA, östra** och den andra i **Europa, västra**.
2. Installera IIS-servern på de båda virtuella datorerna och uppdatera standardwebbsidan som beskriver namnet på den virtuella datorn som en användare är ansluten när webbplatsen besöks.

#### <a name="create-vms-for-running-websites"></a>Skapa virtuella datorer för att köra webbplatser
I det här avsnittet skapar du de två virtuella datorerna *myIISVMEastUS* och *myIISVMWEurope* i Azure-regionerna **USA, östra** och **Europa, västra**.

1. Längst upp till vänster i Azure Portal väljer du **Skapa en resurs** > **Beräkning** > **Windows Server 2016 VM**.
2. Ange eller välj följande information för **Grundinställningar**, acceptera standardinställningarna för återstående inställningar och välj sedan **Skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myIISVMEastUS|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |Lösenord| Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Resursgrupp| Välj **Ny** och skriv sedan *myResourceGroupTM1*.|
    |Plats| Välj **USA, östra**.|
    |||
4. Välj en VM-storlek i **Välj en storlek**.
5. Välj följande värden för **Inställningar** och sedan **OK**:
    
    |Inställning|Värde|
    |---|---|
    |Virtuellt nätverk| Välj **Virtuellt nätverk** i **Skapa virtuellt nätverk**. För **Namn** anger du *myVNet1* och för undernätet anger du  *mySubnet*.|
    |Nätverkssäkerhetsgrupp|Välj **Grundläggande** och i listrutan **Välj offentliga inkommande portar** väljer du **HTTP** och **RDP** |
    |Startdiagnostik|Välj **Inaktiverad**.|
    |||
6. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen.

7. Utför steg 1–6 igen, med följande ändringar:

    |Inställning|Värde|
    |---|---|
    |Resursgrupp | Välj **Ny** och skriv sedan *myResourceGroupTM2*|
    |Plats|Västra Europa|
    |Namn på virtuell dator | myIISVMWEurope|
    |Virtuellt nätverk | Välj **Virtuellt nätverk** i **Skapa virtuellt nätverk**. För **Namn** anger du *myVNet2* och för undernätet anger du  *mySubnet*.|
    |||
8. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

![Skapa en virtuell dator](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Installera IIS och anpassa standardwebbsidan

I det här avsnittet ska du installera IIS-servern på två virtuella datorer – *myIISVMEastUS*  & *myIISVMWEurope*, och sedan uppdatera standardwebbsidan. Den anpassade webbsidan visar namnet på den virtuella datorn som du ansluter till när du besöker webbplatsen från en webbläsare.

1. Klicka på **Alla resurser** i den vänstra menyn och från resurslistan klickar du sedan på *myIISVMEastUS* som finns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** klickar du på **Anslut**. I **Connect to virtual machine** (Anslut till virtuell dator) väljer du **Ladda ned RDP-fil**. 
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn. 
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.
6. Navigera till **Windows Administrationsverktyg**>**Serverhanteraren** på serverdatorn.
7. Starta Windows PowerShell på VM1 och kör följande kommandon för att installera IIS-servern och uppdatera standard-HTML-filen.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Installera IIS och anpassa webbsida](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. Stäng RDP-anslutningen med *myIISVMEastUS*.
9. Upprepa steg 1–8 med att skapa en RDP-anslutning med den virtuella datorn *myIISVMWEurope* i resursgruppen *myResourceGroupTM2* för att installera IIS och anpassa dess standardwebbsida.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurera DNS-namnen för de virtuella datorer som kör IIS

Traffic Manager dirigerar användartrafik baserat på tjänstslutpunkternas DNS-namn. I det här avsnittet konfigurerar du DNS-namnen för IIS-servrarna – *myIISVMEastUS* och *myIISVMWEurope*.

1. Klicka på **Alla resurser** i den vänstra menyn och från resurslistan väljer du sedan *myIISVMEastUS* som finns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** under **DNS-namn** väljer du **Konfigurera**.
3. På sidan **Konfiguration**, under DNS-namnetiketten, lägger du till ett unikt namn och sedan väljer du **Spara**.
4. Upprepa steg 1–3, för den virtuella datorn med namnet *myIISVMWEurope* som finns i resursgruppen *myResourceGroupTM1*.

### <a name="create-a-test-vm"></a>Skapa en virtuell testdator

I det här avsnittet skapar du en virtuell dator – *mVMEastUS*. Du använder den här virtuella datorn för att testa hur Traffic Manager dirigerar trafik till webbplatsslutpunkten som högre viktningsvärde.

1. Längst upp till vänster i Azure Portal väljer du **Skapa en resurs** > **Beräkning** > **Windows Server 2016 VM**.
2. Ange eller välj följande information för **Grundinställningar**, acceptera standardinställningarna för återstående inställningar och välj sedan **Skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVMEastUS|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |Lösenord| Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Resursgrupp| Välj **Befintlig** och sedan *myResourceGroupTM1*.|
    |||

4. Välj en VM-storlek i **Välj en storlek**.
5. Välj följande värden för **Inställningar** och sedan **OK**:
    |Inställning|Värde|
    |---|---|
    |Virtuellt nätverk| Välj **Virtuellt nätverk** i **Skapa virtuellt nätverk**. För **Namn** anger du *myVNet3* och för undernätet anger du *mySubnet*.|
    |Nätverkssäkerhetsgrupp|Välj **Grundläggande** och i listrutan **Välj offentliga inkommande portar** väljer du **HTTP** och **RDP** |
    |Startdiagnostik|Välj **Inaktiverad**.|
    |||

6. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen.
8. Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med de återstående stegen förrän den virtuella datorn har skapats.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil
Skapa en Traffic Manager-profil baserat på routningsmetoden **Viktat**.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Traffic Manager-profil** > **Skapa**.
2. I **Skapa Traffic Manager-profil** anger eller väljer du följande information, accepterar standardinställningarna för återstående inställningar och väljer sedan **Skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                   | Namnet måste var unikt inom trafficmanager.net-zonen och generera DNS-namnet, trafficmanager.net, som används för att öppna din Traffic Manager-profil.                                   |
    | Routningsmetod          | Välj routningsmetoden **Viktat**.                                       |
    | Prenumeration            | Välj din prenumeration.                          |
    | Resursgrupp          | Välj **Befintlig** och välj *myResourceGroupTM1*. |
    |        |   |

    ![Skapa en Traffic Manager-profil](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Lägg till de två virtuella datorer som kör IIS-servrarna – *myIISVMEastUS*  & *myIISVMWEurope* för att dirigera användartrafik till dem.

1. I portalens sökfält söker du efter det Traffic Manager-profilnamn som du skapade i föregående avsnitt och väljer profilen i det resultat som visas.
2. I **Traffic Manager-profilen** går du till avsnittet **Inställningar** och klickar på **Slutpunkter** och klickar sedan på **Lägg till**.
3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Typ                    | Azure-slutpunkt                                   |
    | Namn           | myEastUSEndpoint                                        |
    | Målresurstyp           | Offentlig IP-adress                          |
    | Målresurs          | **Välj en offentlig IP-adress** för att visa en lista över resurser med offentliga IP-adresser i samma prenumeration. I **Resurs** väljer du den offentliga IP-adressen med namnet *myIISVMEastUS-ip*. Det här är den offentliga IP-adressen för virtuella datorer med IIS i USA, östra.|
    |  Vikt      | Ange **100**.        |
    |        |           |

4. Upprepa steg 2 och 3 för att lägga till en ytterligare slutpunkt med namnet *myWestEuropeEndpoint* för den offentliga IP-adressen *myIISVMWEurope-ip* som är associerad med den virtuella datorn för IIS-servern med namnet *myIISVMWEurope* och i fältet **Vikt** anger du **25**. 
5.  När båda slutpunkterna har lagts till visas de i **Traffic Manager-profilen** tillsammans med sin övervakningsstatus, som är **Online**.

## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil
Om du vill se hur Traffic Manager fungerar i praktiken gör du följande:
1. Fastställ DNS-namnet för din Traffic Manager-profil.
2. Visa Traffic Manager i aktion.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Fastställ DNS-namnet på Traffic Manager-profilen
I den här självstudien använder du för enkelhetens skull DNS-namnet för Traffic Manager-profilen för att besöka webbplatserna. 

Så här kan du fastställa DNS-namnet i Traffic Manager-profilen:

1.  I portalens sökfält söker du efter det **Traffic Manager-profil**namn som du skapade i föregående avsnitt. I det resultat som visas klickar du på Traffic Manager-profilen.
1. Klicka på **Översikt**.
2. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil. I Produktionsdistributioner konfigurerar du ett anpassat domännamn så att den pekar till Traffic Manager-domännamnet, med hjälp av en DNS CNAME-post.

   ![DNS-namn för Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken
I det här avsnittet får du se Traffic Manager i arbete. 

1. Välj **Alla resurser** på menyn till vänster och klicka i listan över resurser på *myVMEastUS* som vinns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** klickar du på **Anslut**. I **Connect to virtual machine** (Anslut till virtuell dator) väljer du **Ladda ned RDP-fil**. 
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn. 
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen. 
6. I en webbläsare på den virtuella datorn *myVMEastUS* anger du DNS-namnet i Traffic Manager-profilen för at visa din webbplats. Du dirigeras till en webbplats som finns på IIS-servern *myIISVMEastUS* eftersom den har tilldelats den högre vikten **100** jämfört med IIS-servern *myIISVMWEurope* (tilldelad det lägre slutpunktsvärdet **25**).

   ![Testa Traffic Manager-profil](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)
   
## <a name="delete-the-traffic-manager-profile"></a>Ta bort Traffic Manager-profilen
När de inte längre behövs tar du bort de resursgrupper du har skapat i den här kursen. Om du vill göra det markerar du resursgruppen (**ResourceGroupTM1** eller **ResourceGroupTM2**) och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Dirigera trafik till specifika slutpunkter baserat på användarens geografiska plats](traffic-manager-configure-geographic-routing-method.md)


