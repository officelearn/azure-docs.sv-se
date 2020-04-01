---
title: Självstudiekurs - Förbättra webbplatsens svar med Azure Traffic Manager
description: I den här självstudieartikeln beskrivs hur du skapar en Traffic Manager-profil för att skapa en mycket responsiv webbplats.
services: traffic-manager
author: rohinkoul
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: rohink
ms.openlocfilehash: f7778b60a5e84f5d8900b8e37bfa655a7915d403
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77136390"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Självstudiekurs: Förbättra webbplatsens svar med Traffic Manager

I den här självstudien beskrivs hur du använder Traffic Manager för att skapa en mycket responsiv webbplats genom att dirigera användartrafik till webbplatsen med den lägsta svarstiden. Vanligtvis är det datacenter som har den lägsta svarstiden det som är närmast geografiskt avstånd.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa två virtuella datorer som kör en grundläggande webbplats i IIS
> * Skapa två virtuella testdatorer för att visa hur Traffic Manager fungerar i praktiken
> * Konfigurera DNS-namn för de virtuella datorer som kör IIS
> * Skapa en Traffic Manager-profil för bättre webbplatsprestanda
> * Lägg till VM-slutpunkter i Traffic Manager-profilen
> * Se hur Traffic Manager fungerar i praktiken

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill se hur Traffic Manager fungerar i praktiken behöver du använda följande i den här självstudien:

- Två instanser av grundläggande webbplatser som körs i olika Azure-regioner - **Östra USA** och **Västeuropa**.
- Två test virtuella datorer för att testa Traffic Manager - en virtuell dator i **östra USA** och den andra virtuella datorn i **Västeuropa**. Test-virtuella datorer används för att illustrera hur Traffic Manager dirigerar användartrafik till den webbplats som körs i samma region eftersom den ger den lägsta svarstiden.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

### <a name="create-websites"></a>Skapa webbplatser

I det här avsnittet skapar du två webbplatsinstanser som tillhandahåller två tjänstslutpunkter för Traffic Manager-profilen i två Azure-regioner. Att skapa två webbplatser omfattar följande steg:

1. Skapa två virtuella datorer för att köra en grundläggande webbplats – en i **USA, östra** och den andra i **Europa, västra**.
2. Installera IIS-servern på de båda virtuella datorerna och uppdatera standardwebbsidan som beskriver namnet på den virtuella datorn som en användare är ansluten när webbplatsen besöks.

#### <a name="create-vms-for-running-websites"></a>Skapa virtuella datorer för att köra webbplatser

I det här avsnittet skapar du två virtuella datorer *myIISVMEastUS* och *myIISVMWestEurope* i **azure-regionerna östra USA** och **Västeuropa.**

1. I det övre vänstra hörnet av Azure-portalen väljer du Skapa ett > **resursberäkningscenter** > **för Windows Server 2019**. **Create a resource**
2. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:

   - **Subscription** > **Prenumerationsresursgrupp:** Välj **Skapa ny** och skriv sedan **myResourceGroupTM1**.
   - **Instansinformation** > **Virtuell datornamn:** Skriv *myIISVMEastUS*.
   - **Instance Details** > Region för**instansinformation**: Välj **östra USA**.
   - **Användarnamn för administratörskonto:** > **Username**Ange ett användarnamn som du väljer.
   - **Lösenord för administratörskonto:** > **Password**Ange ett lösenord som du väljer. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Inkommande portregler** > **Offentliga inkommande portar**: Välj Tillåt valda **portar**.
   - **Regler för inkommande port** > **Välj inkommande portar**: Välj **RDP** och **HTTP** i rulllådan.

