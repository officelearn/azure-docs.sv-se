---
title: 'Självstudiekurs: Konfigurera TLS-avslutning i portalen - Azure Application Gateway'
description: I den här självstudien får du lära dig hur du konfigurerar en programgateway och lägger till ett certifikat för TLS-avslutning med Azure-portalen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: b40eb107fc975d2ef0170944892b936680de3c9f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312379"
---
# <a name="tutorial-configure-an-application-gateway-with-tls-termination-using-the-azure-portal"></a>Självstudiekurs: Konfigurera en programgateway med TLS-avslutning med Azure-portalen

Du kan använda Azure-portalen för att konfigurera en [programgateway](overview.md) med ett certifikat för TLS-avslutning som använder virtuella datorer för serverd-servrar.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett självsignerat certifikat
> * Skapa en programgateway med certifikatet
> * Skapa de virtuella datorer som används som servergruppsservrar
> * Testa programgatewayen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på[https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

I det här avsnittet använder du [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) för att skapa ett självsignerat certifikat. Du överför certifikatet till Azure-portalen när du skapar lyssnaren för programgatewayen.

Öppna ett Windows PowerShell-fönster som administratör på den lokala datorn. Kör följande kommando för att skapa certifikatet:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Du bör se något liknande detta svar:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Använd [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) med tumavtrycket som returnerades för att exportera en pfx-fil från certifikatet:

> [!NOTE]
> Använd inga specialtecken i pfx-fillösenordet. Endast alfanumeriska tecken stöds.

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Skapa en programgateway

1. Välj **Skapa en resurs** på den vänstra menyn på Azure-portalen. Fönstret **Nytt** visas.

2. Välj **Nätverk** och välj sedan **Application Gateway** i listan **Aktuell.**

### <a name="basics-tab"></a>Fliken Grundläggande

1. På fliken **Grunderna** anger du dessa värden för följande inställningar för programgateway:

   - **Resursgrupp**: Välj **myResourceGroupAG** för resursgruppen. Om den inte finns väljer du **Skapa ny** för att skapa den.
   - **Namn på programgateway**: Ange *myAppGateway* för namnet på programgatewayen.

        ![Skapa ny programgateway: Grunderna](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du kan antingen skapa ett nytt virtuellt nätverk eller använda ett befintligt nätverk. I det här exemplet ska du skapa ett nytt virtuellt nätverk samtidigt som du skapar programgatewayen. Application Gateway-instanser skapas i separata undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

    Skapa ett nytt virtuellt nätverk under **Konfigurera virtuellt nätverk**genom att välja **Skapa nytt**. I fönstret **Skapa virtuellt nätverk** som öppnas anger du följande värden för att skapa det virtuella nätverket och två undernät:

    - **Namn**: Ange *myVNet* för namnet på det virtuella nätverket.

    - **Undernätsnamn** (undernät för Programgateway): **Undernätsrutnätet** visar ett undernät med namnet *Standard*. Ändra namnet på det här undernätet till *myAGSubnet*.<br>Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.

    - **Undernätsnamn** (serverdelsundernät): Ange *myBackendSubnet* i kolumnen **Undernätsnamn** på den andra raden i **rutnätet undernät.**

    - **Adressintervall** (serverdelsserverns undernät): I den andra raden i **nätrutnätet** anger du ett adressintervall som inte överlappar adressintervallet *för myAGSubnet*. Om adressintervallet för *myAGSubnet* till exempel är 10.0.0.0/24 anger du *10.0.1.0/24* för adressintervallet för *myBackendSubnet*.

    Välj **OK** om du vill stänga fönstret **Skapa virtuellt nätverk** och spara inställningarna för det virtuella nätverket.

    ![Skapa ny programgateway: virtuellt nätverk](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. På fliken **Grunderna godkänner** du standardvärdena för de andra inställningarna och väljer sedan **Nästa: Frontends**.

### <a name="frontends-tab"></a>Fliken Frontends

1. Kontrollera att **IP-adresstypen Frontend** är offentlig **på**fliken **Frontend.** <br>Du kan konfigurera Frontend IP att vara offentlig eller privat enligt ditt användningsfall. I det här exemplet väljer du en IP-adress med offentliga frontend.
   > [!NOTE]
   > För Application Gateway v2 SKU kan du bara välja **IP-konfiguration** för offentliga frontend. Privat klientdel IP-konfiguration är för närvarande inte aktiverad för den här v2 SKU.

2. Välj **Skapa ny** för den offentliga **IP-adressen** och ange *myAGPublicIPAddress* för det offentliga IP-adressnamnet och välj sedan **OK**. 

   ![Skapa ny programgateway: frontends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Välj **Nästa: Backends**.

### <a name="backends-tab"></a>Fliken Backends

Serverdapoolen används för att dirigera begäranden till serverdaservrar som betjänar begäran. Serverdelspooler kan bestå av nätverkskort, skalningsuppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domännamn (FQDN) och backend-ändar med flera innehavare som Azure App Service. I det här exemplet ska du skapa en tom backend-pool med programgatewayen och sedan lägga till backend-mål i backend-poolen.

1. På fliken **Backends** väljer du **+Lägg till en backend-pool**.

2. I fönstret **Lägg till en backend-pool** som öppnas anger du följande värden för att skapa en tom backend-pool:

    - **Namn**: Ange *myBackendPool* för namnet på backend-poolen.
    - **Lägg till backend pool utan mål**: Välj **Ja** om du vill skapa en backend-pool utan mål. Du ska lägga till backend-mål när du har skapat programgatewayen.

3. I fönstret **Lägg till en serverdpool** väljer du **Lägg till** för att spara serverdapoolkonfigurationen och återgå till fliken **Serveringsfrämda.**

   ![Skapa ny programgateway: backends](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. På fliken **Serveringar** väljer du **Nästa: Konfiguration**.

### <a name="configuration-tab"></a>Fliken Konfiguration

På fliken **Konfiguration** ansluter du klientdels- och serverdelspoolen som du skapade med hjälp av en routningsregel.

1. Välj **Lägg till en regel** i kolumnen **Routningsregler.**

2. I fönstret **Lägg till en routningsregel** som öppnas anger du *myRoutingRule* för **regelnamnet**.

3. En routningsregel kräver en lyssnare. På fliken **Lyssnare** i fönstret **Lägg till en routningsregel** anger du följande värden för lyssnaren:

    - **Lyssnarens namn**: Ange *myListener* för lyssnarens namn.
    - **Frontend IP:** Välj **Offentlig** om du vill välja den offentliga IP-adress som du skapade för klientdelen.
    - **Protokoll**: Välj **HTTPS**.
    - **Port**: Kontrollera 443 har angetts för porten.

   Under **HTTPS-certifikat:**

   - **PFX-certifikatfil** - Bläddra till och välj filen c:\appgwcert.pfx som du skapar tidigare.
   - **Certifikatnamn** - Skriv *mycert1* för namnet på certifikatet.
   - **Lösenord** - Skriv *Azure123456* för lösenordet.
  
        Acceptera standardvärdena för de andra inställningarna på fliken **Lyssnare** och välj sedan fliken **Serveringsmål** för att konfigurera resten av routningsregeln.

   ![Skapa ny programgateway: lyssnare](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. På fliken **Backend-mål** väljer du **myBackendPool** för **backend-målet**.

5. För **HTTP-inställningen**väljer du **Skapa ny** om du vill skapa en ny HTTP-inställning. HTTP-inställningen avgör hur routningsregeln fungerar. I fönstret **Lägg till en HTTP-inställning** som öppnas anger du *minHTTPSetting* för **HTTP-inställningsnamnet**. Acceptera standardvärdena för de andra inställningarna i **fönstret Lägg till en HTTP-inställning** och välj sedan **Lägg** till för att återgå till fönstret Lägg till **en routningsregel.** 

   ![Skapa ny programgateway: HTTP-inställning](./media/create-ssl-portal/application-gateway-create-httpsetting.png)

6. I fönstret **Lägg till en routningsregel** väljer du **Lägg till** för att spara routningsregeln och gå tillbaka till fliken **Konfiguration.**

   ![Skapa ny programgateway: routningsregel](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Välj **Nästa: Taggar** och sedan **Nästa: Granska + skapa**.

### <a name="review--create-tab"></a>Fliken Granska + skapa

Granska inställningarna på fliken **Granska + skapa** och välj sedan **Skapa** för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen. Det kan ta flera minuter för Azure att skapa programgatewayen. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-backend-targets"></a>Lägga till bakåtsträvningsmål

I det här exemplet ska du använda virtuella datorer som målbacksad. Du kan antingen använda befintliga virtuella datorer eller skapa nya. Du skapar två virtuella datorer som Azure använder som serverningsservrar för programgatewayen.

För att göra detta kommer du att:

1. Skapa två nya virtuella datorer, *myVM* och *myVM2,* som ska användas som servergruppsservrar.
2. Installera IIS på de virtuella datorerna för att kontrollera att programgatewayen har skapats.
3. Lägg till serverdservrarna i serverdapoolen.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** på Azure-portalen. Fönstret **Nytt** visas.
2. Välj **Windows Server 2016 Datacenter** i listan **Populär.** Sidan **Skapa en virtuell dator** visas.

   Application Gateway kan dirigera trafik till alla typer av virtuell dator som används i serverdapoolen. I det här exemplet använder du ett Windows Server 2016-datacenter.

1. Ange dessa värden på fliken **Grundläggande inställningar** för följande inställningar för virtuella datorer:

    - **Resursgrupp**: Välj **myResourceGroupAG** för resursgruppsnamnet.
    - **Namn på virtuell dator:** Ange *myVM* för namnet på den virtuella datorn.
    - **Användarnamn**: Ange *azureuser* för administratörens användarnamn.
    - **Lösenord**: Ange *Azure123456* för administratörslösenordet.
4. Acceptera de andra standardinställningarna och välj sedan **Nästa: Diskar**.  
5. Acceptera **standardvärdena för diskfliken** och välj sedan **Nästa: Nätverk**.
6. På fliken **Nätverk** kontrollerar du att **myVNet** har valts för **Virtuellt nätverk** och att **Undernät** är inställt på **myBackendSubnet**. Acceptera de andra standardinställningarna och välj sedan **Nästa: Hantering**.

   Application Gateway kan kommunicera med instanser utanför det virtuella nätverket som den finns i, men du måste se till att det finns IP-anslutning.
1. På fliken **Hantering** anger du **Startdiagnostik** till **Av**. Acceptera de övriga standardinställningarna och välj sedan **Granska + skapa**.
2. Gå igenom inställningarna på fliken **Granska + skapa** och åtgärda eventuella verifieringsfel och välj sedan **Skapa**.
3. Vänta tills distributionen har slutförts innan du fortsätter.

### <a name="install-iis-for-testing"></a>Installera IIS för testning

I det här exemplet installerar du IIS på de virtuella datorerna bara för att verifiera att Azure har skapat programgatewayen.

1. Öppna [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Det gör du genom att först välja **Cloud Shell** i det övre navigeringsfältet på Azure-portalen och sedan välja **PowerShell** i listrutan. 

    ![Installera anpassat tillägg](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

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

3. Skapa en andra virtuell dator och installera IIS genom att följa stegen som du utförde tidigare. Använd *myVM2* för namnet på den virtuella datorn och för **VMName-inställningen** för **cmdlet set-azvmextension.**

### <a name="add-backend-servers-to-backend-pool"></a>Lägga till serverdserver i serverdapoolen

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **Serverdelspooler** på den vänstra menyn.

3. Välj **myBackendPool**.

4. Välj **Virtuell dator** i listrutan under **Mål**.

5. Under **VIRTUELL DATOR** och **NÄTVERKSGRÄNSSNITT** väljer du de virtuella datorerna **myVM** och **myVM2** och deras associerade nätverksgränssnitt i listrutorna.

    ![Lägga till serverdelsservrar](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Välj **Spara**.

7. Vänta tills distributionen har slutförts innan du fortsätter till nästa steg.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Välj **Alla resurser**och välj sedan **myAGPublicIPAddress**.

    ![Registrera programgatewayens offentliga IP-adress](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Skriv *https:// ip-adressen\<\>för din programgateway*i webbläsarens adress.

   Om du vill acceptera säkerhetsvarningen om du har använt ett självsignerat certifikat väljer du **Information** (eller **Avancerat** i Chrome) och går sedan vidare till webbsidan:

    ![Säkerhetsvarning](./media/create-ssl-portal/application-gateway-secure.png)

    Din skyddade IIS-webbplats visas sedan som i exemplet nedan:

    ![Testa basadressen i programgatewayen](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om TLS-stöd för Application Gateway](ssl-overview.md)
