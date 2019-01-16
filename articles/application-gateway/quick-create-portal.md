---
title: 'Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure Portal | Microsoft Docs'
description: Lär dig att använda Azure Portal för att skapa en Azure Application Gateway som leder webbtrafik till virtuella datorer i en serverdelspool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 16e23f77509d2402f765981b39a30e08a2309f68
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156535"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure Portal

I den här snabbstarten visas hur du använder Azure Portal till att snabbt skapa programgatewayen med två virtuella datorer i serverdelspoolen. Sedan testar du den och kontrollerar att den fungerar korrekt. Med Azure Application Gateway kan du dirigera programmets webbtrafik till specifika resurser genom att: tilldela lyssnare till portar, skapa regler och lägga till resurser i en serverdelspool.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-konto.

## <a name="create-an-application-gateway"></a>Skapa en programgateway

För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen.

1. Välj **Skapa en resurs** på den vänstra menyn på Azure-portalen. Fönstret **Nytt** visas.

2. Välj **Nätverk** och välj sedan **Programgateway** i listan **Aktuella**.

### <a name="basics-page"></a>Sidan Grundläggande inställningar

1. På sidan **Grundläggande inställningar** anger du dessa värden för följande inställningar för programgatewayen:

    - **Namn**: Ange *myAppGateway* som namn på programgatewayen.
    - **Resursgrupp**: Välj **myResourceGroupAG** som resursgrupp. Om den inte finns väljer du **Skapa ny** för att skapa den.

    ![Skapa en ny programgateway](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. Godkänn standardvärdena för de andra inställningarna och välj sedan **OK**.

### <a name="settings-page"></a>Sidan Inställningar

1. Välj **Välj ett virtuellt nätverk** under **Undernätskonfiguration** på sidan **Inställningar**.

2. Välj **Skapa nytt** på sidan **Välj virtuellt nätverk** och ange sedan värden för följande inställningar för virtuella nätverk:

    - **Namn**: Ange *myVnet* som namn på det virtuella nätverket.

    - **Adressutrymme**: Ange *10.0.0.0/16* som adressutrymme för det virtuella nätverket.

    - **Namn på undernät**: Ange *myBackendSubnet* som namn på undernätet.<br>Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.

    - **Adressintervall för undernätet**: Ange *10.0.0.0/24* som adressintervall för undernätet.

    ![Skapa det virtuella nätverket](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

3. Välj **OK** för att återgå till sidan **Inställningar**.

4. Kontrollera att **IP-adresstyp** är inställd på **Offentlig** under **IP-konfiguration för klientdel**. Kontrollera att **Skapa ny** har valts under **Offentlig IP-adress**. 

5. Ange *myAGPublicIPAddress* som den offentliga IP-adressens namn. 

6. Godkänn standardvärdena för de andra inställningarna och välj sedan **OK**.

### <a name="summary-page"></a>Sammanfattningssida

Granska inställningarna på **sammanfattningssidan** och välj sedan **OK** för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen. Det kan ta flera minuter för Azure att skapa programgatewayen. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-a-subnet"></a>Lägga till ett undernät

Lägg till ett undernät i det virtuella nätverket som du skapade genom att följa dessa steg:

1. Välj **Alla resurser** på den vänstra menyn på Azure-portalen, ange *myVNet* i sökrutan och välj sedan **myVNet** i sökresultatet.

2. Välj **Undernät** på den vänstra menyn och välj sedan **+ Undernät**. 

    ![Skapa undernät](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Från sidan **Lägg till undernät** anger du *myBackendSubnet* som **namn** på undernätet och väljer sedan **OK**.

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här exemplet skapar du två virtuella datorer som Azure använder som backend-servrar för programgatewayen. Du kan också installera IIS på de virtuella datorerna för att kontrollera att Azure har skapat programgatewayen.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** på Azure-portalen. Fönstret **Nytt** visas.

2. Välj **Beräkning** och sedan **Windows Server 2016 Datacenter** i listan **Aktuella**. Sidan **Skapa en virtuell dator** visas.

3. Ange dessa värden på fliken **Grundläggande inställningar** för följande inställningar för virtuella datorer:

    - **Resursgrupp**: Välj **myResourceGroupAG** som namn på resursgruppen.
    - **Namn på virtuell dator**: Ange *myVM* som namn på den virtuella datorn.
    - **Användarnamn**: Ange *azureuser* som administratörens användarnamn.
    - **Lösenord**: Ange *Azure123456!* som administratörslösenord.

4. Acceptera de övriga standardinställningarna och välj sedan **Nästa: Diskar**.  

5. Acceptera standardinställningarna på fliken **Diskar** och välj sedan **Nästa: Nätverk**.

6. På fliken **Nätverk** kontrollerar du att **myVNet** har valts för **Virtuellt nätverk** och att **Undernät** är inställt på **myBackendSubnet**. Acceptera de övriga standardinställningarna och välj sedan **Nästa: Hantering**.

8. På fliken **Hantering** anger du **Startdiagnostik** till **Av**. Acceptera de övriga standardinställningarna och välj sedan **Granska + skapa**.

9. Gå igenom inställningarna på fliken **Granska + skapa** och åtgärda eventuella verifieringsfel och välj sedan **Skapa**.

10. Vänta på att skapandet av den virtuella datorn är klart innan du fortsätter.

### <a name="install-iis"></a>Installera IIS

1. Öppna [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Det gör du genom att först välja **Cloud Shell** i det övre navigeringsfältet på Azure-portalen och sedan välja **PowerShell** i listrutan. 

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

3. Skapa en andra virtuell dator och installera IIS genom att följa stegen som du utförde tidigare. Använd *myVM2* som namn på den virtuella datorn och för inställningen **VMName** i **Set-AzureRmVMExtension**-cmdleten.

### <a name="add-backend-servers"></a>Lägga till serverdelsservrar

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **Serverdelspooler** på den vänstra menyn. Azure skapade automatiskt en standardpool, **appGatewayBackendPool**, när du skapade programgatewayen. 

3. Välj **appGatewayBackendPool**.

4. Välj **Virtuell dator** i listrutan under **Mål**.

5. Under **VIRTUELL DATOR** och **NÄTVERKSGRÄNSSNITT** väljer du de virtuella datorerna **myVM** och **myVM2** och deras associerade nätverksgränssnitt i listrutorna.

    ![Lägga till serverdelsservrar](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Välj **Spara**.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Leta reda på den offentliga IP-adressen för programgatewayen på skärmen **Översikt**.

    ![Registrera programgatewayens offentliga IP-adress](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

    Du kan också välja **Alla resurser**, ange *myAGPublicIPAddress* i sökrutan och sedan välja den i sökresultatet. Azure visar den offentliga IP-adressen på sidan **Översikt**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

    ![Testa programgatewayen](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen kan du ta bort resursgruppen. När du tar bort resursgruppen tas även programgatewayen och alla dess relaterade resurser bort. 

Så här tar du bort resursgruppen:
1. Välj **Resursgrupper** på den vänstra menyn på Azure-portalen.
2. På sidan **Resursgrupper** söker du efter och väljer **myResourceGroupAG**.
3. Välj **Ta bort resursgrupp** på sidan **Resursgrupp**.
4. Ange *myResourceGroupAG* för **Skriv resursgruppens namn** och välj sedan **Ta bort**

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway som använder Azure CLI](./tutorial-manage-web-traffic-cli.md)
