---
title: Självstudie – konfigurera en Programgateway med SSL-avslutning – Azure-portalen
description: I den här självstudien får du lära dig hur du konfigurerar en Programgateway och lägga till ett certifikat för SSL-avslutning med Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/17/2019
ms.author: victorh
ms.openlocfilehash: f3ba3eb12dc85a72c4e49c374e62209b83400d33
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677858"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Självstudier: Konfigurera en Programgateway med SSL-avslutning med Azure portal

Du kan använda Azure-portalen för att konfigurera en [Programgateway](overview.md) med ett certifikat för SSL-avslutning som använder virtuella datorer för backend-servrar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett självsignerat certifikat
> * Skapa en programgateway med certifikatet
> * Skapa de virtuella datorerna som används som backend-servrar
> * Testa programgatewayen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

I det här avsnittet ska du använda [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) att skapa ett självsignerat certifikat. Överför certifikatet till Azure-portalen när du skapar lyssnaren för application gateway.

Öppna ett Windows PowerShell-fönster som administratör på den lokala datorn. Kör följande kommando för att skapa certifikatet:

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

Du bör se något som liknar det här svaret:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Det krävs ett virtuellt nätverk för kommunikation mellan de resurser som du skapar. I det här exemplet skapas två undernät: ett för programgatewayen och ett annat för serverdelen. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen.

1. Välj **New** hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **Nätverk** och sedan **Application Gateway** i listan Aktuella.
3. Ange *myAppGateway* för namnet på application gateway och *myResourceGroupAG* för den nya resursgruppen.
4. Godkänn standardvärdena för de andra inställningarna och välj sedan **OK**.
5. Välj **Välj ett virtuellt nätverk**väljer **Skapa nytt**, och ange följande värden för det virtuella nätverket:

   - *myVnet* – Det virtuella nätverkets namn.
   - *10.0.0.0/16* – Det virtuella nätverkets adressutrymme.
   - *myBackendSubnet* – Undernätsnamnet.
   - *10.0.0.0/24* – Undernätets adressutrymme.

     ![Skapa det virtuella nätverket](./media/create-ssl-portal/application-gateway-vnet.png)

6. Välj **OK** att skapa virtuellt nätverk och undernät.
7. Välj **välja en offentlig IP-adress**väljer **Skapa nytt**, och ange sedan namnet på den offentliga IP-adressen. I det här exemplet heter den offentliga IP-adressen *myAGPublicIPAddress*. Godkänn standardvärdena för de andra inställningarna och välj sedan **OK**.
8. Välj **HTTPS** för protokollet för lyssnare och se till att porten som har definierats som **443**.
9. Välj mappikonen och bläddra till den *appgwcert.pfx* certifikat som du skapade tidigare för att ladda upp den.
10. Ange *mycert1* för namnet på certifikatet och *Azure123456!* för lösenord och välj sedan **OK**.

    ![Skapa en ny programgateway](./media/create-ssl-portal/application-gateway-create.png)

11. Granska inställningarna på sidan Sammanfattning och välj sedan **OK** att skapa nätverksresurser och application gateway. Det kan ta flera minuter för application gateway kan skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="add-a-subnet"></a>Lägga till ett undernät

1. Välj **alla resurser** i den vänstra menyn och välj sedan **myVNet** i resurslistan.
2. Välj **undernät**, och välj sedan **undernät**.

    ![Skapa undernät](./media/create-ssl-portal/application-gateway-subnet.png)

3. Ange *myBackendSubnet* för namnet på undernätet och välj sedan **OK**.

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här exemplet skapar du två virtuella datorer användas som serverdelsservrar för application gateway. Du kan också installera IIS på de virtuella datorerna för att verifiera application gateway har skapats.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Ny**.
2. Välj **Compute** och sedan **Windows Server 2016 Datacenter** i listan Aktuella.
3. Ange följande värden för den virtuella datorn:

    - *myVM* – Namnet på den virtuella datorn.
    - *azureuser* – för administratörens användarnamn.
    - *Azure123456!* som lösenord.
    - Välj **Använd befintlig** och sedan *myResourceGroupAG*.

4. Välj **OK**.
5. Välj **DS1_V2** för storleken på den virtuella datorn och välj **Välj**.
6. Kontrollera att **myVNet** har valts för det virtuella nätverket och att undernätet är **myBackendSubnet**. 
7. Inaktivera startdiagnostikinställningar genom att välja **Inaktiverad**.
8. Välj **OK**, granska inställningarna på sammanfattningssidan och välj sedan **Skapa**.

### <a name="install-iis"></a>Installera IIS

1. Öppna det interaktiva gränssnittet och kontrollera att det är inställt på **PowerShell**.

    ![Installera anpassat tillägg](./media/create-ssl-portal/application-gateway-extension.png)

2. Kör följande kommando för att installera IIS på den virtuella datorn: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Skapa en andra virtuell dator och installera IIS med hjälp av de steg som du just har slutfört. Ange *myVM2* för dess namn och VMName i Set-AzVMExtension.

### <a name="add-backend-servers"></a>Lägga till serverdelsservrar

1. Välj **Alla resurser** och välj sedan **myAppGateway**.
1. Välj **serverdelspooler**. En standardpool skapades automatiskt med programgatewayen. Välj **appGatewayBackendPool**.
1. Välj **Lägg till mål** att lägga till varje virtuell dator som du skapade till serverdelspoolen.

    ![Lägga till serverdelsservrar](./media/create-ssl-portal/application-gateway-backend.png)

1. Välj **Spara**.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Välj **alla resurser**, och välj sedan **myAGPublicIPAddress**.

    ![Registrera programgatewayens offentliga IP-adress](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Välj information för att acceptera säkerhetsvarningen om du använde ett självsignerat certifikat, och sedan går du vidare till webbsidan:

    ![Säkerhetsvarning](./media/create-ssl-portal/application-gateway-secure.png)

    Din skyddade IIS-webbplats visas sedan som i exemplet nedan:

    ![Testa basadressen i programgatewayen](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mer information om vad du kan göra med Azure Application Gateway](application-gateway-introduction.md)
