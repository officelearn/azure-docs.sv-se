---
title: 'Snabbstart: Direkt webbtrafik med hjälp av portalen'
titleSuffix: Azure Application Gateway
description: Lär dig hur du använder Azure-portalen för att skapa en Azure Application Gateway som dirigerar webbtrafik till virtuella datorer i en serverdelspool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/09/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 17adc800bd5a2ae53e27350c7e0d588eaeee4a8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241404"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure Portal

I den här snabbstarten använder du Azure-portalen för att skapa en programgateway. Då du testa den för att se till att det fungerar korrekt. 

Programgatewayen dirigerar programwebbtrafik till specifika resurser i en backend-pool. Du kan tilldela lyssnare till portar, skapa regler och lägga till resurser i en serverdelspool. För enkelhetens skull använder den här artikeln en enkel installation med en offentlig klientdator, en grundläggande lyssnare som är värd för en enda plats i programgatewayen, en grundläggande routningsregel för begäran och två virtuella datorer i serverdelspoolen.

Du kan också slutföra den här snabbstarten med [Azure PowerShell](quick-create-powershell.md) eller [Azure CLI](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]



## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Du ska skapa programgatewayen med hjälp av flikarna på sidan **Skapa en programgateway.**

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**. Fönstret **Nytt** visas.

2. Välj **Nätverk** och välj sedan **Application Gateway** i listan **Aktuell.**

### <a name="basics-tab"></a>Fliken Grundläggande

