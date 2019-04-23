---
title: Självstudie – skapa en Programgateway med URL-sökvägsbaserad routning regler - Azure-portalen
description: I de här självstudierna lär du dig att skapa URL-baserad routningsregler för en application gateway och en skalningsuppsättning för virtuell dator med Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/18/2019
ms.author: victorh
ms.openlocfilehash: 5307f7674635fd33241e1faba9bb0b7c0432d10b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001039"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Självstudier: Skapa en Programgateway med sökvägsbaserad routning regler med hjälp av Azure portal

Du kan använda Azure-portalen för att konfigurera [URL-baserad routningsregler](url-route-overview.md) när du skapar en [Programgateway](overview.md). I den här självstudien skapar du serverdelspooler med hjälp av virtuella datorer. Du kan sedan skapa routningsregler som gör att Internet-trafik anländer till rätt servrar i poolerna.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en programgateway
> * Skapa virtuella datorer för backend-servrar
> * Skapa serverdelspooler med backend-servrar
> * Skapa en backend-lyssnare
> * Skapa en sökvägsbaserad regel

![URL-routningsexempel](./media/create-url-route-portal/scenario.png)

Om du vill kan du slutföra den här självstudien med [Azure CLI](tutorial-url-route-cli.md) eller [Azure PowerShell](tutorial-url-route-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Ett virtuellt nätverk krävs för kommunikation mellan de resurser du skapar. I det här exemplet skapas två undernät: ett för programgatewayen och ett annat för serverdelen. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen.

1. Välj **New** hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **Nätverk** och sedan **Application Gateway** i listan Aktuella.
3. Ange följande värden för programgatewayen:

   - *myAppGateway* – Namnet på programgatewayen.
   - *myResourceGroupAG* – Den nya resursgruppen.

     ![Skapa en ny programgateway](./media/create-url-route-portal/application-gateway-create.png)

4. Godkänn standardvärdena för de andra inställningarna och välj sedan **OK**.
5. Välj **Välj ett virtuellt nätverk**väljer **Skapa nytt**, och ange följande värden för det virtuella nätverket:

   - *myVnet* – Det virtuella nätverkets namn.
   - *10.0.0.0/16* – Det virtuella nätverkets adressutrymme.
   - *myBackendSubnet* – Undernätsnamnet.
   - *10.0.0.0/24* – Undernätets adressutrymme.

     ![Skapa det virtuella nätverket](./media/create-url-route-portal/application-gateway-vnet.png)

6. Välj **OK** att skapa virtuellt nätverk och undernät.
7. Välj **välja en offentlig IP-adress**väljer **Skapa nytt**, och ange sedan namnet på den offentliga IP-adressen. I det här exemplet heter den offentliga IP-adressen *myAGPublicIPAddress*. Godkänn standardvärdena för de andra inställningarna och välj sedan **OK**.
8. Godkänn standardvärdena för Lyssnarkonfigurationen, lämna inaktiverad brandväggen för webbaserade program och välj sedan **OK**.
9. Granska inställningarna på sidan Sammanfattning och välj sedan **OK** att skapa nätverksresurser och application gateway. Det kan ta flera minuter för application gateway kan skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="add-a-subnet"></a>Lägga till ett undernät

1. Välj **alla resurser** i den vänstra menyn och välj sedan **myVNet** i resurslistan.
2. Välj **undernät**, och välj sedan **undernät**.

    ![Skapa undernät](./media/create-url-route-portal/application-gateway-subnet.png)

3. Ange *myBackendSubnet* för namnet på undernätet och välj sedan **OK**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

I det här exemplet skapar du tre virtuella datorer som ska användas som serverdelsservrar för application gateway. Du kan också installera IIS på de virtuella datorerna för att verifiera application gateway har skapats.

1. Välj **Ny**.
2. Välj **Compute** och sedan **Windows Server 2016 Datacenter** i listan Aktuella.
3. Ange följande värden för den virtuella datorn:

    - *myVM1* – för den virtuella datorns namn.
    - *azureuser* – för administratörens användarnamn.
    - *Azure123456!* som lösenord.
    - Välj **Använd befintlig** och sedan *myResourceGroupAG*.

4. Välj **OK**.
5. Välj **DS1_V2** för storleken på den virtuella datorn och välj **Välj**.
6. Kontrollera att **myVNet** har valts för det virtuella nätverket och att undernätet är **myBackendSubnet**. 
7. Inaktivera startdiagnostikinställningar genom att välja **Inaktiverad**.
8. Välj **OK**, granska inställningarna på sammanfattningssidan och välj sedan **Skapa**.

### <a name="install-iis"></a>Installera IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Öppna det interaktiva gränssnittet och kontrollera att den är inställd på **PowerShell**.

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

3. Skapa två fler virtuella datorer och installera IIS med hjälp av de steg som du är klar. Ange namnen på *myVM2* och *myVM3* för namnen och värdena för VMName i Set-AzVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Skapa backend-pooler med virtuella datorer

1. Välj **alla resurser** och välj sedan **myAppGateway**.
2. Välj **serverdelspooler**. En standardpool skapades automatiskt med programgatewayen. Välj **appGatewayBackendPool**.
3. Välj **Lägg till mål** att lägga till *myVM1* till appGatewayBackendPool.

    ![Lägga till serverdelsservrar](./media/create-url-route-portal/application-gateway-backend.png)

4. Välj **Spara**.
5. Välj **serverdelspooler** och välj sedan **Lägg till**.
6. Ange ett namn för *imagesBackendPool* och Lägg till *myVM2* med **Lägg till mål**.
7. Välj **OK**.
8. Välj **Lägg till** igen för att lägga till en annan serverdelspool med namnet *videoBackendPool* och Lägg till *myVM3* till den.

## <a name="create-a-backend-listener"></a>Skapa en backend-lyssnare

1. Välj **lyssnare** och klicka sedan **grundläggande**.
2. Ange *myBackendListener* efter namn, *myFrontendPort* för namnet på den frontend-porten, och sedan *8080* som port för lyssnaren.
3. Välj **OK**.

## <a name="create-a-path-based-routing-rule"></a>Skapa en sökvägsbaserad regel

1. Välj **regler** och välj sedan **sökvägsbaserad**.
2. Ange *2* för namnet.
3. Ange *avbildningar* för namnet på den första sökvägen. Ange */images/* \* för sökvägen. Välj **imagesBackendPool** för serverdelspoolen.
4. Ange *Video* för namnet på den andra sökvägen. Ange */video/* \* för sökvägen. Välj **videoBackendPool** för serverdelspoolen.

    ![Skapa en sökvägsbaserad regel](./media/create-url-route-portal/application-gateway-route-rule.png)

5. Välj **OK**.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Välj **alla resurser**, och välj sedan **myAGPublicIPAddress**.

    ![Registrera programgatewayens offentliga IP-adress](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Till exempel http://40.121.222.19.

    ![Testa basadressen i programgatewayen](./media/create-url-route-portal/application-gateway-iistest.png)

3. Ändra Webbadressen till http://&lt;ip-adress&gt;: 8080/images/test.htm, ersätta &lt;ip-adress&gt; med din IP-adress, och du bör se ut ungefär som i följande exempel:

    ![Testa bildadressen i programgatewayen](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Ändra Webbadressen till http://&lt;ip-adress&gt;: 8080/video/test.htm, ersätta &lt;ip-adress&gt; med din IP-adress, och du bör se ut ungefär som i följande exempel:

    ![Testa videoadressen i programgatewayen](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen kan du ta bort resursgruppen. När du tar bort resursgruppen tas även programgatewayen och alla dess relaterade resurser bort. 

Så här tar du bort resursgruppen:

1. Välj **Resursgrupper** på den vänstra menyn på Azure-portalen.
2. På sidan **Resursgrupper** söker du efter och väljer **myResourceGroupAG**.
3. Välj **Ta bort resursgrupp** på sidan **Resursgrupp**.
4. Ange *myResourceGroupAG* för **Skriv RESURSGRUPPSNAMNET** och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mer information om vad du kan göra med Azure Application Gateway](application-gateway-introduction.md)
