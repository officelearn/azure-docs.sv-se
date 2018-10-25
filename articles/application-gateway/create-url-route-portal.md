---
title: Skapa en Programgateway med URL-sökvägsbaserad routning regler - Azure-portalen | Microsoft Docs
description: Lär dig mer om att skapa URL-baserad routningsregler för en application gateway och en skalningsuppsättning för virtuell dator med Azure portal.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: victorh
ms.openlocfilehash: 0c59405cc6d46bcaffbab0e2a746467eec26de69
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024128"
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Skapa en Programgateway med sökvägsbaserad routning regler med hjälp av Azure portal

Du kan använda Azure-portalen för att konfigurera [URL-baserad routningsregler](url-route-overview.md) när du skapar en [Programgateway](overview.md). I den här självstudien skapar du serverdelspooler med hjälp av virtuella datorer. Du kan sedan skapa routningsregler som gör att Internet-trafik anländer till rätt servrar i poolerna.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en programgateway
> * Skapa virtuella datorer för backend-servrar
> * Skapa serverdelspooler med backend-servrar
> * Skapa en backend-lyssnare
> * Skapa en sökvägsbaserad regel

![URL-routningsexempel](./media/create-url-route-portal/scenario.png)

Om du vill kan du slutföra den här självstudien med [Azure CLI](tutorial-url-route-cli.md) eller [Azure PowerShell](tutorial-url-route-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Ett virtuellt nätverk krävs för kommunikation mellan de resurser som du skapar. I det här exemplet skapas två undernät: ett för programgatewayen och ett annat för serverdelen. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen.

1. Klicka på **New** hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **Nätverk** och sedan **Application Gateway** i listan Aktuella.
3. Ange följande värden för programgatewayen:

    - *myAppGateway* – Namnet på programgatewayen.
    - *myResourceGroupAG* – Den nya resursgruppen.

    ![Skapa en ny programgateway](./media/create-url-route-portal/application-gateway-create.png)

4. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
5. Klicka på **Välj ett virtuellt nätverk**, klickar du på **Skapa nytt**, och ange följande värden för det virtuella nätverket:

    - *myVnet* – Det virtuella nätverkets namn.
    - *10.0.0.0/16* – Det virtuella nätverkets adressutrymme.
    - *myBackendSubnet* – Undernätsnamnet.
    - *10.0.0.0/24* – Undernätets adressutrymme.

    ![Skapa det virtuella nätverket](./media/create-url-route-portal/application-gateway-vnet.png)

6. Klicka på **OK** för att skapa det virtuella nätverket och undernätet.
7. Klicka på **välja en offentlig IP-adress**, klickar du på **Skapa nytt**, och ange sedan namnet på den offentliga IP-adressen. I det här exemplet heter den offentliga IP-adressen *myAGPublicIPAddress*. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
8. Godkänn standardvärdena för Lyssnarkonfigurationen, lämna inaktiverad brandväggen för webbaserade program och sedan på **OK**.
9. Granska inställningarna på sidan Sammanfattning och klicka sedan på **OK** att skapa nätverksresurser och application gateway. Det kan ta flera minuter för application gateway kan skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="add-a-subnet"></a>Lägga till ett undernät

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myVNet** i resurslistan.
2. Klicka på **undernät**, och klicka sedan på **undernät**.

    ![Skapa undernät](./media/create-url-route-portal/application-gateway-subnet.png)

3. Ange *myBackendSubnet* som namn på undernätet och klicka sedan på **OK**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

I det här exemplet skapar du tre virtuella datorer som ska användas som serverdelsservrar för application gateway. Du installerar även IIS på de virtuella datorerna för att verifiera att programgatewayen har skapats.

1. Klicka på **Ny**.
2. Klicka på **Compute** och välj sedan **Windows Server 2016 Datacenter** i listan över aktuella.
3. Ange följande värden för den virtuella datorn:

    - *myVM1* – för den virtuella datorns namn.
    - *azureuser* – för administratörens användarnamn.
    - *Azure123456!* som lösenord.
    - Välj **Använd befintlig** och sedan *myResourceGroupAG*.

4. Klicka på **OK**.
5. Välj **DS1_V2** som storlek på den virtuella datorn och klicka på **Välj**.
6. Kontrollera att **myVNet** har valts för det virtuella nätverket och att undernätet är **myBackendSubnet**. 
7. Inaktivera startdiagnostikinställningar genom att klicka på **Inaktiverad**.
8. Klicka på **OK**, granska inställningarna på sammanfattningssidan och klicka sedan på **Skapa**.

### <a name="install-iis"></a>Installera IIS

1. Öppna det interaktiva gränssnittet och kontrollera att det är inställt på **PowerShell**.

    ![Installera anpassat tillägg](./media/create-url-route-portal/application-gateway-extension.png)

2. Kör följande kommando för att installera IIS på den virtuella datorn: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Skapa två fler virtuella datorer och installera IIS med hjälp av de steg som du är klar. Ange namnen på *myVM2* och *myVM3* för namnen och värdena för VMName i Set-AzureRmVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Skapa backend-pooler med virtuella datorer

1. Klicka på **alla resurser** och klicka sedan på **myAppGateway**.
2. Klicka på **Serverdelspooler**. En standardpool skapades automatiskt med programgatewayen. Klicka på **appGateayBackendPool**.
3. Klicka på **Lägg till mål** att lägga till *myVM1* till appGatewayBackendPool.

    ![Lägga till serverdelsservrar](./media/create-url-route-portal/application-gateway-backend.png)

4. Klicka på **Spara**.
5. Klicka på **serverdelspooler** och klicka sedan på **Lägg till**.
6. Ange ett namn för *imagesBackendPool* och Lägg till *myVM2* med **Lägg till mål**.
7. Klicka på **OK**.
8. Klicka på **Lägg till** igen för att lägga till en annan serverdelspool med namnet *videoBackendPool* och Lägg till *myVM3* till den.

## <a name="create-a-backend-listener"></a>Skapa en backend-lyssnare

1. Klicka på **lyssnare** och klicka på **grundläggande**.
2. Ange *myBackendListener* efter namn, *myFrontendPort* för namnet på den frontend-porten, och sedan *8080* som port för lyssnaren.
3. Klicka på **OK**.

## <a name="create-a-path-based-routing-rule"></a>Skapa en sökvägsbaserad regel

1. Klicka på **regler** och klicka sedan på **sökvägsbaserad**.
2. Ange *2* för namnet.
3. Ange *avbildningar* för namnet på den första sökvägen. Ange */images/* \* för sökvägen. Välj **imagesBackendPool** för serverdelspoolen.
4. Ange *Video* för namnet på den andra sökvägen. Ange */video/* \* för sökvägen. Välj **videoBackendPool** för serverdelspoolen.

    ![Skapa en sökvägsbaserad regel](./media/create-url-route-portal/application-gateway-route-rule.png)

5. Klicka på **OK**.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Klicka på **alla resurser**, och klicka sedan på **myAGPublicIPAddress**.

    ![Registrera programgatewayens offentliga IP-adress](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Till exempel http://40.121.222.19.

    ![Testa basadressen i programgatewayen](./media/create-url-route-portal/application-gateway-iistest.png)

3. Ändra Webbadressen till http://&lt;ip-adress&gt;: 8080/images/test.htm, Ersätt &lt;ip-adress&gt; med din IP-adress, och du bör se ut ungefär som i följande exempel:

    ![Testa bildadressen i programgatewayen](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Ändra Webbadressen till http://&lt;ip-adress&gt;: 8080/video/test.htm, Ersätt &lt;ip-adress&gt; med din IP-adress, och du bör se ut ungefär som i följande exempel:

    ![Testa videoadressen i programgatewayen](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du

> [!div class="checklist"]
> * Skapa en programgateway
> * Skapa virtuella datorer för backend-servrar
> * Skapa serverdelspooler med backend-servrar
> * Skapa en backend-lyssnare
> * Skapa en sökvägsbaserad regel

Om du vill veta mer om application gateway och deras associerade resurser kan du fortsätta i instruktionsartiklarna.