1. På fliken **Grunderna** anger du dessa värden för följande inställningar för programgateway:

   - **Resursgrupp**: Välj **myResourceGroupAG** för resursgruppen. Om den inte finns väljer du **Skapa ny** för att skapa den.
   - **Namn på programgateway**: Ange *myAppGateway* för namnet på programgatewayen.

     ![Skapa ny programgateway: Grunderna](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du kan antingen skapa ett nytt virtuellt nätverk eller använda ett befintligt nätverk. I det här exemplet ska du skapa ett nytt virtuellt nätverk samtidigt som du skapar programgatewayen. Application Gateway-instanser skapas i separata undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

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
   > För Application Gateway v2 SKU måste det finnas en **offentlig** IP-konfiguration för klientdelen. Du kan fortfarande ha både en offentlig och en privat klientdel IP-konfiguration, men privat endast klientdel IP-konfiguration (Endast ILB-läge) är för närvarande inte aktiverat för v2 SKU. 

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
  
      Acceptera standardvärdena för de andra inställningarna på fliken **Lyssnare** och välj sedan fliken **Serveringsmål** för att konfigurera resten av routningsregeln.

   ![Skapa ny programgateway: lyssnare](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. På fliken **Backend-mål** väljer du **myBackendPool** för **backend-målet**.

5. För **HTTP-inställningen**väljer du **Skapa ny** om du vill skapa en ny HTTP-inställning. HTTP-inställningen avgör hur routningsregeln fungerar. I fönstret **Lägg till en HTTP-inställning** som öppnas anger du *minHTTPSetting* för **HTTP-inställningsnamnet** och *80* för **backend-porten**. Acceptera standardvärdena för de andra inställningarna i **fönstret Lägg till en HTTP-inställning** och välj sedan **Lägg** till för att återgå till fönstret Lägg till **en routningsregel.** 

     ![Skapa ny programgateway: HTTP-inställning](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. I fönstret **Lägg till en routningsregel** väljer du **Lägg till** för att spara routningsregeln och gå tillbaka till fliken **Konfiguration.**

     ![Skapa ny programgateway: routningsregel](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Välj **Nästa: Taggar** och sedan **Nästa: Granska + skapa**.

### <a name="review--create-tab"></a>Fliken Granska + skapa

Granska inställningarna på fliken **Granska + skapa** och välj sedan **Skapa** för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen. Det kan ta flera minuter för Azure att skapa programgatewayen. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-backend-targets"></a>Lägga till bakåtsträvningsmål

I det här exemplet ska du använda virtuella datorer som målbacksad. Du kan antingen använda befintliga virtuella datorer eller skapa nya. Du skapar två virtuella datorer som servergruppsservrar för programgatewayen.

För att göra detta kommer du att:

1. Skapa två nya virtuella datorer, *myVM* och *myVM2,* som ska användas som servergruppsservrar.
2. Installera IIS på de virtuella datorerna för att kontrollera att programgatewayen har skapats.
3. Lägg till serverdservrarna i serverdapoolen.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**. Fönstret **Nytt** visas.
2. Välj **Windows Server 2016 Datacenter** i listan **Populär.** Sidan **Skapa en virtuell dator** visas.<br>Application Gateway kan dirigera trafik till alla typer av virtuell dator som används i serverdapoolen. I det här exemplet använder du ett Windows Server 2016-datacenter.
3. Ange dessa värden på fliken **Grundläggande inställningar** för följande inställningar för virtuella datorer:

    - **Resursgrupp**: Välj **myResourceGroupAG** för resursgruppsnamnet.
    - **Namn på virtuell dator:** Ange *myVM* för namnet på den virtuella datorn.
    - **Region**: Välj samma region där du skapade programgatewayen.
    - **Användarnamn**: Skriv *azureuser* för administratörens användarnamn.
    - **Lösenord**: Skriv ett lösenord.
4. Acceptera de andra standardinställningarna och välj sedan **Nästa: Diskar**.  
5. Acceptera **standardvärdena för diskfliken** och välj sedan **Nästa: Nätverk**.
6. På fliken **Nätverk** kontrollerar du att **myVNet** har valts för **Virtuellt nätverk** och att **Undernät** är inställt på **myBackendSubnet**. Acceptera de andra standardinställningarna och välj sedan **Nästa: Hantering**.<br>Application Gateway kan kommunicera med instanser utanför det virtuella nätverket som den finns i, men du måste se till att det finns IP-anslutning.
7. På fliken **Hantering** anger du **Startdiagnostik** till **Av**. Acceptera de övriga standardinställningarna och välj sedan **Granska + skapa**.
8. Gå igenom inställningarna på fliken **Granska + skapa** och åtgärda eventuella verifieringsfel och välj sedan **Skapa**.
9. Vänta på att skapandet av den virtuella datorn är klart innan du fortsätter.

### <a name="install-iis-for-testing"></a>Installera IIS för testning

I det här exemplet installerar du IIS på de virtuella datorerna bara för att verifiera att Azure har skapat programgatewayen.

1. Öppna Azure PowerShell. Välj **Cloud Shell** i det övre navigeringsfältet i Azure-portalen och välj sedan **PowerShell** i listrutan. 

    ![Installera anpassat tillägg](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Kör följande kommando för att installera IIS på den virtuella datorn. Ändra parametern *Plats* om det behövs: 

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

1. På Portal-menyn i Azure väljer du **Alla resurser** eller söker efter och väljer *Alla resurser*. Välj sedan **myAppGateway**.

2. Välj **Serverdelspooler** på den vänstra menyn.

3. Välj **myBackendPool**.

4. Under **Bakåtsidadmål**väljer du **Måltyp**, **Virtuell dator** i listrutan.

5. Under **Mål**väljer du **virtuella myVM-** och **myVM2-datorer** och tillhörande nätverksgränssnitt från listrutorna.


   > [!div class="mx-imgBorder"]
   > ![Lägga till serverdelsservrar](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Välj **Spara**.

7. Vänta tills distributionen har slutförts innan du fortsätter till nästa steg.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Även om IIS inte krävs för att skapa programgatewayen, installerade du den i den här snabbstarten för att verifiera om Azure har skapat programgatewayen. Använd IIS för att testa programgatewayen:

1. Hitta den offentliga IP-adressen för programgatewayen på sidan **Översikt.** ![Registrera den offentliga](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) IP-adressen för programgateway Eller så kan du välja **Alla resurser,** ange *myAGPublicIPAddress* i sökrutan och sedan markera den i sökresultaten. Azure visar den offentliga IP-adressen på sidan **Översikt**.
2. Kopiera den offentliga IP-adressen och klistra sedan in den i webbläsarens adressfält för att bläddra i den IP-adressen.
3. Kontrollera svaret. Ett giltigt svar verifierar att programgatewayen har skapats och kan ansluta till backend.

   ![Testa programgatewayen](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Uppdatera webbläsaren flera gånger och du bör se anslutningar till både myVM och myVM2.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen tar du bort resursgruppen. När du tar bort resursgruppen tar du också bort programgatewayen och alla relaterade resurser.

Så här tar du bort resursgruppen:

1. På Azure-portalmenyn väljer du **Resursgrupper** eller söker efter och väljer *Resursgrupper*.
2. På sidan **Resursgrupper** söker du efter och väljer **myResourceGroupAG**.
3. Välj **Ta bort resursgrupp** på sidan **Resursgrupp**.
4. Ange *myResourceGroupAG* för **Skriv resursgruppens namn** och välj sedan **Ta bort**

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway som använder Azure CLI](./tutorial-manage-web-traffic-cli.md)
