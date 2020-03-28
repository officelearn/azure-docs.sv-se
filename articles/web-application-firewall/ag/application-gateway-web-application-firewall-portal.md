---
title: 'Självstudiekurs: Skapa med hjälp av portal - Brandvägg för webbprogram'
description: I den här självstudien får du lära dig hur du skapar en programgateway med en brandvägg för webbprogram med hjälp av Azure-portalen.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 68a9f051bf3d59cbf32377cb503e9ded0a54d5e9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74049226"
---
# <a name="tutorial-create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Självstudiekurs: Skapa en programgateway med en brandvägg för webbprogram med Azure-portalen

Den här självstudien visar hur du använder Azure-portalen för att skapa en programgateway med en brandvägg för webbprogram (WAF). I brandväggen används [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-regler till att skydda programmet. De här reglerna kan exempelvis skydda mot attacker som SQL-inmatning, skriptattacker mellan webbplatser och sessionskapningar. När du har skapat programgatewayen testar du den för att se till att den fungerar som den ska. Med Azure Application Gateway dirigerar du programwebbtrafiken till specifika resurser genom att tilldela lyssnare till portar, skapa regler och lägga till resurser i en serverdelspool. Av hänsyn till enkelhet använder den här självstudien en enkel installation med en offentlig klientdator, en grundläggande lyssnare som är värd för en enda plats på den här programgatewayen, två virtuella datorer som används för serverdelspoolen och en grundläggande routningsregel för begäran.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en programgateway med WAF aktiverat
> * Skapa de virtuella datorer som används som servergruppsservrar
> * Skapa ett lagringskonto och konfigurera diagnostik
> * Testa programgatewayen

![Exempel på brandvägg för webbaserade program](../media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!---If you prefer, you can complete this tutorial using [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) or [Azure CLI](tutorial-restrict-web-traffic-cli.md).--->

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Skapa en programgateway

För att Azure ska kunna kommunicera mellan resurser behöver det ett virtuellt nätverk. Du kan antingen skapa ett nytt virtuellt nätverk eller använda ett befintligt nätverk. I det här exemplet skapar du ett nytt virtuellt nätverk. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen. Application Gateway-instanser skapas i separata undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

Välj **Skapa en resurs** på den vänstra menyn på Azure-portalen. Fönstret **Nytt** visas.

Välj **Nätverk** och välj sedan **Application Gateway** i listan **Aktuell.**

### <a name="basics-tab"></a>Fliken Grundläggande

1. På fliken **Grunderna** anger du dessa värden för följande inställningar för programgateway:

   - **Resursgrupp**: Välj **myResourceGroupAG** för resursgruppen. Om den inte finns väljer du **Skapa ny** för att skapa den.
   - **Namn på programgateway**: Ange *myAppGateway* för namnet på programgatewayen.
   - **Nivå:** välj **WAF V2**.

     ![Skapa ny programgateway: Grunderna](../media/application-gateway-web-application-firewall-portal/application-gateway-create-basics.png)

2.  För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du kan antingen skapa ett nytt virtuellt nätverk eller använda ett befintligt nätverk. I det här exemplet ska du skapa ett nytt virtuellt nätverk samtidigt som du skapar programgatewayen. Application Gateway-instanser skapas i separata undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

    Skapa ett nytt virtuellt nätverk under **Konfigurera virtuellt nätverk**genom att välja **Skapa nytt**. I fönstret **Skapa virtuellt nätverk** som öppnas anger du följande värden för att skapa det virtuella nätverket och två undernät:

    - **Namn**: Ange *myVNet* för namnet på det virtuella nätverket.

    - **Undernätsnamn** (undernät för Programgateway): **Undernätsrutnätet** visar ett undernät med namnet *Standard*. Ändra namnet på det här undernätet till *myAGSubnet*.<br>Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.

    - **Undernätsnamn** (serverdelsundernät): Ange *myBackendSubnet* i kolumnen **Undernätsnamn** på den andra raden i **rutnätet undernät.**

    - **Adressintervall** (serverdelsserverns undernät): I den andra raden i **nätrutnätet** anger du ett adressintervall som inte överlappar adressintervallet *för myAGSubnet*. Om adressintervallet för *myAGSubnet* till exempel är 10.0.0.0/24 anger du *10.0.1.0/24* för adressintervallet för *myBackendSubnet*.

    Välj **OK** om du vill stänga fönstret **Skapa virtuellt nätverk** och spara inställningarna för det virtuella nätverket.

     ![Skapa ny programgateway: virtuellt nätverk](../media/application-gateway-web-application-firewall-portal/application-gateway-create-vnet.png)
    
3. På fliken **Grunderna godkänner** du standardvärdena för de andra inställningarna och väljer sedan **Nästa: Frontends**.

### <a name="frontends-tab"></a>Fliken Frontends

1. Kontrollera att **IP-adresstypen Frontend** är offentlig **på**fliken **Frontend.** <br>Du kan konfigurera Frontend IP att vara offentlig eller privat enligt ditt användningsfall. I det här exemplet väljer du en IP-adress med offentliga frontend.
   > [!NOTE]
   > För Application Gateway v2 SKU kan du bara välja **IP-konfiguration** för offentliga frontend. Privat klientdel IP-konfiguration är för närvarande inte aktiverad för den här v2 SKU.

2. Välj **Skapa ny** för den offentliga **IP-adressen** och ange *myAGPublicIPAddress* för det offentliga IP-adressnamnet och välj sedan **OK**. 

     ![Skapa ny programgateway: frontends](../media/application-gateway-web-application-firewall-portal/application-gateway-create-frontends.png)

3. Välj **Nästa: Backends**.

### <a name="backends-tab"></a>Fliken Backends

Serverdapoolen används för att dirigera begäranden till serverdaservrar som betjänar begäran. Serverdelspooler kan bestå av nätverkskort, skalningsuppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domännamn (FQDN) och backend-ändar med flera innehavare som Azure App Service. I det här exemplet ska du skapa en tom backend-pool med programgatewayen och sedan lägga till backend-mål i backend-poolen.

1. På fliken **Backends** väljer du **+Lägg till en backend-pool**.

2. I fönstret **Lägg till en backend-pool** som öppnas anger du följande värden för att skapa en tom backend-pool:

    - **Namn**: Ange *myBackendPool* för namnet på backend-poolen.
    - **Lägg till backend pool utan mål**: Välj **Ja** om du vill skapa en backend-pool utan mål. Du ska lägga till backend-mål när du har skapat programgatewayen.

3. I fönstret **Lägg till en serverdpool** väljer du **Lägg till** för att spara serverdapoolkonfigurationen och återgå till fliken **Serveringsfrämda.**

     ![Skapa ny programgateway: backends](../media/application-gateway-web-application-firewall-portal/application-gateway-create-backends.png)

4. På fliken **Serveringar** väljer du **Nästa: Konfiguration**.

### <a name="configuration-tab"></a>Fliken Konfiguration

På fliken **Konfiguration** ansluter du klientdels- och serverdelspoolen som du skapade med hjälp av en routningsregel.

1. Välj **Lägg till en regel** i kolumnen **Routningsregler.**

2. I fönstret **Lägg till en routningsregel** som öppnas anger du *myRoutingRule* för **regelnamnet**.

3. En routningsregel kräver en lyssnare. På fliken **Lyssnare** i fönstret **Lägg till en routningsregel** anger du följande värden för lyssnaren:

    - **Lyssnarens namn**: Ange *myListener* för lyssnarens namn.
    - **Frontend IP:** Välj **Offentlig** om du vill välja den offentliga IP-adress som du skapade för klientdelen.
  
      Acceptera standardvärdena för de andra inställningarna på fliken **Lyssnare** och välj sedan fliken **Serveringsmål** för att konfigurera resten av routningsregeln.

   ![Skapa ny programgateway: lyssnare](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-listener.png)

4. På fliken **Backend-mål** väljer du **myBackendPool** för **backend-målet**.

5. För **HTTP-inställningen**väljer du **Skapa ny** om du vill skapa en ny HTTP-inställning. HTTP-inställningen avgör hur routningsregeln fungerar. I fönstret **Lägg till en HTTP-inställning** som öppnas anger du *minHTTPSetting* för **HTTP-inställningsnamnet**. Acceptera standardvärdena för de andra inställningarna i **fönstret Lägg till en HTTP-inställning** och välj sedan **Lägg** till för att återgå till fönstret Lägg till **en routningsregel.** 

     ![Skapa ny programgateway: HTTP-inställning](../media/application-gateway-web-application-firewall-portal/application-gateway-create-httpsetting.png)

6. I fönstret **Lägg till en routningsregel** väljer du **Lägg till** för att spara routningsregeln och gå tillbaka till fliken **Konfiguration.**

     ![Skapa ny programgateway: routningsregel](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-backends.png)

7. Välj **Nästa: Taggar** och sedan **Nästa: Granska + skapa**.

### <a name="review--create-tab"></a>Fliken Granska + skapa

Granska inställningarna på fliken **Granska + skapa** och välj sedan **Skapa** för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen. Det kan ta flera minuter för Azure att skapa programgatewayen. 

Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-backend-targets"></a>Lägga till bakåtsträvningsmål

I det här exemplet ska du använda virtuella datorer som målbacksad. Du kan antingen använda befintliga virtuella datorer eller skapa nya. Du skapar två virtuella datorer som Azure använder som serverningsservrar för programgatewayen.

För att göra detta kommer du att:

1. Skapa två nya virtuella datorer, *myVM* och *myVM2,* som ska användas som servergruppsservrar.
2. Installera IIS på de virtuella datorerna för att kontrollera att programgatewayen har skapats.
3. Lägg till serverdservrarna i serverdapoolen.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** på Azure-portalen. Fönstret **Nytt** visas.
2. Välj **Windows Server 2016 Datacenter** i listan **Populär.** Sidan **Skapa en virtuell dator** visas.<br>Application Gateway kan dirigera trafik till alla typer av virtuell dator som används i serverdapoolen. I det här exemplet använder du ett Windows Server 2016-datacenter.
3. Ange dessa värden på fliken **Grundläggande inställningar** för följande inställningar för virtuella datorer:

    - **Resursgrupp**: Välj **myResourceGroupAG** för resursgruppsnamnet.
    - **Namn på virtuell dator:** Ange *myVM* för namnet på den virtuella datorn.
    - **Användarnamn**: Ange *azureuser* för administratörens användarnamn.
    - **Lösenord**: Ange *Azure123456!* som administratörslösenord.
4. Acceptera de andra standardinställningarna och välj sedan **Nästa: Diskar**.  
5. Acceptera **standardvärdena för diskfliken** och välj sedan **Nästa: Nätverk**.
6. På fliken **Nätverk** kontrollerar du att **myVNet** har valts för **Virtuellt nätverk** och att **Undernät** är inställt på **myBackendSubnet**. Acceptera de andra standardinställningarna och välj sedan **Nästa: Hantering**.<br>Application Gateway kan kommunicera med instanser utanför det virtuella nätverket som den finns i, men du måste se till att det finns IP-anslutning.
7. På fliken **Hantering** anger du **Startdiagnostik** till **Av**. Acceptera de övriga standardinställningarna och välj sedan **Granska + skapa**.
8. Gå igenom inställningarna på fliken **Granska + skapa** och åtgärda eventuella verifieringsfel och välj sedan **Skapa**.
9. Vänta på att skapandet av den virtuella datorn är klart innan du fortsätter.

### <a name="install-iis-for-testing"></a>Installera IIS för testning

I det här exemplet installerar du IIS på de virtuella datorerna bara för att verifiera att Azure har skapat programgatewayen.

1. Öppna [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Det gör du genom att först välja **Cloud Shell** i det övre navigeringsfältet på Azure-portalen och sedan välja **PowerShell** i listrutan. 

    ![Installera anpassat tillägg](../media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

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

    ![Lägga till serverdelsservrar](../media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

6. Välj **Spara**.

7. Vänta tills distributionen har slutförts innan du fortsätter till nästa steg.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Skapa ett lagringskonto och konfigurera diagnostik

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

I den här artikeln använder programgatewayen ett lagringskonto för att lagra data för identifierings- och förebyggande ändamål. Du kan även använda Azure Monitor-loggar eller Event Hub till att registrera data.

1. Välj **Skapa en resurs** i det övre vänstra hörnet av Azure-portalen.
1. Välj **Lagring**och välj sedan **Lagringskonto**.
1. För *resursgrupp*väljer du **myResourceGroupAG** för resursgruppen.
1. Skriv *myagstore1* för namnet på lagringskontot.
1. Acceptera standardvärdena för de andra inställningarna och välj sedan **Granska + Skapa**.
1. Granska inställningarna och välj sedan **Skapa**.

### <a name="configure-diagnostics"></a>Konfigurera diagnostiken

Konfigurera diagnostik för registrering av data i loggarna ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog och ApplicationGatewayFirewallLog.

1. I menyn till vänster väljer du **Alla resurser**och väljer sedan *myAppGateway*.
2. Under Övervakning väljer du **Diagnostikinställningar**.
3. välj **Lägg till diagnostikinställning**.
4. Ange *myDiagnosticsSettings* som namn för diagnostikinställningarna.
5. Välj **Arkiv till ett lagringskonto**och välj sedan **Konfigurera** för att välja det *myagstore1-lagringskonto* som du tidigare skapade och välj sedan **OK**.
6. Välj de programgatewayloggar som du vill samla in och behålla.
7. Välj **Spara**.

    ![Konfigurera diagnostiken](../media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="create-and-link-a-web-application-firewall-policy"></a>Skapa och länka en brandvägg för webbprogram

Alla WAF-anpassningar och inställningar finns i ett separat objekt, som kallas WAF-princip. Principen måste vara associerad med programgatewayen. En WAF-princip finns i [Skapa en WAF-princip](create-waf-policy-ag.md). När den har skapats kan du sedan associera principen till din WAF (eller en enskild lyssnare) från WAF-principen på fliken **Associerade programgateways.** 

![Associerade programgateways](../media/application-gateway-web-application-firewall-portal/associated-application-gateways.png)

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Även om IIS inte krävs för att skapa programgatewayen, installerade du den för att kontrollera om Azure har skapat programgatewayen. Använd IIS för att testa programgatewayen:

1. Hitta den offentliga IP-adressen för programgatewayen på sidan **Översikt.** ![Registrera offentlig IP-adress för programgateway](../media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png) 

   Du kan också välja **Alla resurser,** ange *myAGPublicIPAddress* i sökrutan och sedan markera den i sökresultaten. Azure visar den offentliga IP-adressen på sidan **Översikt**.
1. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.
1. Kontrollera svaret. Ett giltigt svar verifierar att programgatewayen har skapats och att den kan ansluta till backend.

   ![Testa programgatewayen](../media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen kan du ta bort resursgruppen. När du tar bort resursgruppen tas även programgatewayen och alla dess relaterade resurser bort. 

Så här tar du bort resursgruppen:

1. Välj **Resursgrupper** på den vänstra menyn på Azure-portalen.
2. På sidan **Resursgrupper** söker du efter och väljer **myResourceGroupAG**.
3. Välj **Ta bort resursgrupp** på sidan **Resursgrupp**.
4. Ange *myResourceGroupAG* för **TYP RESURSGRUPPNAMN** och välj sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om brandvägg för webbprogram](../overview.md)
