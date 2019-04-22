---
title: Konfigurera Azure Application Gateway med en privat klientdels-IP-adress
description: Den här artikeln innehåller information om hur du konfigurerar en Programgateway med en privat klientdels-IP-adress
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/26/2019
ms.author: absha
ms.openlocfilehash: cfc63349e20aa6dbef4e0d31e81842d325bd3ec6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905548"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Konfigurera en Programgateway med en intern belastningsutjämnare (ILB) slutpunkt

Azure Application Gateway kan konfigureras med en internetuppkopplad VIP eller med en intern slutpunkt som inte är exponerad för Internet (med hjälp av en privat IP-adress för frontend-IP-adress), även känt som en intern belastningsutjämnare (ILB) slutpunkt. Konfigurera gatewayen med en privat IP-adress för klientdel är användbart för interna line-of-business-program som inte är exponerade mot Internet. Det är också användbart för tjänster och nivåer i ett affärsprogram med flera nivåer som finns vid en säkerhetsgräns som inte är exponerad för Internet men som fortfarande kräver distribution med resursallokering (round-robin), sessionsvaraktighet eller SSL-avslut (Secure Sockets Layer).

Den här artikeln vägleder dig igenom stegen för att konfigurera en Programgateway med en klientdel privat IP-adress från Azure Portal.

I den här artikeln får du lära dig hur du:

- Skapa en privat klientdels-IP-konfiguration för en Application Gateway
- Skapa en Programgateway med privata klientdelens IP-konfiguration


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Skapa en programgateway

För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du kan skapa ett nytt virtuellt nätverk, eller så kan du använda ett befintligt namn. I det här exemplet skapar vi ett nytt virtuellt nätverk. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen. Application Gateway-instanser skapas i olika undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

1. Klicka på **New** hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **Nätverk** och sedan **Application Gateway** i listan Aktuella.
3. Ange *myAppGateway* för namnet på application gateway och *myResourceGroupAG* för den nya resursgruppen.
4. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
5. Klicka på **Välj ett virtuellt nätverk**, klicka på **Skapa nytt** och ange sedan följande värden för det virtuella nätverket:
   - myVNet * – för namnet på det virtuella nätverket.
   - 10.0.0.0/16* – för virtuella nätverkets adressutrymme.
   - *myBackendSubnet* – Undernätsnamnet.
   - *10.0.0.0/24* – Undernätets adressutrymme.  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. Klicka på **OK** för att skapa det virtuella nätverket och undernätet.
7. Välj Frontend IP-konfigurationen som privat och som standard är en dynamisk IP-adresstilldelning. Den första tillgängliga adressen för valt undernätet anges som klientdelens IP-adress.
8. Om du vill välja en privat IP-adress från adressintervallet i undernätet (statisk tilldelning) klickar du på rutan **väljer en specifik privat IP-adress** och ange IP-adress.
   > [!NOTE]
   > När du har allokerat, kan inte IP-adresstyp (statisk eller dynamisk) ändras senare.
9. Välj din konfiguration för lyssnare för protokollet och port, konfiguration av WAF (vid behov) och klicka på OK.
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Granska inställningarna på sidan Sammanfattning och klicka sedan på **OK** att skapa nätverksresurser och application gateway. Det kan ta flera minuter för application gateway kan skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-backend-pool"></a>Lägg till serverdelspool

Serverdelspoolen används för att dirigera begäranden till backend-servrarna som ska tillhandahålla begäran. Serverdel kan bestå av nätverkskort, VM-skalningsuppsättningar, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade namn (FQDN) och flera innehavare serverprogram som Azure App Service. I det här exemplet använder vi virtuella datorer som mål-serverdelen. Vi kan använda befintliga virtuella datorer, eller så kan du skapa nya. I det här exemplet skapar vi två virtuella datorer som använder Azure som serverdelsservrar för application gateway. Detta gör att vi:

1. Skapa 2 nya virtuella datorer, *myVM* och *myVM2*, som ska användas som backend-servrarna.
2. Installera IIS på de virtuella datorerna för att kontrollera att application gateway har skapats.
3. Lägg till backend-servrarna till i serverdelspoolen.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **Ny**.
2. Klicka på **Compute** och sedan **Windows Server 2016 Datacenter** i listan Aktuella.
3. Ange följande värden för den virtuella datorn:
   - *myVM* – Namnet på den virtuella datorn.
   - *azureuser* – för administratörens användarnamn.
   - *Azure123456!* som lösenord.
   - Välj **Använd befintlig** och sedan *myResourceGroupAG*.
4. Klicka på **OK**.
5. Välj **DS1_V2** för storleken på den virtuella datorn och klicka på **Välj**.
6. Kontrollera att **myVNet** har valts för det virtuella nätverket och att undernätet är **myBackendSubnet**.
7. Inaktivera startdiagnostikinställningar genom att klicka på **Inaktiverad**.
8. Klicka på **OK**, granska inställningarna på sammanfattningssidan och klicka sedan på **Skapa**.

### <a name="install-iis"></a>Installera IIS

1. Öppna det interaktiva gränssnittet och kontrollera att det är inställt på **PowerShell**.
    ![private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
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
