---
title: 'Självstudiekurs: Är värd för flera webbplatser med Azure-portalen'
titleSuffix: Azure Application Gateway
description: I den här självstudien får du lära dig hur du skapar en programgateway som är värd för flera webbplatser med Azure-portalen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: victorh
ms.openlocfilehash: ca6be666a9b77532b4f1c61f6e3391c239e82c91
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74075157"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Självstudiekurs: Skapa och konfigurera en programgateway som värd för flera webbplatser med Azure-portalen

Du kan använda Azure-portalen för att [konfigurera värdskap för flera webbplatser](multiple-site-overview.md) när du skapar en [programgateway](overview.md). I den här självstudien definierar du serverdadresspooler med hjälp av virtuella datorer. Du konfigurerar sedan lyssnare och regler baserat på de domäner du äger för att kontrollera att webbtrafiken anländer till rätt servrar i poolerna. I den här självstudien förutsätts att du äger flera domäner. Vi använder *www.contoso.com* och *www.fabrikam.com* som exempel.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en programgateway
> * Skapa virtuella datorer för serverdservrar
> * Skapa servergruppspooler med serverdaservrar
> * Skapa lyssnare för serverdelen
> * Skapa routningsregler
> * Skapa en CNAME-post i domänen

![Exempel på routning mellan flera webbplatser](./media/create-multiple-sites-portal/scenario.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på[https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Skapa en programgateway

1. Välj **Skapa en resurs** på den vänstra menyn på Azure-portalen. Fönstret **Nytt** visas.

2. Välj **Nätverk** och välj sedan **Application Gateway** i listan **Aktuell.**

### <a name="basics-tab"></a>Fliken Grundläggande

1. På fliken **Grunderna** anger du dessa värden för följande inställningar för programgateway:

   - **Resursgrupp**: Välj **myResourceGroupAG** för resursgruppen. Om den inte finns väljer du **Skapa ny** för att skapa den.
   - **Namn på programgateway**: Ange *myAppGateway* för namnet på programgatewayen.

     ![Skapa ny programgateway: Grunderna](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du kan antingen skapa ett nytt virtuellt nätverk eller använda ett befintligt nätverk. I det här exemplet ska du skapa ett nytt virtuellt nätverk samtidigt som du skapar programgatewayen. Application Gateway-instanser skapas i separata undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

    Under **Konfigurera virtuellt nätverk**väljer du Skapa **nytt** för att skapa ett nytt virtuellt nätverk . I fönstret **Skapa virtuellt nätverk** som öppnas anger du följande värden för att skapa det virtuella nätverket och två undernät:

    - **Namn**: Ange *myVNet* för namnet på det virtuella nätverket.

    - **Undernätsnamn** (undernät för Programgateway): **Undernätsrutnätet** visar ett undernät med namnet *Standard*. Ändra namnet på det här undernätet till *myAGSubnet*.<br>Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.

    - **Undernätsnamn** (serverdelsundernät): Ange *myBackendSubnet* i kolumnen **Undernätsnamn** på den andra raden i **rutnätet undernät.**

    - **Adressintervall** (serverdelsserverns undernät): I den andra raden i **nätrutnätet** anger du ett adressintervall som inte överlappar adressintervallet *för myAGSubnet*. Om adressintervallet för *myAGSubnet* till exempel är 10.0.0.0/24 anger du *10.0.1.0/24* för adressintervallet för *myBackendSubnet*.

    Välj **OK** om du vill stänga fönstret **Skapa virtuellt nätverk** och spara inställningarna för det virtuella nätverket.

     ![Skapa ny programgateway: virtuellt nätverk](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. På fliken **Grunderna godkänner** du standardvärdena för de andra inställningarna och väljer sedan **Nästa: Frontends**.

### <a name="frontends-tab"></a>Fliken Frontends

1. Kontrollera att **IP-adresstypen Frontend** är offentlig **på**fliken **Frontend.** <br>Du kan konfigurera Frontend IP att vara offentlig eller privat enligt ditt användningsfall. I det här exemplet väljer du en IP-adress med offentliga frontend.
   > [!NOTE]
   > För Application Gateway v2 SKU kan du bara välja **IP-konfiguration** för offentliga frontend. Privat klientdel IP-konfiguration är för närvarande inte aktiverad för den här v2 SKU.

2. Välj **Skapa ny** för den offentliga **IP-adressen** och ange *myAGPublicIPAddress* för det offentliga IP-adressnamnet och välj sedan **OK**. 

     ![Skapa ny programgateway: frontends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Välj **Nästa: Backends**.

### <a name="backends-tab"></a>Fliken Backends

Serverdapoolen används för att dirigera begäranden till serverdaservrar som betjänar begäran. Serverdelspooler kan vara nätverkskort, skalningsuppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domännamn (FQDN) och backend-ändar med flera innehavare som Azure App Service. I det här exemplet ska du skapa en tom backend-pool med programgatewayen och sedan lägga till backend-mål i backend-poolen.

1. På fliken **Backends** väljer du **+Lägg till en backend-pool**.

2. I fönstret **Lägg till en backend-pool** som öppnas anger du följande värden för att skapa en tom backend-pool:

    - **Namn**: Ange *contosoPool* för namnet på backend-poolen.
    - **Lägg till backend pool utan mål**: Välj **Ja** om du vill skapa en backend-pool utan mål. Du ska lägga till backend-mål när du har skapat programgatewayen.

3. I fönstret **Lägg till en serverdpool** väljer du **Lägg till** för att spara serverdapoolkonfigurationen och återgå till fliken **Serveringsfrämda.**
4. Lägg nu till en annan backend pool som kallas *fabrikamPool*.

     ![Skapa ny programgateway: backends](./media/create-multiple-sites-portal/backend-pools.png)

4. På fliken **Serveringar** väljer du **Nästa: Konfiguration**.

### <a name="configuration-tab"></a>Fliken Konfiguration

På fliken **Konfiguration** ansluter du klientdels- och serverdelspoolerna som du har skapat med hjälp av en routningsregel.

1. Välj **Lägg till en regel** i kolumnen **Routningsregler.**

2. I fönstret **Lägg till en routningsregel** som öppnas anger du *contosoRule* för **regelnamnet**.

3. En routningsregel kräver en lyssnare. På fliken **Lyssnare** i fönstret **Lägg till en routningsregel** anger du följande värden för lyssnaren:

    - **Lyssnarens namn**: Ange *contosoListener* för lyssnarens namn.
    - **Frontend IP:** Välj **Offentlig** om du vill välja den offentliga IP-adress som du skapade för klientdelen.

   Under **Ytterligare inställningar:**
   - **Lyssnartyp:** Flera webbplatser
   - **Värdnamn**: **www.contoso.com**

   Acceptera standardvärdena för de andra inställningarna på fliken **Lyssnare** och välj sedan fliken **Serveringsmål** för att konfigurera resten av routningsregeln.

   ![Skapa ny programgateway: lyssnare](./media/create-multiple-sites-portal/routing-rule.png)

4. På fliken **Backend-mål** väljer du **contosoPool** för **backend-målet**.

5. För **HTTP-inställningen**väljer du **Skapa ny** om du vill skapa en ny HTTP-inställning. HTTP-inställningen avgör hur routningsregeln fungerar. I fönstret **Lägg till en HTTP-inställning** som öppnas anger du *contosoHTTPSetting* för **HTTP-inställningsnamnet**. Acceptera standardvärdena för de andra inställningarna i **fönstret Lägg till en HTTP-inställning** och välj sedan **Lägg** till för att återgå till fönstret Lägg till **en routningsregel.** 

6. I fönstret **Lägg till en routningsregel** väljer du **Lägg till** för att spara routningsregeln och gå tillbaka till fliken **Konfiguration.**
7. Välj **Lägg till en regel** och lägg till en liknande regel, lyssnare, backend-mål och HTTP-inställning för Fabrikam.

     ![Skapa ny programgateway: routningsregel](./media/create-multiple-sites-portal/fabrikamRule.png)

7. Välj **Nästa: Taggar** och sedan **Nästa: Granska + skapa**.

### <a name="review--create-tab"></a>Fliken Granska + skapa

Granska inställningarna på fliken **Granska + skapa** och välj sedan **Skapa** för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen. Det kan ta flera minuter för Azure att skapa programgatewayen.

Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-backend-targets"></a>Lägga till bakåtsträvningsmål

I det här exemplet ska du använda virtuella datorer som målbacksad. Du kan antingen använda befintliga virtuella datorer eller skapa nya. Du skapar två virtuella datorer som Azure använder som serverningsservrar för programgatewayen.

Om du vill lägga till backend-mål gör du:

1. Skapa två nya virtuella datorer, *contosoVM* och *fabrikamVM*, som ska användas som servergruppsservrar.
2. Installera IIS på de virtuella datorerna för att kontrollera att programgatewayen har skapats.
3. Lägg till serverdservrarna i serverdapoolerna.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** på Azure-portalen. Fönstret **Nytt** visas.
2. Välj **Beräkna** och välj sedan **Windows Server 2016 Datacenter** i listan **Populär.** Sidan **Skapa en virtuell dator** visas.<br>Application Gateway kan dirigera trafik till alla typer av virtuell dator som används i serverdapoolen. I det här exemplet använder du ett Windows Server 2016-datacenter.
3. Ange dessa värden på fliken **Grundläggande inställningar** för följande inställningar för virtuella datorer:

    - **Resursgrupp**: Välj **myResourceGroupAG** för resursgruppsnamnet.
    - **Namn på virtuell dator**: Ange *contosoVM* för namnet på den virtuella datorn.
    - **Användarnamn**: Ange *azureuser* för administratörens användarnamn.
    - **Lösenord**: Ange *Azure123456!* som administratörslösenord.
4. Acceptera de andra standardinställningarna och välj sedan **Nästa: Diskar**.  
5. Acceptera **standardvärdena för diskfliken** och välj sedan **Nästa: Nätverk**.
6. På fliken **Nätverk** kontrollerar du att **myVNet** har valts för **Virtuellt nätverk** och att **Undernät** är inställt på **myBackendSubnet**. Acceptera de andra standardinställningarna och välj sedan **Nästa: Hantering**.<br>Application Gateway kan kommunicera med instanser utanför det virtuella nätverket som den finns i, men du måste se till att det finns IP-anslutning.
7. På fliken **Hantering** anger du **Startdiagnostik** till **Av**. Acceptera de övriga standardinställningarna och välj sedan **Granska + skapa**.
8. Gå igenom inställningarna på fliken **Granska + skapa** och åtgärda eventuella verifieringsfel och välj sedan **Skapa**.
9. Vänta på att skapandet av den virtuella datorn är klart innan du fortsätter.

### <a name="install-iis-for-testing"></a>Installera IIS för testning

I det här exemplet installerar du IIS på de virtuella datorerna bara för att verifiera att Azure har skapat programgatewayen.

1. Öppna [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Det gör du genom att först välja **Cloud Shell** i det övre navigeringsfältet på Azure-portalen och sedan välja **PowerShell** i listrutan. 

    ![Installera anpassat tillägg](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Kör följande kommando för att installera IIS på den virtuella datorn: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Skapa en andra virtuell dator och installera IIS med hjälp av de steg som du tidigare har slutfört. Använd *fabrikamVM* för namnet på den virtuella datorn och för **VMName-inställningen** för **cmdlet set-azvmextension.**

### <a name="add-backend-servers-to-backend-pools"></a>Lägga till servergruppsservrar i serverdapooler

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **Serverdelspooler** på den vänstra menyn.

3. Välj **contosoPool**.

4. Välj **Virtuell dator** i listrutan under **Mål**.

5. Under **virtuella dator-** och **nätverksgränssnitt**väljer du den virtuella **contosoVM-datorn** och det är associerat nätverksgränssnitt i listrutorna.

    ![Lägga till serverdelsservrar](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Välj **Spara**.
7. Upprepa om du vill lägga till *fabrikamVM* och gränssnittet i *fabrikamPool*.

Vänta tills distributionen har slutförts innan du fortsätter till nästa steg.

## <a name="create-a-www-a-record-in-your-domains"></a>Skapa en www A-post i dina domäner

När programgatewayen har skapats med dess offentliga IP-adress kan du hämta IP-adressen och använda den för att skapa en A-post i dina domäner. 

1. Klicka på **Alla resurser**och sedan på **myAGPublicIPAddress**.

    ![Registrera DNS-adress för programgateway](./media/create-multiple-sites-portal/public-ip.png)

2. Kopiera IP-adressen och använd den som värde för en ny *www* A-post i dina domäner.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Ange domännamnet i adressfältet i webbläsaren. Till exempel `http://www.contoso.com`.

    ![Testa contoso-webbplatsen i programgatewayen](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Om du ändrar adressen till din andra domän bör du se något som liknar följande exempel:

    ![Testa fabrikam-webbplatsen i programgatewayen](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen kan du ta bort resursgruppen. När du tar bort resursgruppen tar du också bort programgatewayen och alla dess relaterade resurser.

Så här tar du bort resursgruppen:

1. Välj **Resursgrupper** på den vänstra menyn på Azure-portalen.
2. På sidan **Resursgrupper** söker du efter och väljer **myResourceGroupAG**.
3. Välj **Ta bort resursgrupp** på sidan **Resursgrupp**.
4. Ange *myResourceGroupAG* för **Skriv resursgruppens namn** och välj sedan **Ta bort**

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om vad du kan göra med Azure Application Gateway](application-gateway-introduction.md)