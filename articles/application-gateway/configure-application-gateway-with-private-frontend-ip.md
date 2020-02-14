---
title: Konfigurera en intern belastnings Utjämnings slut punkt (ILB)
titleSuffix: Azure Application Gateway
description: Den här artikeln innehåller information om hur du konfigurerar Application Gateway med en privat klient dels-IP-adress
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: f56929e14aef34f675139782328ed5c559df12c7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198640"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Konfigurera en Programgateway med en intern belastningsutjämnare (ILB)

Azure Application Gateway kan konfigureras med en Internet-riktad VIP eller med en intern slut punkt som inte är exponerad för Internet. En intern slut punkt använder en privat IP-adress för klient delen, som även kallas en *intern belastningsutjämnare (ILB)* .

Att konfigurera gatewayen med en privat IP-adress för klient delen är användbart för interna branschspecifika program som inte är utsatta för Internet. Det är också användbart för tjänster och nivåer i ett program med flera nivåer som är i en säkerhets gränser som inte är utsatt för Internet men som fortfarande kräver belastnings fördelning för resursallokering, session varaktighet eller Secure Sockets Layer (SSL).

Den här artikeln vägleder dig genom stegen för att konfigurera en Programgateway med en privat IP-adress för klient delen med hjälp av Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Skapa en programgateway

För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du kan antingen skapa ett nytt virtuellt nätverk eller använda ett befintligt. I det här exemplet skapar du ett nytt virtuellt nätverk. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen. Application Gateway instanser skapas i separata undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

