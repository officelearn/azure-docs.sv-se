---
title: Självstudie – Skapa en Application Gateway med URL Path-baserade regler för routning – Azure Portal
description: I den här självstudien får du lära dig hur du skapar URL Path-baserade routningsregler för en Programgateway och en skalnings uppsättning för virtuella datorer med hjälp av Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: victorh
ms.openlocfilehash: 63a1faa79374e72eabfbee4ece454728c3b4cc05
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597578"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Självstudier: Skapa en Programgateway med sökvägar baserade routningsregler med hjälp av Azure Portal

Du kan använda Azure Portal för att konfigurera [URL-sökvägar baserade routningsregler](url-route-overview.md) när du skapar en [Application Gateway](overview.md). I den här självstudien skapar du backend-pooler med virtuella datorer. Du skapar sedan routningsregler som kontrollerar att webb trafiken kommer till rätt servrar i poolerna.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en programgateway
> * Skapa virtuella datorer för backend-servrar
> * Skapa backend-pooler med backend-servrar
> * Skapa en server dels lyssnare
> * Skapa en regel för väg-baserad routning

![URL-routningsexempel](./media/create-url-route-portal/scenario.png)

Om du vill kan du slutföra den här självstudien med [Azure CLI](tutorial-url-route-cli.md) eller [Azure PowerShell](tutorial-url-route-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Skapa en programgateway

1. Välj **Skapa en resurs** på den vänstra menyn på Azure-portalen. Fönstret **Nytt** visas.

2. Välj **Nätverk** och välj sedan **Programgateway** i listan **Aktuella**.

### <a name="basics-tab"></a>Fliken grunder

1. På fliken **grundläggande** anger du dessa värden för följande Programgateway-inställningar:

   - **Resursgrupp**: Välj **myResourceGroupAG** som resursgrupp. Om den inte finns väljer du **Skapa ny** för att skapa den.
   - **Namn på Application Gateway**: Ange *myAppGateway* som namn på programgatewayen.

     ![Skapa ny Application Gateway: Grundinställningar](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du kan antingen skapa ett nytt virtuellt nätverk eller använda ett befintligt. I det här exemplet ska du skapa ett nytt virtuellt nätverk på samma tidpunkt som du skapar programgatewayen. Application Gateway instanser skapas i separata undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

    Under **Konfigurera virtuellt nätverk**väljer du **Skapa nytt** för att skapa ett nytt virtuellt nätverk. I fönstret **Skapa virtuellt nätverk** som öppnas anger du följande värden för att skapa det virtuella nätverket och två undernät:

    - **Namn på**: Ange *myVnet* som namn på det virtuella nätverket.

    - **Under näts namn** (Application Gateway undernät): **Under nätets** rutnät visas ett undernät med namnet *default*. Ändra namnet på det här under nätet till *myAGSubnet*.

      Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.

    - **Under näts namn** (backend-serverns undernät): I den andra raden i **under nätets** rutnät anger du *MyBackendSubnet* i kolumnen **under nät namn** .

    - **Adress intervall** (backend-serverns undernät): I den andra raden i **under nätets** rutnät anger du ett adress intervall som inte överlappar adress intervallet för *myAGSubnet*. Om adress intervallet för *myAGSubnet* till exempel är 10.0.0.0/24, anger du *10.0.1.0/24* för adress intervallet för *myBackendSubnet*.

    Välj **OK** för att stänga fönstret **Skapa virtuellt nätverk** och spara inställningarna för det virtuella nätverket.

     ![Skapa ny Application Gateway: virtuellt nätverk](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. På fliken **grundläggande** accepterar du standardvärdena för de andra inställningarna och väljer **sedan Nästa: Frontend-klienter.**

### <a name="frontends-tab"></a>Fliken frontend

1. På fliken **klient** delar kontrollerar du att **IP-adress typen för klient delen** är **offentlig**. <br>Du kan konfigurera klient delens IP-adress så att den är offentlig eller privat per användnings fall. I det här exemplet väljer du en offentlig IP-adress för klient delen.
   > [!NOTE]
   > För Application Gateway v2 SKU: n kan du bara välja **offentlig** IP-konfiguration för klient delen. Den privata klient delens IP-konfiguration är för närvarande inte aktive rad för denna v2-SKU

2. Välj **Skapa ny** för den **offentliga IP-adressen** och ange *myAGPublicIPAddress* för den offentliga IP-adressen och välj sedan **OK**. 

     ![Skapa ny Application Gateway: klient delar](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Välj **Nästa: Server delar**.

### <a name="backends-tab"></a>Fliken Server delar

Backend-poolen används för att dirigera begär anden till backend-servrar som hanterar begäran. Backend-pooler kan vara nätverkskort, skalnings uppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domän namn (FQDN) och backend-ändar för flera klienter som Azure App Service. I det här exemplet ska du skapa en tom backend-pool med din Application Gateway och sedan lägga till Server dels mål i backend-poolen.

1. På fliken **Server** delar väljer du **+ Lägg till en backend-pool**.

2. I fönstret **Lägg till en server dels grupp** som öppnas anger du följande värden för att skapa en tom backend-pool:

    - **Namn på**: Ange *appGatewayBackendPool* som namn på backend-poolen.
    - **Lägg till backend-pool utan mål**: Välj **Ja** om du vill skapa en backend-pool utan mål. Du kommer att lägga till Server dels mål när du har skapat programgatewayen.

3. I fönstret **Lägg till en server dels grupp** väljer du **Lägg till** för att spara konfigurationen av backend-poolen och återgår till fliken back **ändar** .
4. Lägg nu till två fler backend-pooler med namnet *imagesBackendPool*och *videoBackendPool*.

     ![Skapa ny Application Gateway: Server delar](./media/create-url-route-portal/backends.png)

4. På fliken **Server** delar väljer **du nästa: Konfiguration**.

### <a name="configuration-tab"></a>Fliken konfiguration

På fliken **konfiguration** ansluter du klient dels-och backend-pooler som du skapade med hjälp av regler för routning.

1. Välj **Lägg till en regel** i kolumnen **routningsregler** .
2. I fönstret **Lägg till regel för routning** som öppnas anger du *regel 1* som **regel namn**.
3. En regel för routning kräver en lyssnare. Ange följande  värden för lyssnaren på fliken lyssnare i fönstret **Lägg till regel** för vidarebefordran:

    - **Namn på lyssnare**: Ange *DefaultListener* som namn på lyssnaren.
    - **Frontend-IP**: Välj **offentlig** för att välja den offentliga IP-adress som du skapade för klient delen.

   Acceptera standardvärdena för de andra inställningarna på fliken **lyssnare** och välj sedan fliken **backend-mål** för att konfigurera resten av regeln.
4. På fliken **backend-mål** väljer du **AppGatewayBackendPool** för **Server dels målet**.

5. För **http-inställningen**väljer du **Skapa ny** för att skapa en ny http-inställning. HTTP-inställningen avgör hur routningsregler fungerar. I fönstret **Lägg till en HTTP-inställning** som öppnas anger du *myHTTPSetting* som **namn på http-inställningen**. Acceptera standardvärdena för de andra inställningarna i fönstret **Lägg till en HTTP-inställning** och välj sedan **Lägg till** för att återgå till fönstret **Lägg till regel för routning** . 

6. I fönstret **Lägg till regel för routning** väljer du **Lägg till** för att spara regeln för Routning och återgå till fliken **konfiguration** .



1. Välj **Lägg till en regel** i kolumnen **routningsregler** .

2. I fönstret **Lägg till regel för routning** som öppnas anger du *regel 2* som **regel namn**.

3. En regel för routning kräver en lyssnare. Ange följande  värden för lyssnaren på fliken lyssnare i fönstret **Lägg till regel** för vidarebefordran:

    - **Namn på lyssnare**: Ange *myBackendListener* som namn på lyssnaren.
    - **Frontend-IP**: Välj **offentlig** för att välja den offentliga IP-adress som du skapade för klient delen.
    - **Port**: 8080

   Under **ytterligare inställningar**:
   - **Typ**av lyssnare: Basic

   Acceptera standardvärdena för de andra inställningarna på fliken **lyssnare** och välj sedan fliken **backend-mål** för att konfigurera resten av regeln.

4. På fliken **backend-mål** väljer du **AppGatewayBackendPool** för **Server dels målet**.

5. För **http-inställningen**väljer du *myHTTPSetting*. Acceptera standardvärdena för de andra inställningarna i fönstret **Lägg till en HTTP-inställning** och välj sedan **Lägg till** för att återgå till fönstret **Lägg till regel för routning** . 

1. Under **Path-baserad routning**väljer **du Lägg till flera mål för att skapa en Sök vägs baserad regel**.
2. I fönstret **Lägg till en Sök vägs regel** anger du följande värden för Sök vägs regeln:

   - **Sökväg**: */images/\**
   - **Namn på Sök vägs regel**: *Avbildningar*
   - **Http-inställning**: Välj *myHTTPSetting*
   - **Server dels mål**: *imagesBackendPool*
9. Välj **Lägg till**.
10. Lägg till en annan Sök vägs regel med namnet *video*, med en */video/\**  -sökväg och *videoBackendPool*.
11. Välj **Spara ändringar och gå tillbaka till regler för routning**.

    ![Lägg till en hanteringsregel](media/create-url-route-portal/add-routing-rule.png)

12. Välj **Lägg till**.

7. Välj **Nästa:**  Taggar**och nästa: Granska + skapa**.

### <a name="review--create-tab"></a>Granska + fliken Skapa

Granska inställningarna på fliken **Granska + skapa** och välj sedan **skapa** för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen. Det kan ta flera minuter för Azure att skapa programgatewayen.

Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.


## <a name="create-virtual-machines"></a>Skapa virtuella datorer

I det här exemplet skapar du tre virtuella datorer som ska användas som backend-servrar för programgatewayen. Du installerar även IIS på de virtuella datorerna för att kontrol lera att Application Gateway har skapats.

1. Välj **Skapa en resurs** på Azure-portalen. Fönstret **Nytt** visas.
2. Välj **Compute** och välj sedan **Windows Server 2016 Data Center** i listan **populär** . Sidan **Skapa en virtuell dator** visas.

   Application Gateway kan dirigera trafik till vilken typ av virtuell dator som helst som används i dess backend-pool. I det här exemplet använder du ett Windows Server 2016 Data Center.
1. Ange dessa värden på fliken **Grundläggande inställningar** för följande inställningar för virtuella datorer:

    - **Resursgrupp**: Välj **myResourceGroupAG** som namn på resursgruppen.
    - **Namn på virtuell dator**: Ange *myVM1* som namn på den virtuella datorn.
    - **Användarnamn**: Ange *azureuser* som administratörens användarnamn.
    - **Lösenord**: Ange *Azure123456!* som administratörslösenord.
4. Acceptera de övriga standardinställningarna och välj sedan **Nästa: Diskar**.  
5. Acceptera standardinställningarna på fliken **Diskar** och välj sedan **Nästa: Nätverk**.
6. På fliken **Nätverk** kontrollerar du att **myVNet** har valts för **Virtuellt nätverk** och att **Undernät** är inställt på **myBackendSubnet**. Acceptera de övriga standardinställningarna och välj sedan **Nästa: Hantering**.

   Application Gateway kan kommunicera med instanser utanför det virtuella nätverk som det finns i, men du måste se till att det finns en IP-anslutning.
1. På fliken **Hantering** anger du **Startdiagnostik** till **Av**. Acceptera de övriga standardinställningarna och välj sedan **Granska + skapa**.
2. Gå igenom inställningarna på fliken **Granska + skapa** och åtgärda eventuella verifieringsfel och välj sedan **Skapa**.
3. Vänta på att skapandet av den virtuella datorn är klart innan du fortsätter.

### <a name="install-iis"></a>Installera IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Öppna det interaktiva gränssnittet och kontrol lera att det är inställt på **PowerShell**.

    ![Installera anpassat tillägg](./media/create-url-route-portal/application-gateway-extension.png)

2. Kör följande kommando för att installera IIS på den virtuella datorn: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Skapa två fler virtuella datorer och installera IIS med hjälp av de steg som du precis har slutfört. Använd *myVM2* och *myVM3* för namn på virtuella datorer och **VMName** -värden i set-AzVMExtension.

## <a name="add-backend-servers-to-backend-pools"></a>Lägg till backend-servrar till backend-pooler

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **Serverdelspooler** på den vänstra menyn.

3. Välj **appGatewayBackendPool**.

4. Välj **Virtuell dator** i listrutan under **Mål**.

5. Under **virtuella datorer** och **nätverks gränssnitt**väljer du den virtuella **myVM1** -datorn och den är kopplad till nätverks gränssnittet från List rutorna.

    ![Lägga till serverdelsservrar](./media/create-url-route-portal/backend-pool.png)

6. Välj **Spara**.
7. Upprepa om du vill lägga till *myVM2* och gränssnitt till *ImagesBackendPool*och sedan *myVM3* och gränssnitt till *videoBackendPool*.

Vänta tills distributionen har slutförts innan du fortsätter till nästa steg.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Välj **alla resurser**och välj sedan **myAGPublicIPAddress**.

    ![Registrera programgatewayens offentliga IP-adress](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Till exempel http://40.121.222.19.

    ![Testa basadressen i programgatewayen](./media/create-url-route-portal/application-gateway-iistest.png)

3. Ändra URL: en till&lt;http://IP-&gt;Address: 8080/images/test.htm, &lt;ersätta IP-&gt; adress med din IP-adress och du bör se något som liknar följande exempel:

    ![Testa bildadressen i programgatewayen](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Ändra URL: en till&lt;http://IP-&gt;Address: 8080/video/test.htm, &lt;ersätta IP-&gt; adress med din IP-adress och du bör se något som liknar följande exempel:

    ![Testa videoadressen i programgatewayen](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen kan du ta bort resursgruppen. När du tar bort resursgruppen tas även programgatewayen och alla dess relaterade resurser bort. 

Så här tar du bort resursgruppen:

1. Välj **Resursgrupper** på den vänstra menyn på Azure-portalen.
2. På sidan **Resursgrupper** söker du efter och väljer **myResourceGroupAG**.
3. Välj **Ta bort resursgrupp** på sidan **Resursgrupp**.
4. Ange *myResourceGroupAG* för **Skriv resurs gruppens namn** och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om vad du kan göra med Azure Application Gateway](application-gateway-introduction.md)
