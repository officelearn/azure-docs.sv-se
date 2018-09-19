---
title: Konfigurera undernät trafikroutningsmetod med Azure Traffic Manager | Microsoft Docs
description: Den här artikeln förklarar hur du konfigurerar Traffic Manager kan dirigera trafik från specifika undernät.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jpconnock
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: kumud
ms.openlocfilehash: 6e5e6008741306d322ebd07bcbf144133ca4e632
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46131282"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Dirigera trafik till specifika slutpunkter baserat på användare undernät med Traffic Manager

Den här artikeln beskriver hur du konfigurerar trafikdirigeringsmetoden undernät. Den **undernät** trafikdirigeringsmetoden kan du mappa en uppsättning IP-adressintervall till specifika slutpunkter och när en begäran tas emot av Traffic Manager, den söker igenom käll-IP för begäran och returnerar den slutpunkt som är kopplade till den. 

I scenariot som beskrivs i den här artikeln med hjälp av Routning undernät, beroende på IP-adressen för användarens fråga, dirigeras antingen trafik till en intern webbplats eller en produktionswebbplats.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
Om du vill se Traffic Manager fungerar i praktiken krävs den här självstudien att du distribuerar följande:
- två grundläggande webbplatser som körs i olika Azure-regioner - **USA, östra** (som fungerar som intern webbplats) och **Västeuropa** (som fungerar som produktionswebbplats).
- två virtuella testdatorer för att testa Traffic Manager – en virtuell dator i **USA, östra** och den andra virtuella datorn i **Västeuropa**. 

Testet virtuella datorer används för att illustrera hur Traffic Manager dirigerar trafiken till den interna webbplatsen eller produktionswebbplats baserat på undernät från som användarfrågan kommer ifrån.

### <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

### <a name="create-websites"></a>Skapa webbplatser

I det här avsnittet skapar du två webbplatsinstanser som tillhandahåller två Tjänsteslutpunkter för Traffic Manager-profilen i två Azure-regioner. Skapa två webbplatser omfattar följande steg:
1. Skapa två virtuella datorer för att köra en grundläggande webbplats – en i **USA, östra**, och det andra i **Västeuropa**.
2. Installera IIS-server på varje virtuell dator och uppdatera webbplatsen standardsidan som beskriver virtuella datorns namn som en användare är ansluten till när du besöker webbplatsen.

#### <a name="create-vms-for-running-websites"></a>Skapa virtuella datorer för att köra webbplatser
I det här avsnittet skapar du två virtuella datorer *myEndpointVMEastUS* och *myEndpointVMWEurope* i den **USA, östra** och **Västeuropa** Azure regioner.

1. I övre vänstra hörnet på Azure portal, Välj **skapa en resurs** > **Compute** > **Windows Server 2016 VM**.
2. Ange eller välj följande information för **Grundinställningar**, acceptera standardinställningarna för återstående inställningar och välj sedan **Skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myIISVMEastUS|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |Lösenord| Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Resursgrupp| Välj **New** och skriv sedan *myResourceGroupTM1*.|
    |Plats| Välj **USA, östra**.|
    |||
4. Välj en VM-storlek i **Välj en storlek**.
5. Välj följande värden för **Inställningar** och sedan **OK**:
    
    |Inställning|Värde|
    |---|---|
    |Virtuellt nätverk| Välj **virtuellt nätverk**i **skapa virtuellt nätverk**, för **namn**, ange *myVNet1*, undernät, ange  *mySubnet*.|
    |Nätverkssäkerhetsgrupp|Välj **grundläggande**, och i **Välj offentliga inkommande portar** listrutan, väljer **HTTP** och **RDP** |
    |Startdiagnostik|Välj **inaktiverad**.|
    |||
6. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen.

7. Utför steg 1–6 igen, med följande ändringar:

    |Inställning|Värde|
    |---|---|
    |Resursgrupp | Välj **Ny** och skriv sedan *myResourceGroupTM2*|
    |Plats|Västra Europa|
    |Namn på virtuell dator | myIISVMWEurope|
    |Virtuellt nätverk | Välj **virtuellt nätverk**i **skapa virtuellt nätverk**, för **namn**, ange *myVNet2*, undernät, ange  *mySubnet*.|
    |||
8. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

   ![Skapa en virtuell dator](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Installera IIS och anpassa standardwebbsidan

I det här avsnittet ska du installera IIS-servern på två virtuella datorer – *myIISVMEastUS*  & *myIISVMWEurope*, och sedan uppdaterar webbplatsen standardsidan. Den anpassade webbplatsen visar namnet på den virtuella datorn som du ansluter till när du besöker webbplatsen från en webbläsare.

1. Välj **alla resurser** på den vänstra menyn och klicka sedan i resurslistan på *myIISVMEastUS* som finns i den *myResourceGroupTM1* resursgrupp.
2. På den **översikt** klickar du på **Connect**, och sedan i **Anslut till den virtuella datorn**väljer **ladda ned RDP-filen**. 
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn. 
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.
6. Navigera till **Windows Administrationsverktyg**>**Serverhanteraren** på serverdatorn.
7. Starta Windows PowerShell på *myIISVMEastUS* och använder följande kommandon för att installera IIS-servern och uppdatera standard htm-fil.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my test website server - " + $env:computername)
    ```
8. Stäng RDP-anslutning med *myIISVMEastUS*.
9. Upprepa steg 1 – 6 med genom att skapa en RDP-anslutning med den virtuella datorn *myIISVMWEurope* inom den *myResourceGroupTM2* resursgrupp för att installera IIS och anpassa dess standardwebbsidan.
10. Starta Windows PowerShell på *myIISVMWEurope* och använder följande kommandon för att installera IIS-servern och uppdatera standard htm-fil.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my production website server - " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurera DNS-namn för de virtuella datorer som kör IIS

Traffic Manager dirigerar trafik för användare baserat på DNS-namnet på Tjänsteslutpunkter. I det här avsnittet ska du konfigurera DNS-namn för IIS-servrar – *myIISVMEastUS* och *myIISVMWEurope*.

1. Klicka på **alla resurser** på den vänstra menyn och sedan i resurslistan, väljer du *myIISVMEastUS* som finns i den *myResourceGroupTM1* resursgrupp.
2. På den **översikt** sidan under **DNS-namnet**väljer **konfigurera**.
3. På den **Configuration** kan under DNS-namnsetikett, lägga till ett unikt namn och välj sedan **spara**.
4. Upprepa steg 1 – 3, för den virtuella datorn med namnet *myIISVMWEurope* som finns i den *myResourceGroupTM1* resursgrupp.

### <a name="create-test-vms"></a>Skapa virtuella testdatorer

I det här avsnittet skapar du en virtuell dator (*mVMEastUS* och *myVMWestEurope*) i varje Azure-region (**USA, östra** och **Västeuropa**. Du använder dessa virtuella datorer för att testa hur Traffic Manager dirigerar trafik till den närmaste IIS-servern när du bläddrar till webbplatsen.

1. I övre vänstra hörnet på Azure portal, Välj **skapa en resurs** > **Compute** > **Windows Server 2016 VM**.
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
    |Virtuellt nätverk| Välj **virtuellt nätverk**i **skapa virtuellt nätverk**, för **namn**, ange *myVNet3*, undernät, ange  *mySubnet3*.|
    |Nätverkssäkerhetsgrupp|Välj **grundläggande**, och i **Välj offentliga inkommande portar** listrutan, väljer **HTTP** och **RDP** |
    |Startdiagnostik|Välj **inaktiverad**.|
    |||

6. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen.

7. Utför steg 1 till 5 igen, med följande ändringar:

    |Inställning|Värde|
    |---|---|
    |Namn på virtuell dator | *myVMWEurope*|
    |Resursgrupp | Välj **befintliga**, och skriv sedan *myResourceGroupTM2*|
    |Virtuellt nätverk | Välj **virtuellt nätverk**i **skapa virtuellt nätverk**, för **namn**, ange *myVNet4*, undernät, ange  *mySubnet4*.|
    |||

8. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil
Skapa en Traffic Manager-profil som gör det möjligt att returnera slutpunkter baserat på käll-IP för begäran.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Traffic Manager-profil** > **Skapa**.
2. I **Skapa Traffic Manager-profil** anger eller väljer du följande information, accepterar standardinställningarna för återstående inställningar och väljer sedan **Skapa**:
    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                   | Det här namnet måste vara unikt inom trafficmanager.net-zonen och resultat i DNS-namn, trafficmanager.net som används för att få åtkomst till Traffic Manager-profilen.                                   |
    | Routningsmetod          | Välj den **undernät** routningsmetod.                                       |
    | Prenumeration            | Välj din prenumeration.                          |
    | Resursgrupp          | Välj **befintliga** och ange *myResourceGroupTM1*. |
    | |                              |
    |
  
    ![Skapa en Traffic Manager-profil](./media/traffic-manager-subnet-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Lägg till de två virtuella datorer som kör IIS servrar – *myIISVMEastUS*  & *myIISVMWEurope* att dirigera användartrafik baserat på undernätet för användarens fråga.

1. I portalens sökfält söker du efter det Traffic Manager-profilnamn som du skapade i föregående avsnitt och väljer profilen i det resultat som visas.
2. I **Traffic Manager-profilen** går du till avsnittet **Inställningar** och klickar på **Slutpunkter** och klickar sedan på **Lägg till**.
3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Typ                    | Azure-slutpunkt                                   |
    | Namn           | myTestWebSiteEndpoint                                        |
    | Målresurstyp           | Offentlig IP-adress                          |
    | Målresurs          | **Välj en offentlig IP-adress** att visa en lista över resurser med offentliga IP-adresser i samma prenumeration. I **Resource**, Välj offentlig IP-adress med namnet *myIISVMEastUS ip*. Det här är den offentliga IP-adressen för IIS-server-dator i USA, östra.|
    |  Routning undernätsinställningar    |   Lägg till IP-adressen för *myVMEastUS* testa virtuell dator. Alla användare-frågor från den här virtuella datorn dirigeras till den *myTestWebSiteEndpoint*.    |

4. Upprepa steg 2 och 3 för att lägga till en annan slutpunkt med namnet *myProductionEndpoint* för den offentliga IP-adressen *myIISVMWEurope ip* som är associerad med den IIS-servern virtuell dator med namnet *myIISVMWEurope* . För **routning undernätsinställningar**, Lägg till IP-adressen för testet VM - *myVMWestEurope*. Alla användarfråga från det här testet VM kommer att dirigeras till slutpunkten - *myProductionWebsiteEndpoint*.
5.  När båda slutpunkterna har lagts till visas de i **Traffic Manager-profilen** tillsammans med sin övervakningsstatus, som är **Online**.

    ![Lägga till en Traffic Manager-slutpunkt](./media/traffic-manager-subnet-routing-method/customize-endpoint-with-subnet-routing-eastus.png)
  
## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil
I det här avsnittet ska testa du hur Traffic Manager dirigerar trafik från ett särskilt undernät till en viss slutpunkt. Om du vill visa Traffic Manager fungerar i praktiken, gör du följande:
1. Bestämma DNS-namnet för din Traffic Manager-profil.
2. Visa Traffic Manager fungerar på följande sätt:
    - Från den Virtuella testdatorn (*myVMEastUS*) som finns i den **USA, östra** region i en webbläsare, bläddrar du till DNS-namnet på Traffic Manager-profilen.
    - Från den Virtuella testdatorn (*myVMEastUS*) som finns i den **Västeuropa** region i en webbläsare, bläddrar du till DNS-namnet på Traffic Manager-profilen.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Bestämma DNS-namnet på Traffic Manager-profil
I den här självstudien använder för enkelhetens skull du DNS-namnet på Traffic Manager-profilen att gå till webbplatserna. 

Du kan fastställa DNS-namnet på Traffic Manager-profilen på följande sätt:

1.  I portalens sökfält söker du efter det **Traffic Manager-profil**namn som du skapade i föregående avsnitt. I det resultat som visas klickar du på Traffic Manager-profilen.
1. Klicka på **Översikt**.
2. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil. I Produktionsdistribution måste konfigurera du ett anpassad domännamn så att den pekar till Traffic Manager-domännamn, med hjälp av en DNS CNAME-post.

   ![DNS-namn för Traffic Manager](./media/traffic-manager-subnet-routing-method/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken
Du kan se Traffic Manager är åtgärd i det här avsnittet. 

1. Välj **alla resurser** på den vänstra menyn och klicka sedan i resurslistan på *myVMEastUS* som finns i den *myResourceGroupTM1* resursgrupp.
2. På den **översikt** klickar du på **Connect**, och sedan i **Anslut till den virtuella datorn**väljer **ladda ned RDP-filen**. 
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn. 
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen. 
1. I en webbläsare på den virtuella datorn *myVMEastUS*, DNS-namn för din Traffic Manager-profil för att visa din webbplats. Eftersom den virtuella datorn *myVMEastUS* IP-adress som är associerad med slutpunkten *myIISVMEastUS*, webbläsaren startar Test-webbplatsservern - *myIISVMEastUS*.

   ![Testa Traffic Manager-profil](./media/traffic-manager-subnet-routing-method/test-traffic-manager.png)

2. Anslut sedan till den virtuella datorn *myVMWestEurope* i **Västeuropa** med steg 1-5 och bläddra till domännamnet för Traffic Manager-profil från den här virtuella datorn. Eftersom den virtuella datorn *myVMWestEurope* IP-adress som är associerad med slutpunkten *myIISVMEastUS*, webbläsaren startar Test-webbplatsservern - *myIISVMWEurope*. 
  
## <a name="delete-the-traffic-manager-profile"></a>Ta bort Traffic Manager-profilen
När den inte längre behövs kan du ta bort resursgrupper (**ResourceGroupTM1** och **ResourceGroupTM2**). Om du vill göra det väljer du resursgruppen (**ResourceGroupTM1** eller **ResourceGroupTM2**), och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [viktad trafikroutningsmetod](traffic-manager-configure-weighted-routing-method.md).
- Lär dig mer om [prioriterad routningsmetod](traffic-manager-configure-priority-routing-method.md).
- Lär dig mer om [geografisk routningsmetod](traffic-manager-configure-geographic-routing-method.md).


