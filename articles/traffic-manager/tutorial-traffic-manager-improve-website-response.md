---
title: Självstudie – dirigera trafik till att förbättra webbplatsen svaret med hjälp av Azure Traffic Manager | Microsoft Docs
description: Den här självstudiekursen beskrivs hur du skapar en Traffic Manager-profil om du vill skapa en mycket dynamisk webbplats.
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: jeconnoc
editor: ''
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: kumud
ms.openlocfilehash: 89518d30b862e18fb7c989c95144ffa7f1c294fc
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40025135"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Självstudie: Förbättra webbplatsen svaret med hjälp av Traffic Manager 

Den här självstudien beskrivs hur du använder Traffic Manager för att skapa en mycket dynamisk webbplats genom att dirigera trafiken till webbplatsen med kortast svarstid. Datacenter med kortast svarstid är vanligtvis det som ligger närmast geografiska avstånd.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa två virtuella datorer som kör en grundläggande webbplats i IIS
> * Skapa två test virtuella datorer att visa Traffic Manager fungerar i praktiken
> * Konfigurera DNS-namn för de virtuella datorer som kör IIS
> * Skapa en Traffic Manager-profil för förbättrad webbplatsens prestanda
> * Lägga till VM-slutpunkter i Traffic Manager-profilen
> * Visa Traffic Manager fungerar i praktiken

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
Om du vill se Traffic Manager fungerar i praktiken krävs den här självstudien att du distribuerar följande:
- två instanser av grundläggande webbplatser som körs i olika Azure-regioner - **USA, östra** och **Västeuropa**.
- två virtuella testdatorer för att testa Traffic Manager – en virtuell dator i **USA, östra** och den andra virtuella datorn i **Västeuropa**. Testet virtuella datorer används för att illustrera hur Traffic Manager dirigerar trafiken till den webbplats som körs i samma region som det ger kortast svarstid.

### <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

### <a name="create-websites"></a>Skapa webbplatser

I det här avsnittet skapar du två webbplatsinstanser som tillhandahåller två Tjänsteslutpunkter för Traffic Manager-profilen i två Azure-regioner. Skapa två webbplatser omfattar följande steg:
1. Skapa två virtuella datorer för att köra en grundläggande webbplats – en i **USA, östra**, och det andra i **Västeuropa**.
2. Installera IIS-server på varje virtuell dator och uppdatera webbplatsen standardsidan som beskriver virtuella datorns namn som en användare är ansluten till när du besöker webbplatsen.

#### <a name="create-vms-for-running-websites"></a>Skapa virtuella datorer för att köra webbplatser
I det här avsnittet skapar du två virtuella datorer *myIISVMEastUS* och *myIISVMWEurope* i den **USA, östra** och **Västeuropa** Azure-regioner.

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
    |Virtuellt nätverk| Välj **virtuellt nätverk**i **skapa virtuellt nätverk**, för **namn**, ange *myVNet1*, undernät, ange * mySubnet*.|
    |Nätverkssäkerhetsgrupp|Välj **grundläggande**, och i **Välj offentliga inkommande portar** listrutan, väljer **HTTP** och **RDP** |
    |Startdiagnostik|Välj **inaktiverad**.|
    |||
6. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen.

7. Utför steg 1–6 igen, med följande ändringar:

    |Inställning|Värde|
    |---|---|
    |Resursgrupp | Välj **New**, och skriv sedan *myResourceGroupTM2*|
    |Plats|Västra Europa|
    |Namn på virtuell dator | myIISVMWEurope|
    |Virtuellt nätverk | Välj **virtuellt nätverk**i **skapa virtuellt nätverk**, för **namn**, ange *myVNet2*, undernät, ange * mySubnet*.|
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
8. Stäng RDP-anslutning med *myIISVMEastUS*.
9. Upprepa steg 1-8 med genom att skapa en RDP-anslutning med den virtuella datorn *myIISVMWEurope* inom den *myResourceGroupTM2* resursgrupp för att installera IIS och anpassa dess standardwebbsidan.

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
    |Resursgrupp| Välj **befintliga** och välj sedan *myResourceGroupTM1*.|
    |||

4. Välj en VM-storlek i **Välj en storlek**.
5. Välj följande värden för **Inställningar** och sedan **OK**:
    |Inställning|Värde|
    |---|---|
    |Virtuellt nätverk| Välj **virtuellt nätverk**i **skapa virtuellt nätverk**, för **namn**, ange *myVNet3*, undernät, ange * mySubnet*.|
    |Nätverkssäkerhetsgrupp|Välj **grundläggande**, och i **Välj offentliga inkommande portar** listrutan, väljer **HTTP** och **RDP** |
    |Startdiagnostik|Välj **inaktiverad**.|
    |||

6. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen.

7. Utför steg 1 till 5 igen, med följande ändringar:

    |Inställning|Värde|
    |---|---|
    |Namn på virtuell dator | *myVMWEurope*|
    |Resursgrupp | Välj **befintliga**, och skriv sedan *myResourceGroupTM2*|
    |Virtuellt nätverk | Välj **virtuellt nätverk**i **skapa virtuellt nätverk**, för **namn**, ange *myVNet4*, undernät, ange * mySubnet*.|
    |||

8. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil
Skapa en Traffic Manager-profil som dirigerar användartrafik som genom att skicka dem till slutpunkten med kortast svarstid.

