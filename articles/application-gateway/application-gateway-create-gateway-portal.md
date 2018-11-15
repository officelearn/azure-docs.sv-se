---
title: Skapa en Programgateway - Azure-portalen
description: Lär dig hur du skapar en Programgateway med hjälp av Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2018
ms.author: victorh
ms.openlocfilehash: d8ab49bc988060533bc5ff65d414dcba6245be48
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51631917"
---
# <a name="create-an-application-gateway-using-the-azure-portal"></a>Skapa en Programgateway med hjälp av Azure portal

Du kan använda Azure-portalen för att skapa eller hantera programgatewayer. Den här artikeln visar hur du skapar nätverksresurser serverdelsservrar och en Programgateway.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Ett virtuellt nätverk krävs för kommunikation mellan de resurser som du skapar. I det här exemplet skapas två undernät: ett för programgatewayen och ett annat för serverdelen. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Klicka på **nätverk** och klicka sedan på **Application Gateway** i listan över aktuella.

### <a name="basics"></a>Grundläggande inställningar

1. Ange följande värden för programgatewayen:

    - *myAppGateway* – Namnet på programgatewayen.
    - *myResourceGroupAG* – Den nya resursgruppen.

    ![Skapa en ny programgateway](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.

### <a name="settings"></a>Inställningar

1. Klicka på **Välj ett virtuellt nätverk**, klickar du på **Skapa nytt**, och ange följande värden för det virtuella nätverket:

    - *myVnet* – Det virtuella nätverkets namn.
    - *10.0.0.0/16* – Det virtuella nätverkets adressutrymme.
    - *myBackendSubnet* – Undernätsnamnet.
    - *10.0.0.0/24* – för adressintervallet i undernätet.

    ![Skapa det virtuella nätverket](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Klicka på **OK** att gå tillbaka till sidan Inställningar.
7. Under **Frontend-IP-konfiguration** Kontrollera **IP-adresstyp** är inställd på **offentliga**, och under **offentliga IP-adressen**, se till att **Skapa ny** har valts. Typ *myAGPublicIPAddress* för namnet för offentliga IP-adress. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.

### <a name="summary"></a>Sammanfattning

Granska inställningarna på sammanfattningssidan och klicka sedan på **OK** för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen. Det kan ta flera minuter att skapa programgatewayen. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-a-subnet"></a>Lägga till ett undernät

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myVNet** i resurslistan.
2. Klicka på **undernät**, och klicka sedan på **+ undernät**.

    ![Skapa undernät](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Ange *myBackendSubnet* som namn på undernätet och klicka sedan på **OK**.

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här exemplet skapar du två virtuella datorer som används som backend-servrarna för application gateway. Du installerar även IIS på de virtuella datorerna för att verifiera att programgatewayen har skapats.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **Skapa en resurs** i Azure Portal.
2. Klicka på **Compute** och välj sedan **Windows Server 2016 Datacenter** i listan över aktuella.
3. Ange följande värden för den virtuella datorn:

    - *myResourceGroupAG* för resursgruppen.
    - *myVM* – Namnet på den virtuella datorn.
    - *azureuser* – för administratörens användarnamn.
    - *Azure123456!* som lösenord.

   Godkänn andra standardinställningarna och klicka på **nästa: diskar**.
4. Acceptera standardinställningarna för disk och klicka på **nästa: nätverk**.
5. Kontrollera att **myVNet** har valts för det virtuella nätverket och att undernätet är **myBackendSubnet**.
6. Godkänn andra standardinställningarna och klicka på **nästa: Management**.
7. Klicka på **av** inaktivera startdiagnostikinställningar. Godkänn andra standardinställningarna och klicka på **granska + skapa**.
8. Granska inställningarna på sidan Sammanfattning och klicka sedan på **skapa**.
9. Vänta på att skapa en virtuell dator ska slutföras innan du fortsätter.

### <a name="install-iis"></a>Installera IIS

1. Öppna det interaktiva gränssnittet och se till att den är inställd på **PowerShell**.

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

1. Klicka på **alla resurser**, och klicka sedan på **myAppGateway**.
4. Klicka på **Serverdelspooler**. En standardpool skapades automatiskt med programgatewayen. Klicka på **appGatewayBackendPool**.
5. Under **mål**, klickar du på **IP-adress eller FQDN** Välj **VM**.
6. Under **VM**, lägga till myVM och myVM2 virtuella datorer och deras tillhörande nätverksgränssnitt.

    ![Lägga till serverdelsservrar](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Klicka på **Spara**.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Leta reda på den offentliga IP-adressen för programgatewayen på skärmen Översikt. Klicka på **alla resurser** och klicka sedan på **myAGPublicIPAddress**.

    ![Registrera programgatewayens offentliga IP-adress](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

    ![Testa programgatewayen](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, Programgateway och alla relaterade resurser när de inte längre behövs. Gör detta genom att markera den resursgrupp som innehåller programgatewayen och sedan klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har skapat du en resursgrupp, nätverksresurser och backend-servrarna. Du använde sedan dessa resurser för att skapa en Programgateway. Läs mer om application gateway och deras associerade resurser i [vad är Azure Application Gateway?](overview.md)
