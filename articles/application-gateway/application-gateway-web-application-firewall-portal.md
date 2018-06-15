---
title: Skapa en Programgateway med en brandvägg för webbaserade program - Azure-portalen | Microsoft Docs
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
ms.openlocfilehash: 9967813b193159b68aa0f008dae4440aa6e533dc
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
ms.locfileid: "33207399"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Skapa en Programgateway med en brandvägg för webbaserade program som använder Azure portal

Du kan använda Azure-portalen för att skapa en [Programgateway](application-gateway-introduction.md) med en [Brandvägg för webbaserade program](application-gateway-web-application-firewall-overview.md) (Brandvägg). Brandvägg använder [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) regler för att skydda ditt program. Dessa regler innehåller skydd mot attacker, till exempel SQL injection, webbplatser scripting attacker och sessionen hijacks.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en Programgateway med Brandvägg aktiverat
> * Skapa de virtuella datorerna som används som backend-servrar
> * Skapa ett lagringskonto och konfigurera diagnostik

![Exempel på brandväggen ett webbprogram](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure portal [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Ett virtuellt nätverk behövs för kommunikation mellan resurser som du skapar. Två undernät skapas i det här exemplet: en för programgatewayen och en för backend-servrarna. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen.

1. Klicka på **ny** hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **nätverk** och välj sedan **Programgateway** i listan över aktuella.
3. Ange värdena för Programgateway:

    - *myAppGateway* – namnet på programgatewayen.
    - *myResourceGroupAG* - för den nya resursgruppen.
    - Välj *Brandvägg* för nivån av programgatewayen.

    ![Skapa nya Programgateway](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

4. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
5. Klicka på **Välj ett virtuellt nätverk**, klickar du på **Skapa nytt**, och ange sedan värdena för det virtuella nätverket:

    - *myVNet* – namnet på det virtuella nätverket.
    - *10.0.0.0/16* - för virtuella nätverkets adressutrymme.
    - *myAGSubnet* - för undernätsnamnet.
    - *10.0.0.0/24* - för undernätsadressutrymmet.

    ![Skapa det virtuella nätverket](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)

6. Klicka på **OK** att skapa virtuella nätverk och undernät.
7. Klicka på **Välj en offentlig IP-adress**, klickar du på **Skapa nytt**, och ange sedan namnet på den offentliga IP-adressen. I det här exemplet heter den offentliga IP-adressen *myAGPublicIPAddress*. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
8. Godkänn standardvärdena för Lyssnarkonfigurationen lämna inaktiverad Brandvägg för webbaserade program och klicka sedan på **OK**.
9. Granska inställningarna på sidan Sammanfattning och klicka sedan på **OK** att skapa nätverksresurser och programgatewayen. Det kan ta flera minuter för Programgateway skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="add-a-subnet"></a>Lägg till ett undernät

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på **myVNet** från resurslistan över.
2. Klicka på **undernät**, och klicka sedan på **undernät**.

    ![Skapa undernät](./media/application-gateway-web-application-firewall-portal/application-gateway-subnet.png)

3. Ange *myBackendSubnet* för namnet på undernätet och klickar sedan på **OK**.

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här exemplet skapar du två virtuella datorer som ska användas som backend-servrar för programgatewayen. Du kan även installera IIS på de virtuella datorerna för att verifiera att programgatewayen har skapats.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **Ny**.
2. Klicka på **Compute** och välj sedan **Windows Server 2016 Datacenter** i listan över aktuella.
3. Ange dessa värden för den virtuella datorn:

    - *myVM* – namnet på den virtuella datorn.
    - *azureuser* – för administratörens användarnamn.
    - *Azure123456!* för lösenordet.
    - Välj **använda befintliga**, och välj sedan *myResourceGroupAG*.

4. Klicka på **OK**.
5. Välj **DS1_V2** som storlek på den virtuella datorn och klicka på **Välj**.
6. Se till att **myVNet** har valts för det virtuella nätverket och undernätet är **myBackendSubnet**. 
7. Inaktivera startdiagnostikinställningar genom att klicka på **Inaktiverad**.
8. Klicka på **OK**, granska inställningarna på sammanfattningssidan och klicka sedan på **Skapa**.

### <a name="install-iis"></a>Installera IIS

1. Öppna det interaktiva gränssnittet och se till att den är inställd på **PowerShell**.

    ![Installera anpassade tillägg](./media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

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

1. Klicka på **alla resurser**, och klicka sedan på **myAppGateway**.
2. Klicka på **serverdelspooler**. En standardadresspool skapas automatiskt med programgatewayen. Klicka på **appGateayBackendPool**.
3. Klicka på **Lägg till mål** att lägga till varje virtuell dator som du skapade i serverdelspoolen.

    ![Lägg till backend-servrar](./media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

4. Klicka på **Spara**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Skapa ett lagringskonto och konfigurera diagnostik

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

I den här kursen använder programgatewayen ett lagringskonto för att lagra data för att upptäcka och förhindra syften. Du kan också använda logganalys eller Händelsehubb för att registrera data.

1. Klicka på **ny** hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **Lagring** och sedan **Koppla undernät – blob, fil, tabell, kö**.
3. Ange namnet på lagringskontot, Välj **Använd befintliga** för resursgruppen och välj sedan **myResourceGroupAG**. I det här exemplet är lagringskontonamnet *myagstore1*. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **skapa**.

## <a name="configure-diagnostics"></a>Konfigurera diagnostik

Konfigurera diagnostik för att registrera data i ApplicationGatewayAccessLog och ApplicationGatewayPerformanceLog ApplicationGatewayFirewallLog loggar.

1. I den vänstra menyn klickar du på **alla resurser**, och välj sedan *myAppGateway*.
2. Klicka på under övervakning, **diagnostik loggar**.
3. Klicka på **Lägg till diagnostik inställningen**.
4. Ange *myDiagnosticsSettings* som namn på inställningarna för webbprogramdiagnostik.
5. Välj **arkivet till ett lagringskonto**, och klicka sedan på **konfigurera** att välja den *myagstore1* storage-konto som du skapade tidigare.
6. Välj gateway programloggarna för att samla in och behålla.
7. Klicka på **Spara**.

    ![Konfigurera diagnostik](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Hitta den offentliga IP-adressen för Programgateway på skärmen Översikt. Klicka på **alla resurser** och klicka sedan på **myAGPublicIPAddress**.

    ![Registrera programmet gateway offentlig IP-adress](./media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png)

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

    ![Testa Programgateway](./media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Skapa en Programgateway med Brandvägg aktiverat
> * Skapa de virtuella datorerna som används som backend-servrar
> * Skapa ett lagringskonto och konfigurera diagnostik

Mer information om programgatewayer och deras associerade resurser fortsätter du att artiklarna.