1. Längst upp till vänster på skärmen Välj **skapa en resurs** > **nätverk** > **Traffic Manager-profil**  >  **Skapa**.
2. I den **skapa Traffic Manager-profil**, ange eller Välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **skapa**:
    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                   | Det här namnet måste vara unikt inom trafficmanager.net-zonen och resultat i DNS-namn, trafficmanager.net som används för att få åtkomst till Traffic Manager-profilen.                                   |
    | Routningsmetod          | Välj den **prioritet** routningsmetod.                                       |
    | Prenumeration            | Välj din prenumeration.                          |
    | Resursgrupp          | Välj **Skapa nytt** och ange *myResourceGroupTM1*. |
    | Plats                | Välj **USA, östra**.  Inställningen refererar till platsen för resursgruppen och har ingen inverkan på Traffic Manager-profilen som distribueras globalt.                              |
    |
  
    ![Skapa en Traffic Manager-profil](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Lägg till Traffic Manager-slutpunkter

Lägg till de två virtuella datorer som kör IIS servrar – *myIISVMEastUS*  & *myIISVMWEurope* att dirigera trafik till närmaste slutpunkten för användaren.

1. I portalens sökfältet, Sök efter namnet på Traffic Manager-profilen som du skapade i föregående avsnitt och Välj profilen i resultaten som visas.
2. I **Traffic Manager-profil**i den **inställningar** klickar du på **slutpunkter**, och klicka sedan på **Lägg till**.
3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Typ                    | Azure-slutpunkt                                   |
    | Namn           | myEastUSEndpoint                                        |
    | Målresurstyp           | Offentlig IP-adress                          |
    | Målresurs          | **Välj en offentlig IP-adress** att visa en lista över resurser med offentliga IP-adresser i samma prenumeration. I **Resource**, Välj offentlig IP-adress med namnet *myIISVMEastUS ip*. Det här är den offentliga IP-adressen för IIS-server-dator i USA, östra.|
    |        |           |

4. Upprepa steg 2 och 3 för att lägga till en annan slutpunkt med namnet *myWestEuropeEndpoint* för den offentliga IP-adressen *myIISVMWEurope ip* som är associerad med den IIS-servern virtuell dator med namnet *myIISVMWEurope *.
5.  När tillägg av båda slutpunkterna är klar, visas de i **Traffic Manager-profil** tillsammans med dess övervakningsstatus som **Online**.

    ![Lägg till en Traffic Manager-slutpunkt](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)
  

## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil
I det här avsnittet ska testa du hur Traffic Manager dirigerar trafiken till närmaste virtuella datorer som kör webbplatsen att ge lägsta svarstid. Om du vill visa Traffic Manager fungerar i praktiken, gör du följande:
1. Bestämma DNS-namnet för din Traffic Manager-profil.
2. Visa Traffic Manager fungerar på följande sätt:
    - Från den Virtuella testdatorn (*myVMEastUS*) som finns i den **USA, östra** region i en webbläsare, bläddrar du till DNS-namnet på Traffic Manager-profilen.
    - Från den Virtuella testdatorn (*myVMEastUS*) som finns i den **Västeuropa** region i en webbläsare, bläddrar du till DNS-namnet på Traffic Manager-profilen.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Bestämma DNS-namnet på Traffic Manager-profil
I den här självstudien använder för enkelhetens skull du DNS-namnet på Traffic Manager-profilen att gå till webbplatserna. 

Du kan fastställa DNS-namnet på Traffic Manager-profilen på följande sätt:

1.  I portalens sökfältet söker du efter den **Traffic Manager-profil** namn som du skapade i föregående avsnitt. Klicka på traffic manager-profilen i resultaten som visas.
1. Klicka på **översikt**.
2. Den **Traffic Manager-profil** visar DNS-namnet på din nyligen skapade Traffic Manager-profil. I Produktionsdistribution måste konfigurera du ett anpassad domännamn så att den pekar till Traffic Manager-domännamn, med hjälp av en DNS CNAME-post.

   ![Traffic Manager DNS-namn](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Visa Traffic Manager fungerar i praktiken
Du kan se Traffic Manager är åtgärd i det här avsnittet. 

1. Välj **alla resurser** på den vänstra menyn och klicka sedan i resurslistan på *myVMEastUS* som finns i den *myResourceGroupTM1* resursgrupp.
2. På den **översikt** klickar du på **Connect**, och sedan i **Anslut till den virtuella datorn**väljer **ladda ned RDP-filen**. 
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn. 
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen. 
1. I en webbläsare på den virtuella datorn *myVMEastUS*, DNS-namn för din Traffic Manager-profil för att visa din webbplats. Eftersom den virtuella datorn finns i **USA, östra**, du dirigeras till den närmaste webbplats på närmaste IIS-servern *myIISVMEastUS* som finns i **USA, östra**.

   ![Testa Traffic Manager-profil](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Anslut sedan till den virtuella datorn *myVMWestEurope* i **Västeuropa** med steg 1-5 och bläddra till domännamnet för Traffic Manager-profil från den här virtuella datorn.  Eftersom den virtuella datorn finns i **Västeuropa**, du är nu dirigeras till webbplats på närmaste IIS-servern *myIISVMWEurope* som finns i **Västeuropa**. 

   ![Testa Traffic Manager-profil](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)
   
## <a name="delete-the-traffic-manager-profile"></a>Ta bort Traffic Manager-profilen
När den inte längre behövs kan du ta bort resursgrupper (**ResourceGroupTM1** och **ResourceGroupTM2**). Om du vill göra det väljer du resursgruppen (**ResourceGroupTM1** eller **ResourceGroupTM2**), och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera trafik till en uppsättning slutpunkter](traffic-manager-configure-weighted-routing-method.md)


