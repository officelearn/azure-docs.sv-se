---
title: Skapa en Programgateway med en brandvägg för webbaserade program – Azure-portalen | Microsoft Docs
description: Lär dig hur du skapar en Programgateway med en brandvägg för webbaserade program med hjälp av Azure portal.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 7a090a068984a71c917cf5c33761dd78ac1ad2c8
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453264"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Skapa en Programgateway med en brandvägg för webbaserade program med Azure portal

Du kan använda Azure-portalen för att skapa en [programgatewayen](application-gateway-introduction.md) med en [Brandvägg för webbaserade program](application-gateway-web-application-firewall-overview.md) (WAF). I brandväggen används [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-regler till att skydda programmet. De här reglerna kan exempelvis skydda mot attacker som SQL-inmatning, skriptattacker mellan webbplatser och sessionskapningar.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en programgateway med WAF aktiverat
> * Skapa de virtuella datorerna som används som backend-servrar
> * Skapa ett lagringskonto och konfigurera diagnostik

![Exempel på brandvägg för webbaserade program](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Det krävs ett virtuellt nätverk för kommunikation mellan de resurser som du skapar. I det här exemplet skapas två undernät: ett för programgatewayen och ett annat för serverdelen. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen.

1. Klicka på **New** hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **Nätverk** och sedan **Application Gateway** i listan Aktuella.
3. Ange följande värden för programgatewayen:

    - *myAppGateway* – Namnet på programgatewayen.
    - *myResourceGroupAG* – Den nya resursgruppen.
    - Välj *WAF* för nivån av application gateway.

    ![Skapa en ny programgateway](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

4. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
5. Klicka på **Välj ett virtuellt nätverk**, klicka på **Skapa nytt** och ange sedan följande värden för det virtuella nätverket:

    - *myVnet* – Det virtuella nätverkets namn.
    - *10.0.0.0/16* – Det virtuella nätverkets adressutrymme.
    - *myBackendSubnet* – Undernätsnamnet.
    - *10.0.0.0/24* – Undernätets adressutrymme.

    ![Skapa det virtuella nätverket](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)

6. Klicka på **OK** för att skapa det virtuella nätverket och undernätet.
7. Klicka på **välja en offentlig IP-adress**, klickar du på **Skapa nytt**, och ange sedan namnet på den offentliga IP-adressen. I det här exemplet heter den offentliga IP-adressen *myAGPublicIPAddress*. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
8. Godkänn standardvärdena för Lyssnarkonfigurationen, lämna inaktiverad brandväggen för webbaserade program och sedan på **OK**.
9. Granska inställningarna på sidan Sammanfattning och klicka sedan på **OK** att skapa nätverksresurser och application gateway. Det kan ta flera minuter för application gateway kan skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="add-a-subnet"></a>Lägga till ett undernät

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myVNet** i resurslistan.
2. Klicka på **undernät**, och klicka sedan på **undernät**.

    ![Skapa undernät](./media/application-gateway-web-application-firewall-portal/application-gateway-subnet.png)

3. Ange *myBackendSubnet* som namn på undernätet och klicka sedan på **OK**.

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här exemplet skapar du två virtuella datorer som ska användas som serverdelsservrar för programgatewayen. Du installerar även IIS på de virtuella datorerna för att verifiera att programgatewayen har skapats.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **Ny**.
2. Klicka på **Compute** och sedan **Windows Server 2016 Datacenter** i listan Aktuella.
3. Ange följande värden för den virtuella datorn:

    - *myVM* – Namnet på den virtuella datorn.
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

    ![Installera anpassat tillägg](./media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

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
2. Klicka på **Serverdelspooler**. En standardpool skapades automatiskt med programgatewayen. Klicka på **appGatewayBackendPool**.
3. Klicka på **Lägg till mål** att lägga till varje virtuell dator som du skapade till serverdelspoolen.

    ![Lägga till serverdelsservrar](./media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

4. Klicka på **Spara**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Skapa ett lagringskonto och konfigurera diagnostik

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

I den här självstudien används ett lagringskonto till att lagra data för identifiering och förebyggande åtgärder. Du kan också använda Azure Monitor-loggar eller Event Hub för att registrera data.

1. Klicka på **New** hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **Lagring** och sedan **Koppla undernät – blob, fil, tabell, kö**.
3. Ange namnet på lagringskontot, Välj **Använd befintlig** för resursgruppen och välj sedan **myResourceGroupAG**. I det här exemplet är namnet på lagringskontot *myagstore1*. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **skapa**.

## <a name="configure-diagnostics"></a>Konfigurera diagnostiken

Konfigurera diagnostik för registrering av data i loggarna ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog och ApplicationGatewayFirewallLog.

1. I den vänstra menyn, klickar du på **alla resurser**, och välj sedan *myAppGateway*.
2. Under övervakning klickar du på **diagnostikloggar**.
3. Klicka på **Lägg till diagnostikinställning**.
4. Ange *myDiagnosticsSettings* som namn på diagnostikinställningarna.
5. Välj **arkivet till ett lagringskonto**, och klicka sedan på **konfigurera** att välja den *myagstore1* storage-konto som du skapade tidigare.
6. Välj application gateway-loggar att samla in och behålla.
7. Klicka på **Spara**.

    ![Konfigurera diagnostiken](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Leta reda på den offentliga IP-adressen för programgatewayen på skärmen Översikt. Klicka på **Alla resurser** och sedan på **myAGPublicIPAddress**.

    ![Registrera programgatewayens offentliga IP-adress](./media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png)

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

    ![Testa programgatewayen](./media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa en programgateway med WAF aktiverat
> * Skapa de virtuella datorerna som används som backend-servrar
> * Skapa ett lagringskonto och konfigurera diagnostik

Om du vill veta mer om application gateway och deras associerade resurser kan du fortsätta i instruktionsartiklarna.