---
title: Skapa en Programgateway - Azure-portalen | Microsoft Docs
description: "Lär dig hur du skapar en Programgateway med hjälp av Azure portal."
services: application-gateway
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: davidmu
ms.openlocfilehash: df9235bc7ff61943de52a0bcc4064bf9fab6636a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-using-the-azure-portal"></a>Skapa en Programgateway med Azure-portalen

Du kan använda Azure-portalen för att skapa eller hantera programgatewayer. Den här snabbstarten visar hur du skapar nätverksresurser backend-servrar och en Programgateway.

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

    ![Skapa nya Programgateway](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

4. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
5. Klicka på **Välj ett virtuellt nätverk**, klickar du på **Skapa nytt**, och ange sedan värdena för det virtuella nätverket:

    - *myVNet* – namnet på det virtuella nätverket.
    - *10.0.0.0/16* - för virtuella nätverkets adressutrymme.
    - *myAGSubnet* - för undernätsnamnet.
    - *10.0.0.0/24* - för undernätsadressutrymmet.

    ![Skapa det virtuella nätverket](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Klicka på **OK** att skapa virtuella nätverk och undernät.
6. Klicka på **Välj en offentlig IP-adress**, klickar du på **Skapa nytt**, och ange sedan namnet på den offentliga IP-adressen. I det här exemplet heter den offentliga IP-adressen *myAGPublicIPAddress*. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
8. Godkänn standardvärdena för Lyssnarkonfigurationen lämna inaktiverad Brandvägg för webbaserade program och klicka sedan på **OK**.
9. Granska inställningarna på sidan Sammanfattning och klicka sedan på **OK** att skapa det virtuella nätverket och den offentliga IP-adressen programgatewayen. Det kan ta flera minuter för Programgateway skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="add-a-subnet"></a>Lägg till ett undernät

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på **myVNet** från resurslistan över.
2. Klicka på **undernät**, och klicka sedan på **undernät**.

    ![Skapa ett undernät](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Ange *myBackendSubnet* för namnet på undernätet och klickar sedan på **OK**.

## <a name="create-backend-servers"></a>Skapa backend-servrar

I det här exemplet skapar du två virtuella datorer som ska användas som backend-servrar för programgatewayen. Du kan även installera IIS på de virtuella datorerna för att verifiera att programgatewayen har skapats.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **Ny**.
2. Klicka på **Compute** och välj sedan **Windows Server 2016 Datacenter** i listan över aktuella.
3. Ange dessa värden för den virtuella datorn:

    - *myVM* – namnet på den virtuella datorn.
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

    ![Installera anpassade tillägg](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Kör följande kommando för att installera IIS på den virtuella datorn: 

    ```azurepowershell-interactive
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Skapa en andra virtuell dator och installera IIS med hjälp av stegen som du just har avslutats. Ange *myVM2* för dess namn och VMName i Set-AzureRmVMExtension.

### <a name="add-backend-servers"></a>Lägg till backend-servrar

3. Klicka på **alla resurser**, och klicka sedan på **myAppGateway**.
4. Klicka på **serverdelspooler**. En standardadresspool skapas automatiskt med programgatewayen. Klicka på **appGatewayBackendPool**.
5. Klicka på **Lägg till mål** att lägga till varje virtuell dator som du skapade i serverdelspoolen.

    ![Lägg till backend-servrar](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Klicka på **Spara**.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Hitta den offentliga IP-adressen för Programgateway på skärmen Översikt. Klicka på **alla resurser** och klicka sedan på **myAGPublicIPAddress**.

    ![Registrera programmet gateway offentlig IP-adress](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. Kopiera den offentliga IP-adressen och klistra in den i adressfältet i webbläsaren.

    ![Testa Programgateway](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen och Programgateway alla relaterade resurser när de inte längre behövs. Gör du genom att markera den resursgrupp som innehåller Programgateway och klicka på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en resursgrupp, nätverksresurser och backend-servrar. Du kan sedan använda resurserna för att skapa en Programgateway. Mer information om programgatewayer och deras associerade resurser fortsätter du att artiklarna.