1. Expandera Portal-menyn och välj **skapa en resurs**.
2. Välj **Nätverk** och sedan **Application Gateway** i listan Aktuella.
3. Ange *myAppGateway* som namn på Application Gateway och *myResourceGroupAG* för den nya resurs gruppen.
4. För **region**väljer du **(US) centrala USA**.
5. För **nivå**väljer du **standard**.
6. Under **Konfigurera virtuellt nätverk** väljer du **Skapa ny**och anger följande värden för det virtuella nätverket:
   - *myVnet* – Det virtuella nätverkets namn.
   - *10.0.0.0/16* – Det virtuella nätverkets adressutrymme.
   - *myBackendSubnet* – Undernätsnamnet.
   - *10.0.0.0/24* – Undernätets adressutrymme.
   - *myBackendSubnet* – för backend-undernätets namn.
   - *10.0.1.0/24* – adress utrymmet för Server delens undernät.

    ![Skapa det virtuella nätverket](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Välj **OK** för att skapa det virtuella nätverket och under nätet.
7. Välj **Nästa: frontend**-klienter.
8. För **IP-adress för klient**del väljer du **privat**.

   Som standard är det en dynamisk IP-adresstilldelning. Den första tillgängliga adressen för det konfigurerade under nätet tilldelas som IP-adress för klient delen.
   > [!NOTE]
   > När det har allokerats kan IP-adress typen (statisk eller dynamisk) inte ändras senare.
9. Välj **Nästa:** Server delar.
10. Välj **Lägg till en backend-pool**.
11. I **namn**skriver du *appGatewayBackendPool*.
12. Välj **Ja**om du vill **lägga till en backend-pool utan mål**. Du kommer att lägga till målen senare.
13. Välj **Lägg till**.
14. Välj **Nästa: konfiguration**.
15. Under **regler för routning**väljer du **Lägg till en regel**.
16. För **regel namn**skriver du *Rrule-01*.
17. För **lyssnar namn**skriver du *Listener-01*.
18. För **klient delens IP-adress**väljer du **privat**.
19. Acceptera återstående standardvärden och välj fliken **backend-mål** .
20. För **måltyp**väljer du **backend-pool**och väljer sedan **appGatewayBackendPool**.
21. För **http-inställning**väljer du **Skapa ny**.
22. För **http-Inställningens namn**skriver du *http-Setting-01*.
23. För **Server dels protokoll**väljer du **http**.
24. För **Server dels port**skriver du *80*.
25. Acceptera återstående standardvärden och välj **Lägg till**.
26. På sidan **Lägg till regel för routning** väljer du **Lägg till**.
27. Välj **Nästa: Taggar**.
28. Välj **Nästa: granska + skapa**.
29. Granska inställningarna på sidan Sammanfattning och välj sedan **skapa** för att skapa nätverks resurserna och programgatewayen. Det kan ta flera minuter att skapa programgatewayen. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-backend-pool"></a>Lägg till backend-pool

Backend-poolen används för att dirigera begär anden till backend-servrar som hanterar begäran. Server delen kan bestå av nätverkskort, skalnings uppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domän namn (FQDN) och backend-ändar för flera klienter som Azure App Service. I det här exemplet använder du virtuella datorer som mål Server del. Du kan antingen använda befintliga virtuella datorer eller skapa nya. I det här exemplet skapar du två virtuella datorer som Azure använder som backend-servrar för programgatewayen.

Gör så här:

1. Skapa två nya virtuella datorer, *myVM* och *myVM2*, som används som backend-servrar.
2. Installera IIS på de virtuella datorerna för att kontrol lera att Application Gateway har skapats.
3. Lägg till backend-servrarna i backend-poolen.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs**.
2. Välj **Compute** och välj sedan **virtuell dator**.
4. Ange följande värden för den virtuella datorn:
   - Välj *myResourceGroupAG* för **resurs grupp**.
   - *myVM* – för **namnet på den virtuella datorn**.
   - Välj **Windows Server 2019 Data Center** för **avbildning**.
   - *azureadmin* – för **användar namnet**.
   - *Azure123456!* för **lösen ordet**.
5. Acceptera återstående standardvärden och välj **Nästa: diskar**.
6. Acceptera standardvärdena och välj **Nästa: nätverk**.
7. Kontrollera att **myVNet** har valts för det virtuella nätverket och att undernätet är **myBackendSubnet**.
8. Acceptera återstående standardvärden och välj **Nästa: hantering**.
9. Välj **av** om du vill inaktivera startdiagnostik.
10. Acceptera återstående standardvärden och välj **Nästa: Avancerat**.
11. Välj **Nästa: Taggar**.
12. Välj **Nästa: granska + skapa**.
13. Granska inställningarna på sidan Sammanfattning och välj sedan **skapa**. Det kan ta flera minuter att skapa den virtuella datorn. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="install-iis"></a>Installera IIS

1. Öppna Cloud Shell och kontrol lera att den är inställd på **PowerShell**.
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



3. Skapa en andra virtuell dator och installera IIS med hjälp av de steg som du just har slutfört. Ange myVM2 som namn och för VMName i set-AzVMExtension.

### <a name="add-backend-servers-to-backend-pool"></a>Lägg till backend-servrar i backend-poolen

1. Välj **Alla resurser** och välj sedan **myAppGateway**.
2. Välj **backend-pooler**. Välj **appGatewayBackendPool**.
3. Under **måltyp** väljer du **virtuell dator** och under **mål**väljer du den vNIC som är kopplad till myVM.
4. Upprepa om du vill lägga till MyVM2.
   ![privat – frontendip – 4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. Välj **Spara.**

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Kontrol lera klient delens IP-adress som har tilldelats genom att klicka på sidan **IP-konfigurationer för klient** del i portalen.
    ![Private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Kopiera den privata IP-adressen och klistra in den i webbläsarens Adress fält i en virtuell dator i samma VNet eller lokalt som har anslutning till det här virtuella nätverket och försök få åtkomst till Application Gateway.

## <a name="next-steps"></a>Nästa steg

Om du vill övervaka hälso tillståndet för Server delen, se [backend-hälsa och diagnostikloggar för Application Gateway](application-gateway-diagnostics.md).
