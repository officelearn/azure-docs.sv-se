---
title: Konfigurera en Programgateway med SSL-avslutning - Azure-portalen | Microsoft Docs
description: Lär dig hur du konfigurerar en Programgateway och lägga till ett certifikat för SSL-avslutning med Azure-portalen.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.date: 5/15/2018
ms.author: victorh
ms.openlocfilehash: c64754595ef67b7c083ee8d47da5b412467c191b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195921"
---
# <a name="configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Konfigurera en Programgateway med SSL-avslutning med Azure-portalen

Du kan använda Azure-portalen för att konfigurera en [Programgateway](overview.md) med ett certifikat för SSL-avslutning som använder virtuella datorer för backend-servrar.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa ett självsignerat certifikat
> * Skapa en Programgateway med certifikatet
> * Skapa de virtuella datorerna som används som backend-servrar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure portal [http://portal.azure.com](http://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

I det här avsnittet kan du använda [ny SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) att skapa ett självsignerat certifikat som du överför till Azure-portalen när du skapar en lyssnare för programgatewayen.

Öppna en Windows PowerShell-fönster som administratör på den lokala datorn. Kör följande kommando för att skapa certifikatet:

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

Du bör se något som liknar detta svar:

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

Ett virtuellt nätverk behövs för kommunikation mellan resurser som du skapar. Två undernät skapas i det här exemplet: en för programgatewayen och en för backend-servrarna. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen.

1. Klicka på **ny** hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **nätverk** och välj sedan **Programgateway** i listan över aktuella.
3. Ange *myAppGateway* för namnet på programgatewayen och *myResourceGroupAG* för den nya resursgruppen.
4. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
5. Klicka på **Välj ett virtuellt nätverk**, klickar du på **Skapa nytt**, och ange sedan värdena för det virtuella nätverket:

    - *myVNet* – namnet på det virtuella nätverket.
    - *10.0.0.0/16* - för virtuella nätverkets adressutrymme.
    - *myAGSubnet* - för undernätsnamnet.
    - *10.0.0.0/24* - för undernätsadressutrymmet.

    ![Skapa det virtuella nätverket](./media/create-ssl-portal/application-gateway-vnet.png)

6. Klicka på **OK** att skapa virtuella nätverk och undernät.
7. Klicka på **Välj en offentlig IP-adress**, klickar du på **Skapa nytt**, och ange sedan namnet på den offentliga IP-adressen. I det här exemplet heter den offentliga IP-adressen *myAGPublicIPAddress*. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
8. Klicka på **HTTPS** för protokollet för lyssnaren och se till att porten som har definierats som **443**.
9. Klicka på mappikonen och bläddra till den *appgwcert.pfx* certifikat som du tidigare skapade för att ladda upp den.
10. Ange *mycert1* för namnet på certifikatet och *Azure123456!* lösenord och klicka sedan på **OK**.

    ![Skapa nya Programgateway](./media/create-ssl-portal/application-gateway-create.png)

11. Granska inställningarna på sidan Sammanfattning och klicka sedan på **OK** skapa nätverksresurser och programgatewayen. Det kan ta flera minuter för Programgateway skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="add-a-subnet"></a>Lägg till ett undernät

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på **myVNet** från resurslistan över.
2. Klicka på **undernät**, och klicka sedan på **undernät**.

    ![Skapa undernät](./media/create-ssl-portal/application-gateway-subnet.png)

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

    ![Installera anpassade tillägg](./media/create-ssl-portal/application-gateway-extension.png)

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
4. Klicka på **serverdelspooler**. En standardadresspool skapas automatiskt med programgatewayen. Klicka på **appGateayBackendPool**.
5. Klicka på **Lägg till mål** att lägga till varje virtuell dator som du skapade i serverdelspoolen.

    ![Lägg till backend-servrar](./media/create-ssl-portal/application-gateway-backend.png)

6. Klicka på **Spara**.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Klicka på **alla resurser**, och klicka sedan på **myAGPublicIPAddress**.

    ![Registrera programmet gateway offentlig IP-adress](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Acceptera säkerhetsvarningen om du använder ett självsignerat certifikat, Välj information och fortsätter sedan till webbsidan:

    ![Säker varning](./media/create-ssl-portal/application-gateway-secure.png)

    Din skyddade IIS-webbplats visas sedan som i exemplet nedan:

    ![Testa bas-URL i Programgateway](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett självsignerat certifikat
> * Skapa en Programgateway med certifikatet
> * Skapa de virtuella datorerna som används som backend-servrar

Mer information om programgatewayer och deras associerade resurser fortsätter du att artiklarna.