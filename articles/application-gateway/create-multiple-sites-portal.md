---
title: Skapa en Programgateway som är värd för flera webbplatser - Azure-portalen | Microsoft Docs
description: Lär dig hur du skapar en Programgateway som är värd för flera webbplatser med Azure-portalen.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/28/2017
ms.author: davidmu
ms.openlocfilehash: 078252fe3413c702a93da59484be9fc678ddec1e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Skapa och konfigurera en Programgateway som värd för flera webbplatser med hjälp av Azure portal

Du kan använda Azure portal och [konfigurera vara värd för flera webbplatser](multiple-site-overview.md) när du skapar en [Programgateway](overview.md). I den här kursen definierar du serverdelsadresspooler med hjälp av virtuella datorer. Du konfigurerar sedan lyssnare och regler baserat på domäner som du äger för att kontrollera Internet-trafik anländer till rätt servrar i poolerna. Den här kursen förutsätter att du äger flera domäner och använder exempel på *www.contoso.com* och *www.fabrikam.com*.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en programgateway
> * Skapa virtuella datorer för backend-servrar
> * Skapa serverdelspooler med backend-servrar
> * Skapa backend-lyssnare
> * Skapa regler för Routning
> * Skapa en CNAME-post i din domän

![Exempel på flera platser routning](./media/create-multiple-sites-portal/scenario.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure portal [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Ett virtuellt nätverk behövs för kommunikation mellan resurser som du skapar. Två undernät skapas i det här exemplet: en för programgatewayen och en för backend-servrarna. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen.

1. Klicka på **ny** hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **nätverk** och välj sedan **Programgateway** i listan över aktuella.
3. Ange värdena för Programgateway:

    - *myAppGateway* – namnet på programgatewayen.
    - *myResourceGroupAG* - för den nya resursgruppen.

    ![Skapa nya Programgateway](./media/create-multiple-sites-portal/application-gateway-create.png)

4. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
5. Klicka på **Välj ett virtuellt nätverk**, klickar du på **Skapa nytt**, och ange sedan värdena för det virtuella nätverket:

    - *myVNet* – namnet på det virtuella nätverket.
    - *10.0.0.0/16* - för virtuella nätverkets adressutrymme.
    - *myAGSubnet* - för undernätsnamnet.
    - *10.0.0.0/24* - för undernätsadressutrymmet.

    ![Skapa det virtuella nätverket](./media/create-multiple-sites-portal/application-gateway-vnet.png)

6. Klicka på **OK** att skapa virtuella nätverk och undernät.
7. Klicka på **Välj en offentlig IP-adress**, klickar du på **Skapa nytt**, och ange sedan namnet på den offentliga IP-adressen. I det här exemplet heter den offentliga IP-adressen *myAGPublicIPAddress*. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
8. Godkänn standardvärdena för Lyssnarkonfigurationen lämna inaktiverad Brandvägg för webbaserade program och klicka sedan på **OK**.
9. Granska inställningarna på sidan Sammanfattning och klicka sedan på **OK** skapa nätverksresurser och programgatewayen. Det kan ta flera minuter för Programgateway skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="add-a-subnet"></a>Lägg till ett undernät

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på **myVNet** från resurslistan över.
2. Klicka på **undernät**, och klicka sedan på **undernät**.

    ![Skapa undernät](./media/create-multiple-sites-portal/application-gateway-subnet.png)

3. Ange *myBackendSubnet* för namnet på undernätet och klickar sedan på **OK**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

I det här exemplet skapar du två virtuella datorer som ska användas som backend-servrar för programgatewayen. Du kan även installera IIS på de virtuella datorerna för att kontrollera att trafik routning på rätt sätt.

1. Klicka på **Ny**.
2. Klicka på **Compute** och välj sedan **Windows Server 2016 Datacenter** i listan över aktuella.
3. Ange dessa värden för den virtuella datorn:

    - *contosoVM* – namnet på den virtuella datorn.
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

    ![Installera anpassade tillägg](./media/create-multiple-sites-portal/application-gateway-extension.png)

2. Kör följande kommando för att installera IIS på den virtuella datorn: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
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

3. Skapa den andra virtuella datorn och installera IIS med hjälp av stegen som du just har avslutats. Ange namnen på *fabrikamVM* för namnet och värdet för VMName i Set-AzureRmVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Skapa backend-pooler med virtuella datorer

1. Klicka på **alla resurser** och klicka sedan på **myAppGateway**.
2. Klicka på **serverdelspooler**, och klicka sedan på **Lägg till**.
3. Ange ett namn med *contosoPool* och Lägg till *contosoVM* med **Lägg till mål**.

    ![Lägg till backend-servrar](./media/create-multiple-sites-portal/application-gateway-multisite-backendpool.png)

4. Klicka på **OK**.
5. Klicka på **serverdelspooler** och klicka sedan på **Lägg till**.
6. Skapa den *fabrikamPool* med den *fabrikamVM* med hjälp av stegen som du just har avslutats.

## <a name="create-backend-listeners"></a>Skapa backend-lyssnare

1. Klicka på **lyssnare** och klicka sedan på **flera platser**.
2. Ange värdena för lyssnare:
    
    - *contosoListener* – namnet på lyssnaren.
    - *www.contoso.com* -Ersätt detta exempel på värden med ditt domännamn.

3. Klicka på **OK**.
4. Skapa en andra lyssnare med namnet på *fabrikamListener* och använda din andra domännamn. I det här exemplet *www.fabrikam.com* används.

## <a name="create-routing-rules"></a>Skapa regler för Routning

Regler bearbetas i ordningen de anges och trafik dirigeras med hjälp av den första regeln som matchar oavsett särskilda egenskaper. Till exempel om du har en regel med hjälp av en grundläggande lyssnare och en regel med en flera platser lyssnare båda på samma port måste regeln med flera platser lyssnaren anges innan en regel med grundläggande lyssnare för flera platser regeln för att fungera som förväntat. 

I det här exemplet skapar två nya regler och ta bort standardregel som skapades när du skapade programgatewayen. 

1. Klicka på **regler** och klicka sedan på **grundläggande**.
2. Ange *contosoRule* för namnet.
3. Välj *contosoListener* för lyssnaren.
4. Välj *contosoPool* för serverdelspoolen.

    ![Skapa en regel som sökväg-baserade](./media/create-multiple-sites-portal/application-gateway-multisite-rule.png)

5. Klicka på **OK**.
6. Skapa en andra regel med hjälp av namnen på *fabrikamRule*, *fabrikamListener*, och *fabrikamPool*.
7. Ta bort standardregel med namnet *regel 1* genom att klicka på den och sedan på **ta bort**.

## <a name="create-a-cname-record-in-your-domain"></a>Skapa en CNAME-post i din domän

När programgatewayen har skapats med dess offentliga IP-adress kan du hämta DNS-adress och använda den för att skapa en CNAME-post i din domän. Användning av A-poster rekommenderas inte eftersom VIP kan ändras när programgatewayen har startats om.

1. Klicka på **alla resurser**, och klicka sedan på **myAGPublicIPAddress**.

    ![Posten Programgateway DNS-adress](./media/create-multiple-sites-portal/application-gateway-multisite-dns.png)

2. Kopiera DNS-serveradress och använda det som värdet för en ny CNAME-post i din domän.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Ange domännamnet i adressfältet i webbläsaren. T.ex, http://www.contoso.com.

    ![Testa plats för contoso i Programgateway](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Ändra adressen till den andra domänen och du bör se något som liknar följande exempel:

    ![Testa fabrikam plats i Programgateway](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Skapa en programgateway
> * Skapa virtuella datorer för backend-servrar
> * Skapa serverdelspooler med backend-servrar
> * Skapa backend-lyssnare
> * Skapa regler för Routning
> * Skapa en CNAME-post i din domän
