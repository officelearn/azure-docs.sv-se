---
title: 'Självstudiekurs: URL-sökvägsbaserade routningsregler med portalen - Azure Application Gateway'
description: I den här självstudien får du lära dig hur du skapar URL-sökvägsbaserade routningsregler för en programgateway och skalningsuppsättning för virtuella datorer med hjälp av Azure-portalen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: bc810ac7901d83f03d3f3ac2199561225326d261
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74048142"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Självstudiekurs: Skapa en programgateway med sökvägsbaserade routningsregler med Azure-portalen

Du kan använda Azure-portalen för att konfigurera [URL-sökvägsbaserade routningsregler](application-gateway-url-route-overview.md) när du skapar en [programgateway](application-gateway-introduction.md). I den här självstudien skapar du serverdpooler med hjälp av virtuella datorer. Du skapar sedan routningsregler som ser till att webbtrafiken kommer till lämpliga servrar i poolerna.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en programgateway
> * Skapa virtuella datorer för serverdservrar
> * Skapa servergruppspooler med serverdaservrar
> * Skapa en backend-lyssnare
> * Skapa en sökvägsbaserad routningsregel

![URL-routningsexempel](./media/application-gateway-create-url-route-portal/scenario.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på[https://portal.azure.com](https://portal.azure.com)

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

I det här exemplet skapar du tre virtuella datorer som ska användas som servergruppsservrar för programgatewayen. Du installerar också IIS på de virtuella datorerna för att kontrollera att programgatewayen fungerar som förväntat.

1. Välj **Skapa en resurs** på Azure-portalen.
2. Välj **Windows Server 2016 Datacenter** i listan Populär.
3. Ange följande värden för den virtuella datorn:

    - **Resursgrupp**väljer du **Skapa ny**och skriver sedan *myResourceGroupAG*.
    - **Namn på virtuell dator:** *myVM1*
    - **Region**: *(USA) Östra USA*
    - **Användarnamn**: *azureuser*
    - **Lösenord**: *Azure123456!*


4. Välj **Nästa:Diskar**.
5. Välj **nästa:Nätverk**
6. För **virtuellt nätverk**väljer du **Skapa nya** och skriver sedan dessa värden för det virtuella nätverket:

   - *myVnet* – Det virtuella nätverkets namn.
   - *10.0.0.0/16* – Det virtuella nätverkets adressutrymme.
   - *myBackendSubnet* för det första undernätsnamnet
   - *10.0.1.0/24* - för undernätets adressutrymme.
   - *myAGSubnet* - för det andra undernätsnamnet.
   - *10.0.0.0/24* – Undernätets adressutrymme.
7. Välj **OK**.

8. Kontrollera att **myBackendSubnet** är markerat för undernätet under **Nätverksgränssnitt**och välj sedan **Nästa: Hantering**.
9. Välj **Av** för att inaktivera startdiagnostik.
10. Klicka på **Granska + Skapa,** granska inställningarna på sammanfattningssidan och välj sedan **Skapa**.
11. Skapa ytterligare två virtuella datorer, *myVM2* och *myVM3* och placera dem i *MyVNet* virtuella nätverk och *myBackendSubnet* undernätet.

### <a name="install-iis"></a>Installera IIS

1. Öppna det interaktiva gränssnittet och kontrollera att det är inställt på **PowerShell**.

    ![Installera anpassat tillägg](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Kör följande kommando för att installera IIS på den virtuella datorn: 

    ```azurepowershell
         $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

        Set-AzVMExtension `
         -ResourceGroupName myResourceGroupAG `
         -Location eastus `
         -ExtensionName IIS `
         -VMName myVM1 `
         -Publisher Microsoft.Compute `
         -ExtensionType CustomScriptExtension `
         -TypeHandlerVersion 1.4 `
         -Settings $publicSettings
    ```

3. Skapa ytterligare två virtuella datorer och installera IIS med de steg som du just har slutfört. Ange namnen *på myVM2* och *myVM3* för namnen och för värdena för VMName i Set-AzVMExtension.

## <a name="create-an-application-gateway"></a>Skapa en programgateway

1. Välj **Skapa en resurs** på den vänstra menyn på Azure-portalen. Fönstret **Nytt** visas.

2. Välj **Nätverk** och välj sedan **Application Gateway** i listan **Aktuell.**

### <a name="basics-tab"></a>Fliken Grundläggande

1. På fliken **Grunderna** anger du dessa värden för följande inställningar för programgateway:

   - **Resursgrupp**: Välj **myResourceGroupAG** för resursgruppen.
   - **Namn på programgateway**: Ange *myAppGateway* för namnet på programgatewayen.
   - **Region** - Välj **(USA) östra USA**.

        ![Skapa ny programgateway: Grunderna](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Under **Konfigurera virtuellt nätverk**väljer du **myVNet** för namnet på det virtuella nätverket.
3. Välj **myAGSubnet** för undernätet.
3. Acceptera standardvärdena för de andra inställningarna och välj sedan **Nästa: Frontends**.

### <a name="frontends-tab"></a>Fliken Frontends

1. Kontrollera att **IP-adresstypen Frontend** är offentlig **på**fliken **Frontend.**

   > [!NOTE]
   > För Application Gateway v2 SKU kan du bara välja **IP-konfiguration** för offentliga frontend. Privat klientdel IP-konfiguration är för närvarande inte aktiverad för den här v2 SKU.

2. Välj **Skapa ny** för den offentliga **IP-adressen** och ange *myAGPublicIPAddress* för det offentliga IP-adressnamnet och välj sedan **OK**. 
3. Välj **Nästa: Backends**.

### <a name="backends-tab"></a>Fliken Backends

Serverdapoolen används för att dirigera begäranden till serverdaservrar som betjänar begäran. Serverdelspooler kan bestå av nätverkskort, skalningsuppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domännamn (FQDN) och backend-ändar med flera innehavare som Azure App Service.

1. På fliken **Backends** väljer du **+Lägg till en backend-pool**.

2. I fönstret **Lägg till en backend-pool** som öppnas anger du följande värden för att skapa en tom backend-pool:

    - **Namn**: Ange *myBackendPool* för namnet på backend-poolen.
3. Under **Bakåtsträvande mål** **väljer**du **Virtuell dator** i listrutan.

5. Under **Mål** väljer du nätverksgränssnittet för **myVM1**.
6. Välj **Lägg till**.
7. Upprepa om du vill lägga till en backend-pool *för bilder* med *myVM2* som mål och en video-backend-pool med *myVM3* som mål. *Video*
8. Välj **Lägg till** om du vill spara serverdapoolkonfigurationen och gå tillbaka till fliken **Serveringar.**

4. På fliken **Serveringar** väljer du **Nästa: Konfiguration**.

### <a name="configuration-tab"></a>Fliken Konfiguration

På fliken **Konfiguration** ansluter du klientdels- och serverdelspoolen som du skapade med hjälp av en routningsregel.

1. Välj **Lägg till en regel** i kolumnen **Routningsregler.**

2. I fönstret **Lägg till en routningsregel** som öppnas anger du *myRoutingRule* för **regelnamnet**.

3. En routningsregel kräver en lyssnare. På fliken **Lyssnare** i fönstret **Lägg till en routningsregel** anger du följande värden för lyssnaren:

    - **Lyssnarens namn**: Ange *myListener* för lyssnarens namn.
    - **Frontend IP:** Välj **Offentlig** om du vill välja den offentliga IP-adress som du skapade för klientdelen.
    - **Port**: Typ *8080*
  
        Acceptera standardvärdena för de andra inställningarna på fliken **Lyssnare** och välj sedan fliken **Serveringsmål** för att konfigurera resten av routningsregeln.

4. På fliken **Backend-mål** väljer du **myBackendPool** för **backend-målet**.

5. För **HTTP-inställningen**väljer du **Skapa ny** om du vill skapa en ny HTTP-inställning. HTTP-inställningen avgör hur routningsregeln fungerar. 

6. I fönstret **Lägg till en HTTP-inställning** som öppnas anger du *minHTTPSetting* för **HTTP-inställningsnamnet**. Acceptera standardvärdena för de andra inställningarna i **fönstret Lägg till en HTTP-inställning** och välj sedan **Lägg** till för att återgå till fönstret Lägg till **en routningsregel.**
7. Under **Sökvägsbaserad routning**väljer du **Lägg till flera mål om du vill skapa en sökvägsbaserad regel**.
8. För **Bana**skriver */images/*\*.
9. Skriv *Bilder*för **path-regelnamn**.
10. För **HTTP-inställning**väljer du **minHTTPSetting**
11. För **Backend-mål**väljer du **Bilder**.
12. Välj **Lägg till** om du vill spara sökvägsregeln och gå tillbaka till fliken Lägg till en **routningsregel.**
13. Upprepa om du vill lägga till en annan regel för Video.
14. Välj **Lägg till** om du vill lägga till routningsregeln och gå tillbaka till fliken **Konfiguration.**
15. Välj **Nästa: Taggar** och sedan **Nästa: Granska + skapa**.

> [!NOTE]
> Du behöver inte lägga */* till en anpassad * sökvägsregel för att hantera standardfall. Detta hanteras automatiskt av standardpoolen för backend.

### <a name="review--create-tab"></a>Fliken Granska + skapa

Granska inställningarna på fliken **Granska + skapa** och välj sedan **Skapa** för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen. Det kan ta flera minuter för Azure att skapa programgatewayen. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.


## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

    ![Registrera programgatewayens offentliga IP-adress](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Till exempel http:\//52.188.72.175:8080.

    ![Testa basadressen i programgatewayen](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   Lyssnaren på port 8080 dirigerar den här begäran till standardpoolen för backend.

3. Ändra webbadressen till *http://&lt;&gt;ip-adress: 8080/images/test.htm*, ersätta &lt;&gt; IP-adress med din IP-adress, och du bör se något i stil med följande exempel:

    ![Testa bildadressen i programgatewayen](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   Lyssnaren på port 8080 dirigerar denna begäran till *profilens* backend-pool.

4. Ändra webbadressen till *http://&lt;&gt;ip-adress: 8080/video/test.htm*, ersätta &lt;IP-adress&gt; med din IP-adress, och du bör se något i stil med följande exempel:

    ![Testa videoadressen i programgatewayen](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   Lyssnaren på port 8080 dirigerar *Video* denna begäran till videobackendpoolen.


## <a name="next-steps"></a>Nästa steg

- [Aktivera slutpunkt till SSL på Azure Application Gateway](application-gateway-backend-ssl.md)
