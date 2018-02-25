---
title: "Skapa en Programgateway med URL-sökväg-baserade regler för routning - Azure-portalen | Microsoft Docs"
description: "Lär dig mer om att skapa URL-sökväg-baserade routningsregler för en Programgateway och skaluppsättningen för virtuell dator med hjälp av Azure portal."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: 62063c42ab15a071a4500417a5d8adf6bfeac97f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Skapa en Programgateway med sökväg-baserade regler för routning med Azure-portalen

Du kan använda Azure-portalen för att konfigurera [URL-sökväg-baserade regler för routning](application-gateway-url-route-overview.md) när du skapar en [Programgateway](application-gateway-introduction.md). I den här självstudiekursen skapar du serverdelspooler med hjälp av virtuella datorer. Sedan kan du skapa regler för routning som kontrollerar Internet-trafik anländer till rätt servrar i poolerna.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Skapa en programgateway
> * Skapa virtuella datorer för backend-servrar
> * Skapa serverdelspooler med backend-servrar
> * Skapa en backend-lyssnare
> * Skapa en sökväg-baserade regel

![Exempel på URL: en Routning](./media/application-gateway-create-url-route-portal/scenario.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Ett virtuellt nätverk behövs för kommunikation mellan resurser som du skapar. Två undernät skapas i det här exemplet: en för programgatewayen och en för backend-servrarna. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen.

1. Klicka på **ny** hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **nätverk** och välj sedan **Programgateway** i listan över aktuella.
3. Ange värdena för Programgateway:

    - *myAppGateway* – namnet på programgatewayen.
    - *myResourceGroupAG* - för den nya resursgruppen.

    ![Skapa nya Programgateway](./media/application-gateway-create-url-route-portal/application-gateway-create.png)

4. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
5. Klicka på **Välj ett virtuellt nätverk**, klickar du på **Skapa nytt**, och ange sedan värdena för det virtuella nätverket:

    - *myVNet* – namnet på det virtuella nätverket.
    - *10.0.0.0/16* - för virtuella nätverkets adressutrymme.
    - *myAGSubnet* - för undernätsnamnet.
    - *10.0.0.0/24* - för undernätsadressutrymmet.

    ![Skapa det virtuella nätverket](./media/application-gateway-create-url-route-portal/application-gateway-vnet.png)

6. Klicka på **OK** att skapa virtuella nätverk och undernät.
7. Klicka på **Välj en offentlig IP-adress**, klickar du på **Skapa nytt**, och ange sedan namnet på den offentliga IP-adressen. I det här exemplet heter den offentliga IP-adressen *myAGPublicIPAddress*. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
8. Godkänn standardvärdena för Lyssnarkonfigurationen lämna inaktiverad Brandvägg för webbaserade program och klicka sedan på **OK**.
9. Granska inställningarna på sidan Sammanfattning och klicka sedan på **OK** skapa nätverksresurser och programgatewayen. Det kan ta flera minuter för Programgateway skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="add-a-subnet"></a>Lägg till ett undernät

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på **myVNet** från resurslistan över.
2. Klicka på **undernät**, och klicka sedan på **undernät**.

    ![Skapa undernät](./media/application-gateway-create-url-route-portal/application-gateway-subnet.png)

3. Ange *myBackendSubnet* för namnet på undernätet och klickar sedan på **OK**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

I det här exemplet kan du skapa tre virtuella datorer som ska användas som backend-servrar för programgatewayen. Du kan även installera IIS på de virtuella datorerna för att verifiera att programgatewayen har skapats.

1. Klicka på **Ny**.
2. Klicka på **Compute** och välj sedan **Windows Server 2016 Datacenter** i listan över aktuella.
3. Ange dessa värden för den virtuella datorn:

    - *myVM1* – namnet på den virtuella datorn.
    - *azureuser* - för administratörsanvändarnamn.
    - *Azure123456!* för lösenordet.
    - Välj **använda befintliga**, och välj sedan *myResourceGroupAG*.

4. Klicka på **OK**.
5. Välj **DS1_V2** för storleken på den virtuella datorn och klicka på **Välj**.
6. Se till att **myVNet** har valts för det virtuella nätverket och undernätet är **myBackendSubnet**. 
7. Klicka på **inaktiverad** att inaktivera startdiagnostikinställningar.
8. Klicka på **OK**granska inställningarna på sidan Sammanfattning och klicka sedan på **skapa**.

### <a name="install-iis"></a>Installera IIS

1. Öppna det interaktiva gränssnittet och se till att den är inställd på **PowerShell**.

    ![Installera anpassade tillägg](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Kör följande kommando för att installera IIS på den virtuella datorn: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
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

3. Skapa två fler virtuella datorer och installera IIS med hjälp av stegen som du just har avslutats. Ange namnen på *myVM2* och *myVM3* för namnen och värdena för VMName i Set-AzureRmVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Skapa backend-pooler med virtuella datorer

1. Klicka på **alla resurser** och klicka sedan på **myAppGateway**.
2. Klicka på **serverdelspooler**. En standardadresspool skapas automatiskt med programgatewayen. Klicka på **appGatewayBackendPool**.
3. Klicka på **Lägg till mål** att lägga till *myVM1* till appGatewayBackendPool.

    ![Lägg till backend-servrar](./media/application-gateway-create-url-route-portal/application-gateway-backend.png)

4. Klicka på **Spara**.
5. Klicka på **serverdelspooler** och klicka sedan på **Lägg till**.
6. Ange ett namn med *imagesBackendPool* och Lägg till *myVM2* med **Lägg till mål**.
7. Klicka på **OK**.
8. Klicka på **Lägg till** igen för att lägga till en annan serverdelspool med namnet *videoBackendPool* och Lägg till *myVM3* till den.

## <a name="create-a-backend-listener"></a>Skapa en backend-lyssnare

1. Klicka på **lyssnare** och klicka på **grundläggande**.
2. Ange *myBackendListener* för namn, *myFrontendPort* för frontend-porten och sedan *8080* som port för lyssnaren.
3. Klicka på **OK**.

## <a name="create-a-path-based-routing-rule"></a>Skapa en sökväg-baserade regel

1. Klicka på **regler** och klicka sedan på **sökväg-baserade**.
2. Ange *regel 2* för namnet.
3. Ange *bilder* för namnet på den första sökvägen. Ange */images/** för sökvägen. Välj **imagesBackendPool** för serverdelspoolen.
4. Ange *Video* för namnet på den andra sökvägen. Ange */video/** för sökvägen. Välj **videoBackendPool** för serverdelspoolen.

    ![Skapa en regel som sökväg-baserade](./media/application-gateway-create-url-route-portal/application-gateway-route-rule.png)

5. Klicka på **OK**.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Klicka på **alla resurser**, och klicka sedan på **myAGPublicIPAddress**.

    ![Registrera programmet gateway offentlig IP-adress](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Kopiera den offentliga IP-adressen och klistra in den i adressfältet i webbläsaren. Till exempel http://http: / / 40.121.222.19.

    ![Testa bas-URL i Programgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

3. Ändra Webbadressen till http://&lt;ip-adress&gt;: 8080/video/test.htm, ersätter &lt;ip-adress&gt; med IP-adress, och du bör se något som liknar följande exempel:

    ![Testa URL: en för bilder i Programgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

4. Ändra Webbadressen till http://&lt;ip-adress&gt;: 8080/video/test.htm, ersätter &lt;ip-adress&gt; med IP-adress, och du bör se något som liknar följande exempel:

    ![Testa video-URL i Programgateway](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du lära dig hur du

> [!div class="checklist"]
> * Skapa en programgateway
> * Skapa virtuella datorer för backend-servrar
> * Skapa serverdelspooler med backend-servrar
> * Skapa en backend-lyssnare
> * Skapa en sökväg-baserade regel

Mer information om programgatewayer och deras associerade resurser fortsätter du att artiklarna.