3. Välj fliken **Hantering** eller välj **Nästa: Diskar**och sedan **Nästa: Nätverk**, sedan **Nästa: Hantering**. Under **Övervakning** anger du **Startdiagnostik** till **Av**.
4. Välj **Granska + skapa**.
5. Granska inställningarna och klicka sedan på **Skapa**.  
6. Följ stegen för att skapa en andra virtuell dator med namnet *myIISVMWestEurope*, med namnet **på resursgruppnamnet** *myResourceGroupTM2*, en **plats** i *Västeuropa*och alla andra inställningar på samma sätt som *myIISVMEastUS*.
7. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

   ![Skapa en virtuell dator](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Installera IIS och anpassa standardwebbsidan

I det här avsnittet installerar du IIS-servern på de två virtuella datorerna *myIISVMEastUS* och *myIISVMWestEurope*och uppdaterar sedan standardwebbplatssidan. Den anpassade webbsidan visar namnet på den virtuella datorn som du ansluter till när du besöker webbplatsen från en webbläsare.

1. Klicka på **Alla resurser** i den vänstra menyn och från resurslistan klickar du sedan på *myIISVMEastUS* som finns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** klickar du på **Anslut**. I **Connect to virtual machine** (Anslut till virtuell dator) väljer du **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.
6. På serverskrivbordet navigerar du till **Windows Administrative Tools**>**Server Manager**.
7. Starta Windows PowerShell på VM1 och använd följande kommandon för att installera IIS-servern och uppdatera standard-HTML-filen.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Installera IIS och anpassa webbsida](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. Stäng RDP-anslutningen med *myIISVMEastUS*.
9. Upprepa steg 1-8 med genom att skapa en RDP-anslutning med VM *myIISVMWestEurope* inom resursgruppen *myResourceGroupTM2* för att installera IIS och anpassa standardwebbsidan.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurera DNS-namnen för de virtuella datorer som kör IIS

Traffic Manager dirigerar användartrafik baserat på tjänstslutpunkternas DNS-namn. I det här avsnittet konfigurerar du DNS-namnen för IIS-servrarna - *myIISVMEastUS* och *myIISVMWestEurope*.

1. Klicka på **Alla resurser** i den vänstra menyn och från resurslistan väljer du sedan *myIISVMEastUS* som finns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** under **DNS-namn** väljer du **Konfigurera**.
3. På sidan **Konfiguration**, under DNS-namnetiketten, lägger du till ett unikt namn och sedan väljer du **Spara**.
4. Upprepa steg 1-3 för den virtuella datorn som heter *myIISVMWestEurope* som finns i resursgruppen *myResourceGroupTM2.*

### <a name="create-test-vms"></a>Skapa virtuella testdatorer

I det här avsnittet skapar du en virtuell dator *(myVMEastUS* och *myVMWestEurope)* i varje Azure-region **(Östra USA** och **Västeuropa).** Du använder dessa virtuella datorer för att testa hur Traffic Manager dirigerar trafik till den närmaste IIS-servern när du surfar till webbplatsen.

1. I det övre vänstra hörnet av Azure-portalen väljer du Skapa ett > **resursberäkningscenter** > **för Windows Server 2019**. **Create a resource**
2. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:

   - **Resursgrupp** > **för**prenumeration: Välj **myResourceGroupTM1**.
   - **Instansinformation** > **Virtuell datornamn:** Skriv *myVMEastUS*.
   - **Instance Details** > Region för**instansinformation**: Välj **östra USA**.
   - **Användarnamn för administratörskonto:** > **Username**Ange ett användarnamn som du väljer.
   - **Lösenord för administratörskonto:** > **Password**Ange ett lösenord som du väljer. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Inkommande portregler** > **Offentliga inkommande portar**: Välj Tillåt valda **portar**.
   - **Inkommande portregler** > **Välj inkommande portar:** Välj **RDP** i rulllådan.

3. Välj fliken **Hantering** eller välj **Nästa: Diskar**och sedan **Nästa: Nätverk**, sedan **Nästa: Hantering**. Under **Övervakning** anger du **Startdiagnostik** till **Av**.
4. Välj **Granska + skapa**.
5. Granska inställningarna och klicka sedan på **Skapa**.  
6. Följ stegen för att skapa en andra virtuell dator med namnet *myVMWestEurope*, med ett **resursgruppnamn** för *myResourceGroupTM2*, en **plats** i *Västeuropa*och alla andra inställningar på samma sätt som *myVMEastUS*.
7. Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager-profil som dirigerar användartrafik genom att skicka dem till slutpunkten med lägsta svarstid.

1. Välj **Skapa en resurs** > **Traffic** > **Manager-profil** > **Skapa**på den övre vänstra sidan av skärmen .
2. I **Skapa Traffic Manager-profil** anger eller väljer du följande information, accepterar standardinställningarna för återstående inställningar och väljer sedan **Skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                   | Namnet måste var unikt inom trafficmanager.net-zonen och generera DNS-namnet, trafficmanager.net, som används för att öppna din Traffic Manager-profil.                                   |
    | Routningsmetod          | Välj **metoden För** prestandaroutning.                                       |
    | Prenumeration            | Välj din prenumeration.                          |
    | Resursgrupp          | Välj resursgruppen *myResourceGroupTM1*. |
    | Location                | Välj **östra USA**. Inställningen refererar till platsen för resursgruppen och har ingen inverkan på Traffic Manager-profilen som distribueras globalt.                              |
    |

    ![Skapa en Traffic Manager-profil](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Lägg till de två virtuella datorer som kör IIS-servrarna - *myIISVMEastUS* & *myIISVMWestEurope* för att dirigera användartrafik till närmaste slutpunkt till användaren.

1. I portalens sökfält söker du efter det Traffic Manager-profilnamn som du skapade i föregående avsnitt och väljer profilen i det resultat som visas.
2. I **Traffic Manager-profilen** går du till avsnittet **Inställningar** och klickar på **Slutpunkter** och klickar sedan på **Lägg till**.
3. Ange, eller välj, följande information, acceptera standardinställningarna för de återstående inställningarna och välj sedan **OK:**

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Typ                    | Azure-slutpunkt                                   |
    | Namn           | myEastUSEndpoint                                        |
    | Målresurstyp           | Offentlig IP-adress                          |
    | Målresurs          | **Välj en offentlig IP-adress** för att visa en lista över resurser med offentliga IP-adresser i samma prenumeration. I **Resurs** väljer du den offentliga IP-adressen med namnet *myIISVMEastUS-ip*. Det här är den offentliga IP-adressen för virtuella datorer med IIS i USA, östra.|
    |        |           |

4. Upprepa steg 2 och 3 för att lägga till en annan slutpunkt med namnet *myWestEuropeEndpoint* för den offentliga IP-adressen *myIISVMWestEurope-ip* som är associerad med IIS-servern VM som heter *myIISVMWestEurope*.
5. När båda slutpunkterna har lagts till visas de i **Traffic Manager-profilen** tillsammans med sin övervakningsstatus, som är **Online**.

    ![Lägga till en Traffic Manager-slutpunkt](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil

I det här avsnittet testar du hur Traffic Manager dirigerar användartrafik till närmaste virtuella datorer som kör webbplatsen för att ge minsta svarstid. Om du vill se hur Traffic Manager fungerar i praktiken gör du följande:

1. Fastställ DNS-namnet för din Traffic Manager-profil.
2. Se hur Traffic Manager fungerar i praktiken:
    - Från test-VM *(myVMEastUS*) som finns i regionen **östra USA** bläddrar du i en webbläsare till DNS-namnet på din Traffic Manager-profil.
    - Från test-VM *(myVMWestEurope*) som finns i regionen **Västeuropa,** i en webbläsare, bläddra till DNS-namnet på din Traffic Manager-profil.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Fastställ DNS-namnet på Traffic Manager-profilen

I den här självstudien använder du för enkelhetens skull DNS-namnet för Traffic Manager-profilen för att besöka webbplatserna.

Så här kan du fastställa DNS-namnet i Traffic Manager-profilen:

1. I portalens sökfält söker du efter det **Traffic Manager-profil**namn som du skapade i föregående avsnitt. I det resultat som visas klickar du på Traffic Manager-profilen.
2. Klicka på **Översikt**.
3. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil. I Produktionsdistributioner konfigurerar du ett anpassat domännamn så att den pekar till Traffic Manager-domännamnet, med hjälp av en DNS CNAME-post.

   ![DNS-namn för Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken

I det här avsnittet får du se Traffic Manager i arbete.

1. Välj **Alla resurser** på menyn till vänster och klicka i listan över resurser på *myVMEastUS* som vinns i resursgruppen *myResourceGroupTM1*.
2. På sidan **Översikt** klickar du på **Anslut**. I **Connect to virtual machine** (Anslut till virtuell dator) väljer du **Ladda ned RDP-fil**.
3. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.
4. Välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.
1. I en webbläsare på den virtuella datorn *myVMEastUS* anger du DNS-namnet i Traffic Manager-profilen för at visa din webbplats. Eftersom den virtuella datorn ligger i **östra USA**dirigeras du till närmaste webbplats som finns på närmaste IIS-server *myIISVMEastUS* som finns i **östra USA.**

   ![Testa Traffic Manager-profil](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Anslut sedan till VM *myVMWestEurope* som finns i **Västeuropa** med steg 1-5 och bläddra till Traffic Manager-profilens domännamn från den här virtuella datorn. Eftersom den virtuella datorn ligger i **Västeuropa,** är du nu dirigeras till webbplatsen värd på närmaste IIS-servern *myIISVMWestEurope* som ligger i **Västeuropa**.

   ![Testa Traffic Manager-profil](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="delete-the-traffic-manager-profile"></a>Ta bort Traffic Manager-profilen

När resursgrupperna inte längre behövs kan du ta bort dem (**ResourceGroupTM1** och **ResourceGroupTM2**). Om du vill göra det markerar du resursgruppen (**ResourceGroupTM1** eller **ResourceGroupTM2**) och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera trafik till en uppsättning slutpunkter](traffic-manager-configure-weighted-routing-method.md)
