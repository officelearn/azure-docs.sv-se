---
title: Konfigurera en ILB-slutpunkt (Internal Load Balancer)
titleSuffix: Azure Application Gateway
description: Den här artikeln innehåller information om hur du konfigurerar Application Gateway med en privat IP-adress för klientdel
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: 809274aba35e9607aeacf7c6483ec3d10f899667
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312373"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Konfigurera en programgateway med en intern ILB-slutpunkt (Load Balancer)

Azure Application Gateway kan konfigureras med en Internet-vänd VIP eller med en intern slutpunkt som inte är exponerad för Internet. En intern slutpunkt använder en privat IP-adress för klientdelen, som också kallas en *ILB-slutpunkt (Internal Load Balancer).*

Konfigurera gatewayen med en klientdel privat IP-adress är användbart för interna affärsprogram som inte är exponerade för Internet. Det är också användbart för tjänster och nivåer inom ett flernivåprogram som finns i en säkerhetsgräns som inte är exponerad för Internet men som fortfarande kräver lastdistribution av round-robin, sessionsstyvhet eller Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL), avslutning.

Den här artikeln guidar dig genom stegen för att konfigurera en programgateway med en klientdel privat IP-adress med hjälp av Azure-portalen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Skapa en programgateway

För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du kan antingen skapa ett nytt virtuellt nätverk eller använda ett befintligt nätverk. I det här exemplet skapar du ett nytt virtuellt nätverk. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen. Application Gateway-instanser skapas i separata undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

