---
title: Konfigurera en intern belastnings Utjämnings slut punkt (ILB)
titleSuffix: Azure Application Gateway
description: Den här artikeln innehåller information om hur du konfigurerar Application Gateway med en privat klient dels-IP-adress
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: a9e3150a5382e4d690ddf66c43bbe51e125509d3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075221"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Konfigurera en Programgateway med en intern belastningsutjämnare (ILB)

Azure Application Gateway kan konfigureras med en Internet-baserad VIP eller med en intern slut punkt som inte exponeras för Internet (genom att använda en privat IP-adress för klient delens IP-adress), även kallad en intern belastningsutjämnare (ILB). Det är praktiskt att konfigurera gatewayen med en privat IP-adress i klient delen för interna affärs program som inte är exponerade för Internet. Det är också användbart för tjänster och nivåer i ett affärsprogram med flera nivåer som finns vid en säkerhetsgräns som inte är exponerad för Internet men som fortfarande kräver distribution med resursallokering (round-robin), sessionsvaraktighet eller SSL-avslut (Secure Sockets Layer).

Den här artikeln beskriver steg för steg hur du konfigurerar en Programgateway med en privat IP-adress för klient delen från Azure Portal.

I den här artikeln får du lära dig hur du:

- Skapa en privat frontend IP-konfiguration för en Application Gateway
- Skapa en Programgateway med en privat klient delens IP-konfiguration


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Skapa en programgateway

För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du kan antingen skapa ett nytt virtuellt nätverk eller använda ett befintligt. I det här exemplet ska vi skapa ett nytt virtuellt nätverk. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen. Application Gateway instanser skapas i separata undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

1. Klicka på **ny** som finns i det övre vänstra hörnet av Azure Portal.
2. Välj **Nätverk** och sedan **Application Gateway** i listan Aktuella.
3. Ange *myAppGateway* som namn på Application Gateway och *myResourceGroupAG* för den nya resurs gruppen.
4. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
5. Klicka på **Välj ett virtuellt nätverk**, klicka på **Skapa nytt** och ange sedan följande värden för det virtuella nätverket:
   - myVNet * – för namnet på det virtuella nätverket.
   - 10.0.0.0/16 * – för det virtuella nätverkets adress utrymme.
   - *myBackendSubnet* – Undernätsnamnet.
   - *10.0.0.0/24* – Undernätets adressutrymme.  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. Klicka på **OK** för att skapa det virtuella nätverket och undernätet.
7. Välj IP-konfigurationen för klient delen som privat och som standard är det en dynamisk IP-adresstilldelning. Den första tillgängliga adressen för det valda under nätet tilldelas som IP-adress för klient delen.
8. Om du vill välja en privat IP-adress från under nätets adress intervall (statisk allokering) klickar du på rutan **Välj en speciell privat IP-adress** och anger IP-adressen.
   > [!NOTE]
   > När det har allokerats kan IP-adress typen (statisk eller dynamisk) inte ändras senare.
9. Välj lyssnar konfigurationen för protokollet och porten, WAF konfiguration (om det behövs) och klicka på OK.
    ![Private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Granska inställningarna på sidan Sammanfattning och klicka sedan på **OK** för att skapa nätverks resurserna och programgatewayen. Det kan ta flera minuter för application gateway kan skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-backend-pool"></a>Lägg till backend-pool

Backend-poolen används för att dirigera begär anden till backend-servrar som kommer att betjäna begäran. Server delen kan bestå av nätverkskort, skalnings uppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domän namn (FQDN) och backend-ändar för flera klienter som Azure App Service. I det här exemplet kommer vi att använda virtuella datorer som mål Server del. Vi kan antingen använda befintliga virtuella datorer eller skapa nya. I det här exemplet ska vi skapa två virtuella datorer som Azure använder som backend-servrar för programgatewayen. För att göra detta kommer vi att:

1. Skapa 2 nya virtuella datorer, *myVM* och *myVM2*, som ska användas som backend-servrar.
2. Installera IIS på de virtuella datorerna för att kontrol lera att Application Gateway har skapats.
3. Lägg till backend-servrarna i backend-poolen.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **Ny**.
2. Klicka på **Compute** och sedan **Windows Server 2016 Datacenter** i listan Aktuella.
3. Ange följande värden för den virtuella datorn:
   - *myVM* – Namnet på den virtuella datorn.
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
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
