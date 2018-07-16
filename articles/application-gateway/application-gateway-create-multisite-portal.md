---
title: Skapa en Programgateway med flera webbplatser – Azure-portalen | Microsoft Docs
description: Lär dig hur du skapar en Programgateway som är värd för flera platser med hjälp av Azure-portalen.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 84f88c40d8fe1d88e9d85da5ef6c9e7992b33cc4
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049906"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-portal"></a>Skapa en Programgateway med flera för webbplatsen med Azure portal

Du kan använda Azure-portalen för att konfigurera [som är värd för flera webbplatser](application-gateway-multi-site-overview.md) när du skapar en [Programgateway](application-gateway-introduction.md). I den här självstudien skapar du serverdelspooler med skalningsuppsättningar för virtuella datorer. Du konfigurerar sedan lyssnare och regler baserat på de domäner du äger för att kontrollera att webbtrafiken anländer till rätt servrar i poolerna. I den här självstudien förutsätts att du äger flera domäner. Vi använder *www.contoso.com* och *www.fabrikam.com* som exempel.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en Application Gateway
> * Skapa virtuella datorer för backend-servrar
> * Skapa serverdelspooler med backend-servrar
> * Skapa lyssnare och regler för Routning
> * Skapa en CNAME-post i domänen

![Exempel på routning mellan flera webbplatser](./media/application-gateway-create-multisite-portal/scenario.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Skapa en Application Gateway

Ett virtuellt nätverk krävs för kommunikation mellan de resurser som du skapar. I det här exemplet skapas två undernät: ett för programgatewayen och ett annat för serverdelen. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen.

1. Klicka på **New** hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **Nätverk** och sedan **Application Gateway** i listan Aktuella.
3. Ange följande värden för programgatewayen:

    - *myAppGateway* – Namnet på programgatewayen.
    - *myResourceGroupAG* – Den nya resursgruppen.

    ![Skapa en ny programgateway](./media/application-gateway-create-multisite-portal/application-gateway-create.png)

4. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
5. Klicka på **Välj ett virtuellt nätverk**, klickar du på **Skapa nytt**, och ange följande värden för det virtuella nätverket:

    - *myVnet* – Det virtuella nätverkets namn.
    - *10.0.0.0/16* – Det virtuella nätverkets adressutrymme.
    - *myBackendSubnet* – Undernätsnamnet.
    - *10.0.0.0/24* – Undernätets adressutrymme.

    ![Skapa virtuellt nätverk](./media/application-gateway-create-multisite-portal/application-gateway-vnet.png)

6. Klicka på **OK** för att skapa det virtuella nätverket och undernätet.
7. Klicka på **välja en offentlig IP-adress**, klickar du på **Skapa nytt**, och ange sedan namnet på den offentliga IP-adressen. I det här exemplet heter den offentliga IP-adressen *myAGPublicIPAddress*. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
8. Godkänn standardvärdena för Lyssnarkonfigurationen, lämna inaktiverad brandväggen för webbaserade program och sedan på **OK**.
9. Granska inställningarna på sidan Sammanfattning och klicka sedan på **OK** att skapa nätverksresurser och application gateway. Det kan ta flera minuter för application gateway kan skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="add-a-subnet"></a>Lägga till ett undernät

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myVNet** i resurslistan.
2. Klicka på **undernät**, och klicka sedan på **undernät**.

    ![Skapa undernät](./media/application-gateway-create-multisite-portal/application-gateway-subnet.png)

3. Ange *myBackendSubnet* som namn på undernätet och klicka sedan på **OK**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

I det här exemplet skapar du två virtuella datorer som ska användas som serverdelsservrar för programgatewayen. Du kan också installera IIS på de virtuella datorerna för att kontrollera att trafik routning korrekt.

1. Klicka på **Ny**.
2. Klicka på **Compute** och välj sedan **Windows Server 2016 Datacenter** i listan över aktuella.
3. Ange följande värden för den virtuella datorn:

    - *contosoVM* – för namnet på den virtuella datorn.
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

    ![Installera anpassat tillägg](./media/application-gateway-create-multisite-portal/application-gateway-extension.png)

2. Kör följande kommando för att installera IIS på den virtuella datorn: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Skapa den andra virtuella datorn och installera IIS med hjälp av stegen som du har. Ange namnen på *fabrikamVM* för namnet och värdet för VMName i Set-AzureRmVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Skapa backend-pooler med virtuella datorer

1. Klicka på **alla resurser** och klicka sedan på **myAppGateway**.
2. Klicka på **serverdelspooler**, och klicka sedan på **Lägg till**.
3. Ange ett namn för *contosoPool* och Lägg till *contosoVM* med **Lägg till mål**.

    ![Lägga till serverdelsservrar](./media/application-gateway-create-multisite-portal/application-gateway-multisite-backendpool.png)

4. Klicka på **OK**.
5. Klicka på **serverdelspooler** och klicka sedan på **Lägg till**.
6. Skapa den *fabrikamPool* med den *fabrikamVM* med hjälp av stegen som du har.

## <a name="create-listeners-and-routing-rules"></a>Skapa lyssnare och regler för Routning

1. Klicka på **lyssnare** och klicka sedan på **multisite**.
2. Ange följande värden för lyssnaren:
    
    - *contosoListener* – för namnet på lyssnaren.
    - *www.contoso.com* -Ersätt exemplet värden namnet med ditt domännamn.

3. Klicka på **OK**.
4. Skapa en andra lyssnare med namnet på *fabrikamListener* och använda dina andra domännamn. I det här exemplet *www.fabrikam.com* används.

Regler bearbetas i ordningen de anges, och trafiken dirigeras med hjälp av den första regel som matchar oavsett särskilda egenskaper. Om du till exempel har en regel med en grundläggande lyssnare och en regel med en lyssnare för flera webbplatser för samma port så måste regeln med lyssnare för flera platser stå innan regeln med den grundläggande lyssnaren om regeln för flera platser ska fungera som förväntat. 

I det här exemplet skapar du två nya regler och tar bort standardregeln som skapades när du skapade programgatewayen. 

1. Klicka på **regler** och klicka sedan på **grundläggande**.
2. Ange *contosoRule* för namnet.
3. Välj *contosoListener* för lyssnaren.
4. Välj *contosoPool* för serverdelspoolen.

    ![Skapa en sökvägsbaserad regel](./media/application-gateway-create-multisite-portal/application-gateway-multisite-rule.png)

5. Klicka på **OK**.
6. Skapa en andra regel som använder namnen på *fabrikamRule*, *fabrikamListener*, och *fabrikamPool*.
7. Ta bort Standardregeln med namnet *1* genom att klicka på den och sedan på **ta bort**.

## <a name="create-a-cname-record-in-your-domain"></a>Skapa en CNAME-post i domänen

När du har skapat programgatewayen med dess offentliga IP-adress kan du hämta DNS-adressen och använda den till att skapa en CNAME-post i domänen. Du bör inte använda A-poster eftersom den virtuella IP-adressen kan ändras när programgatewayen startas om.

1. Klicka på **alla resurser**, och klicka sedan på **myAGPublicIPAddress**.

    ![Post application gateway-adress för DNS](./media/application-gateway-create-multisite-portal/application-gateway-multisite-dns.png)

2. Kopiera den DNS-adressen och använda det som värdet för en ny CNAME-post i din domän.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Ange domännamnet i adressfältet i webbläsaren. Till exempel http://www.contoso.com.

    ![Testa contoso-webbplatsen i programgatewayen](./media/application-gateway-create-multisite-portal/application-gateway-iistest.png)

2. Om du ändrar adressen till din andra domän bör du se något som liknar följande exempel:

    ![Testa fabrikam-webbplatsen i programgatewayen](./media/application-gateway-create-multisite-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du:

> [!div class="checklist"]
> * Skapa en Application Gateway
> * Skapa virtuella datorer för backend-servrar
> * Skapa serverdelspooler med backend-servrar
> * Skapa lyssnare och regler för Routning
> * Skapa en CNAME-post i domänen

> [!div class="nextstepaction"]
> [Läs mer om vad du kan göra med en programgateway](application-gateway-introduction.md)