1. Expandera portalmenyn och välj **Skapa en resurs**.
2. Välj **Nätverk** och sedan **Application Gateway** i listan Aktuella.
3. Ange *myAppGateway* för namnet på programgatewayen och *myResourceGroupAG* för den nya resursgruppen.
4. För **Region**väljer du **(USA) Central US**.
5. För **Nivå**väljer du **Standard**.
6. Under **Konfigurera virtuellt nätverk** väljer du Skapa **nytt**och anger sedan dessa värden för det virtuella nätverket:
   - *myVnet* – Det virtuella nätverkets namn.
   - *10.0.0.0/16* – Det virtuella nätverkets adressutrymme.
   - *myBackendSubnet* – Undernätsnamnet.
   - *10.0.0.0/24* – Undernätets adressutrymme.
   - *myBackendSubnet* - för backend-undernätets namn.
   - *10.0.1.0/24* - för backend-undernätets adressutrymme.

    ![Skapa det virtuella nätverket](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Välj **OK** om du vill skapa det virtuella nätverket och undernätet.
7. Välj **Nästa:Frontends**.
8. För **IP-adresstyp för klientdel**väljer du **Privat**.

   Som standard är det en dynamisk IP-adresstilldelning. Den första tillgängliga adressen för det konfigurerade undernätet tilldelas som ip-adress för klientdelen.
   > [!NOTE]
   > När IP-adresstypen (statisk eller dynamisk) har allokerats kan den inte ändras senare.
9. Välj **Nästa:Backends**.
10. Välj **Lägg till en backend-pool**.
11. För **Namn**skriver *du appGatewayBackendPool*.
12. För **Lägg till backend pool utan mål**väljer du **Ja**. Du lägger till målen senare.
13. Välj **Lägg till**.
14. Välj **Nästa:Konfiguration**.
15. Under **Routningsregler**väljer du **Lägg till en regel**.
16. För **Regelnamn**skriver du *Rrule-01*.
17. För **Lyssnarens namn**skriver du *Lyssnare-01*.
18. För **Frontend IP**väljer du **Privat**.
19. Acceptera de återstående standardvärdena och välj fliken **Backend-mål.**
20. För **Måltyp**väljer du **Backend-pool**och väljer sedan **appGatewayBackendPool**.
21. För **HTTP-inställning**väljer du **Skapa ny**.
22. För **HTTP-inställningsnamn**skriver du *http-setting-01*.
23. För **Backend-protokollet**väljer du **HTTP**.
24. För **Backend-port**skriver du *80*.
25. Acceptera de återstående standardinställningarna och välj **Lägg till**.
26. På sidan **Lägg till en routningsregel** väljer du **Lägg till**.
27. Välj **Nästa: Taggar**.
28. Välj **Nästa: Granska + skapa**.
29. Granska inställningarna på sammanfattningssidan och välj sedan **Skapa** för att skapa nätverksresurserna och programgatewayen. Det kan ta flera minuter att skapa programgatewayen. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-backend-pool"></a>Lägga till backend-pool

Serverdapoolen används för att dirigera begäranden till serverdaservrar som betjänar begäran. Serverdelen kan bestå av nätverkskort, skalningsuppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domännamn (FQDN) och backen slutar med flera innehavare som Azure App Service. I det här exemplet använder du virtuella datorer som målbacksad. Du kan antingen använda befintliga virtuella datorer eller skapa nya. I det här exemplet skapar du två virtuella datorer som Azure använder som backend-servrar för programgatewayen.

För att göra detta, du:

1. Skapa två nya virtuella datorer, *myVM* och *myVM2*, som används som servergruppsservrar.
2. Installera IIS på de virtuella datorerna för att kontrollera att programgatewayen har skapats.
3. Lägg till serverdservrarna i serverdapoolen.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs**.
2. Välj **Beräkna** och välj sedan **Virtuell dator**.
4. Ange följande värden för den virtuella datorn:
   - välj *gruppen ResourceGroupAG* för **resurs**.
   - *myVM* - för **virtuellt datornamn**.
   - Välj **Windows Server 2019 Datacenter** för **avbildning**.
   - *azureadmin* - för **användarnamnet**.
   - *Azure123456!* för **lösenordet**.
5. Acceptera de återstående standardvärdena och välj **Nästa: Diskar**.
6. Acceptera standardinställningarna och välj **Nästa: Nätverk .**
7. Kontrollera att **myVNet** har valts för det virtuella nätverket och att undernätet är **myBackendSubnet**.
8. Acceptera de återstående standardinställningarna och välj **Nästa: Hantering**.
9. Välj **Av** för att inaktivera startdiagnostik.
10. Acceptera de återstående standardinställningarna och välj **Nästa : Avancerat**.
11. Välj **Nästa: Taggar**.
12. Välj **Nästa : Granska + skapa**.
13. Granska inställningarna på sammanfattningssidan och välj sedan **Skapa**. Det kan ta flera minuter att skapa den virtuella datorn. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="install-iis"></a>Installera IIS

1. Öppna Cloud Shell och se till att det är inställt på **PowerShell**.
    ![privat-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Kör följande kommando för att installera IIS på den virtuella datorn:

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `

     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `

     -Location CentralUS `

   ```



3. Skapa en andra virtuell dator och installera IIS med hjälp av de steg som du just har slutfört. Ange myVM2 för dess namn och för VMName i Set-AzVMExtension.

### <a name="add-backend-servers-to-backend-pool"></a>Lägga till serverdserver i serverdapoolen

1. Välj **Alla resurser** och välj sedan **myAppGateway**.
2. Välj **Backend-pooler**. Välj **appGatewayBackendPool**.
3. Under **Måltyp** välj **Virtuell dator** och under **Mål**väljer du det virtuella nätverkskortet som är associerat med myVM.
4. Upprepa för att lägga till MyVM2.
   ![privat-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. välj **Spara.**

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Kontrollera din klient-IP som tilldelats genom att klicka på sidan **Frontend IP-konfigurationer** i portalen.
    ![privat-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Kopiera den privata IP-adressen och klistra sedan in den i webbläsarens adressfält i ett virtuellt virtuell dator i samma virtuella nätverk eller lokala som har anslutning till det här virtuella nätverket och försök komma åt Application Gateway.

## <a name="next-steps"></a>Nästa steg

Om du vill övervaka hälsotillståndet för din backend läser du [Backen end-hälso- och diagnostikloggar för Application Gateway](application-gateway-diagnostics.md).
