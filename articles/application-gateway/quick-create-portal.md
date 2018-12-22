---
title: 'Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure Portal | Microsoft Docs'
description: Lär dig att använda Azure Portal för att skapa en Azure Application Gateway som leder webbtrafik till virtuella datorer i en serverdelspool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/15/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7e11affece7e7eb133aa22e159ec07d4f15e96f7
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999606"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure Portal

Med Azure Application Gateway kan du dirigera programmets webbtrafik till specifika resurser genom att tilldela lyssnare till portar, skapa regler och att lägga till resurser i en serverdelspool.

I den här snabbstarten visas hur du använder Azure Portal till att snabbt skapa programgatewayen med två virtuella datorer i serverdelspoolen. Sedan testar du den och kontrollerar att den fungerar korrekt.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Det krävs ett virtuellt nätverk för kommunikation mellan de resurser som du skapar. I det här exemplet skapas två undernät: ett för programgatewayen och ett annat för serverdelen. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Klicka på **Nätverk** och sedan på **Application Gateway** i listan Aktuella.

### <a name="basics"></a>Grundläggande inställningar

1. Ange följande värden för programgatewayen:

    - *myAppGateway* – Namnet på programgatewayen.
    - *myResourceGroupAG* – Den nya resursgruppen.

    ![Skapa en ny programgateway](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.

### <a name="settings"></a>Inställningar

1. Klicka på **Välj ett virtuellt nätverk**, klicka på **Skapa nytt** och ange sedan följande värden för det virtuella nätverket:

    - *myVnet* – Det virtuella nätverkets namn.
    - *10.0.0.0/16* – Det virtuella nätverkets adressutrymme.
    - *myBackendSubnet* – Undernätsnamnet.
    - *10.0.0.0/24* – Undernätets adressintervall.

    ![Skapa det virtuella nätverket](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Gå tillbaka till sidan Inställningar genom att klicka på **OK**.
7. Under **Frontend-IP-konfiguration** kontrollerar du att **IP-adresstyp** är inställt på **offentlig**, och under **Offentlig IP-adress** ser du till att **Skapa ny** har valts. Skriv in *myAGPublicIPAddress* som namn för offentlig IP-adress. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.

### <a name="summary"></a>Sammanfattning

Granska inställningarna på sammanfattningssidan och klicka sedan på **OK** för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen. Det kan ta flera minuter att skapa programgatewayen. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-a-subnet"></a>Lägga till ett undernät

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myVNet** i resurslistan.
2. Klicka på **Undernät** och sedan på **+ Undernät**.

    ![Skapa undernät](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Ange *myBackendSubnet* som namn på undernätet och klicka sedan på **OK**.

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här exemplet skapar du två virtuella datorer som används som serverdelsservrar för programgatewayen. Du installerar även IIS på de virtuella datorerna för att verifiera att programgatewayen har skapats.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **Skapa en resurs** i Azure Portal.
2. Klicka på **Compute** och sedan **Windows Server 2016 Datacenter** i listan Aktuella.
3. Ange följande värden för den virtuella datorn:

    - *myResourceGroupAG* för resursgruppen.
    - *myVM* – Namnet på den virtuella datorn.
    - *azureuser* – för administratörens användarnamn.
    - *Azure123456!* som lösenord.

   Acceptera övriga standardvärden och klicka på **Nästa: Diskar**.
4. Acceptera standardvärdena för diskar och klicka på **Nästa: Nätverk**.
5. Kontrollera att **myVNet** har valts för det virtuella nätverket och att undernätet är **myBackendSubnet**.
6. Acceptera övriga standardvärden och klicka på **Nästa: Hantering**.
7. Klicka på **Av** för att inaktivera startdiagnostik. Acceptera de andra standardvärdena och klicka på **Granska + skapa**.
8. Granska inställningarna på sammanfattningssidan och klicka sedan på **Skapa**.
9. Vänta på att skapandet av den virtuella datorn är klart innan du fortsätter.

### <a name="install-iis"></a>Installera IIS

1. Öppna det interaktiva gränssnittet och kontrollera att det är inställt på **PowerShell**.

    ![Installera anpassat tillägg](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

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

3. Skapa en andra virtuell dator och installera IIS med hjälp av de steg som du just har slutfört. Ange *myVM2* som namn och VMName i Set-AzureRmVMExtension.

### <a name="add-backend-servers"></a>Lägga till serverdelsservrar

1. Klicka på **Alla resurser** och sedan på **myAppGateway**.
4. Klicka på **Serverdelspooler**. En standardpool skapades automatiskt med programgatewayen. Klicka på **appGatewayBackendPool**.
5. Under **Mål** klickar du på **IP-adress eller FQDN** och väljer **Virtuell dator**.
6. Under **Virtuell dator** lägger du till de virtuella datorerna myVM och myVM2 samt deras tillhörande nätverksgränssnitt.

    ![Lägga till serverdelsservrar](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Klicka på **Spara**.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Leta reda på den offentliga IP-adressen för programgatewayen på skärmen Översikt. Klicka på **Alla resurser** och sedan på **myAGPublicIPAddress**.

    ![Registrera programgatewayens offentliga IP-adress](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

    ![Testa programgatewayen](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte behöver dem längre tar du bort resursgruppen, programgatewayen och alla relaterade resurser. Gör detta genom att markera den resursgrupp som innehåller programgatewayen och sedan klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway som använder Azure CLI](./tutorial-manage-web-traffic-cli.md)
