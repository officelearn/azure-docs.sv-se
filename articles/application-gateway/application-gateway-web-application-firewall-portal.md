---
title: Självstudie – skapa en Programgateway med en brandvägg för webbaserade program – Azure-portalen | Microsoft Docs
description: Lär dig hur du skapar en Programgateway med en brandvägg för webbaserade program med hjälp av Azure portal.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/25/2019
ms.author: victorh
ms.openlocfilehash: 1284ddec4cd9cea3ea53c20d437550405dd614d9
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905876"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Skapa en Programgateway med en brandvägg för webbaserade program med Azure portal

> [!div class="op_single_selector"]
>
> - [Azure Portal](application-gateway-web-application-firewall-portal.md)
> - [PowerShell](tutorial-restrict-web-traffic-powershell.md)
> - [Azure CLI](tutorial-restrict-web-traffic-cli.md)
>
> 

Den här självstudien visar hur du använder Azure-portalen för att skapa en [programgatewayen](application-gateway-introduction.md) med en [Brandvägg för webbaserade program](application-gateway-web-application-firewall-overview.md) (WAF). I brandväggen används [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-regler till att skydda programmet. De här reglerna kan exempelvis skydda mot attacker som SQL-inmatning, skriptattacker mellan webbplatser och sessionskapningar. Efter att application gateway kan testa du den och kontrollera att de fungerar korrekt. Med Azure Application Gateway dirigera application Internet-trafik till specifika resurser genom att tilldela lyssnare till portar, skapa regler och lägga till resurser till en serverdelspool. För enkelhetens skull använder den här artikeln en enkel installation med en offentlig frontend IP-adress, en grundläggande lyssnare till en plats på den här application gateway-värd, två virtuella datorer som används för serverdelspoolen och en regel för vidarebefordran av grundläggande begäran.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en programgateway med WAF aktiverat
> * Skapa de virtuella datorerna som används som backend-servrar
> * Skapa ett lagringskonto och konfigurera diagnostik

![Exempel på brandvägg för webbaserade program](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Skapa en programgateway

För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du kan skapa ett nytt virtuellt nätverk, eller så kan du använda ett befintligt namn. I det här exemplet skapar vi ett nytt virtuellt nätverk. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen. Application Gateway-instanser skapas i olika undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

Välj **Skapa en resurs** på den vänstra menyn på Azure-portalen. Fönstret **Nytt** visas.

Välj **Nätverk** och välj sedan **Programgateway** i listan **Aktuella**.

### <a name="basics-page"></a>Sidan Grundläggande inställningar

1. På sidan **Grundläggande inställningar** anger du dessa värden för följande inställningar för programgatewayen:
   - **Namn**: Ange *myAppGateway* som namn på programgatewayen.
   - **Resursgrupp**: Välj **myResourceGroupAG** som resursgrupp. Om den inte finns väljer du **Skapa ny** för att skapa den.
   - Välj *WAF* för nivån av application gateway.![ Skapa ny Programgateway](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.

### <a name="settings-page"></a>Sidan Inställningar

1. Välj **Välj ett virtuellt nätverk** under **Undernätskonfiguration** på sidan **Inställningar**. <br>
2. Välj **Skapa nytt** på sidan **Välj virtuellt nätverk** och ange sedan värden för följande inställningar för virtuella nätverk:
   - **Namn**: Ange *myVnet* som namn på det virtuella nätverket.
   - **Adressutrymme**: Ange *10.0.0.0/16* som adressutrymme för det virtuella nätverket.
   - **Namn på undernät**: Ange *myBackendSubnet* som namn på undernätet.<br>Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.
   - **Adressintervall för undernätet**: Ange *10.0.0.0/24* för adressintervallet i undernätet.![ Skapa virtuellt nätverk](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)
3. Klicka på **OK** för att skapa det virtuella nätverket och undernätet.
4. Välj den **Frontend-IP-konfiguration**. Kontrollera att **IP-adresstyp** är inställd på **Offentlig** under **IP-konfiguration för klientdel**. Kontrollera att **Skapa ny** har valts under **Offentlig IP-adress**. <br>Du kan konfigurera Frontend IP för att vara offentlig eller privat enligt ditt användningsområde. I det här exemplet väljer vi en offentlig Klientdels-IP. 
5. Ange *myAGPublicIPAddress* som den offentliga IP-adressens namn. 
6. Godkänn standardvärdena för de andra inställningarna och välj sedan **OK**.<br>Vi ska välja standardvärdena i den här artikeln för enkelhetens skull men du kan konfigurera anpassade värden för de andra inställningarna beroende på ditt användningsområde 

### <a name="summary-page"></a>Sammanfattningssida

Granska inställningarna på **sammanfattningssidan** och välj sedan **OK** för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen. Det kan ta flera minuter för Azure att skapa programgatewayen. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-backend-pool"></a>Lägg till serverdelspool

Serverdelspoolen används för att dirigera begäranden till backend-servrarna som ska tillhandahålla begäran. Serverdelspooler kan bestå av nätverkskort, VM-skalningsuppsättningar, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade namn (FQDN) och flera innehavare serverprogram som Azure App Service. Du måste lägga till backend-mål i en serverdelspool.

I det här exemplet använder vi virtuella datorer som mål-serverdelen. Vi kan använda befintliga virtuella datorer, eller så kan du skapa nya. I det här exemplet skapar vi två virtuella datorer som använder Azure som serverdelsservrar för application gateway. Detta gör att vi:

1. Skapa ett nytt undernät *myBackendSubnet*, i vilket de nya virtuella datorerna kommer att skapas. 
2. Skapa 2 nya virtuella datorer, *myVM* och *myVM2*, som ska användas som backend-servrarna.
3. Installera IIS på de virtuella datorerna för att kontrollera att application gateway har skapats.
4. Lägg till backend-servrarna till i serverdelspoolen.

### <a name="add-a-subnet"></a>Lägga till ett undernät

Lägg till ett undernät i det virtuella nätverket som du skapade genom att följa dessa steg:

1. Välj **Alla resurser** på den vänstra menyn på Azure-portalen, ange *myVNet* i sökrutan och välj sedan **myVNet** i sökresultatet.

2. Välj **Undernät** på den vänstra menyn och välj sedan **+ Undernät**. 

   ![Skapa undernät](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Från sidan **Lägg till undernät** anger du *myBackendSubnet* som **namn** på undernätet och väljer sedan **OK**.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** på Azure-portalen. Fönstret **Nytt** visas.
2. Välj **Beräkning** och sedan **Windows Server 2016 Datacenter** i listan **Aktuella**. Sidan **Skapa en virtuell dator** visas.<br>Application Gateway kan dirigera trafik till någon typ av virtuell dator som används i serverdelspoolen. I det här exemplet använder du en Windows Server 2016 Datacenter.
3. Ange dessa värden på fliken **Grundläggande inställningar** för följande inställningar för virtuella datorer:
   - **Resursgrupp**: Välj **myResourceGroupAG** som namn på resursgruppen.
   - **Namn på virtuell dator**: Ange *myVM* som namn på den virtuella datorn.
   - **Användarnamn**: Ange *azureuser* som administratörens användarnamn.
   - **Lösenord**: Ange *Azure123456!* som administratörslösenord.
4. Acceptera de övriga standardinställningarna och välj sedan **Nästa: Diskar**.  
5. Acceptera standardinställningarna på fliken **Diskar** och välj sedan **Nästa: Nätverk**.
6. På fliken **Nätverk** kontrollerar du att **myVNet** har valts för **Virtuellt nätverk** och att **Undernät** är inställt på **myBackendSubnet**. Acceptera de övriga standardinställningarna och välj sedan **Nästa: Hantering**.<br>Application Gateway kan kommunicera med instanser utanför det virtuella nätverket som den tillhör, men vi måste kontrollera att IP-anslutning. 
7. På fliken **Hantering** anger du **Startdiagnostik** till **Av**. Acceptera de övriga standardinställningarna och välj sedan **Granska + skapa**.
8. Gå igenom inställningarna på fliken **Granska + skapa** och åtgärda eventuella verifieringsfel och välj sedan **Skapa**.
9. Vänta på att skapandet av den virtuella datorn är klart innan du fortsätter.

### <a name="install-iis-for-testing"></a>Installera IIS för att testa

I det här exemplet installerar vi IIS på de virtuella datorerna endast i syfte att verifiera Azure har skapats application gateway. 

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

3. Skapa en andra virtuell dator och installera IIS genom att följa stegen som du utförde tidigare. Använd *myVM2* för virtuella datornamn och den **VMName** inställningen för den **Set-AzVMExtension** cmdlet.

### <a name="add-backend-servers-to-backend-pool"></a>Lägg till backend-servrarna till serverdelspoolen

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **Serverdelspooler** på den vänstra menyn. Azure skapade automatiskt en standardpool, **appGatewayBackendPool**, när du skapade programgatewayen. 

3. Välj **appGatewayBackendPool**.

4. Välj **Virtuell dator** i listrutan under **Mål**.

5. Under **VIRTUELL DATOR** och **NÄTVERKSGRÄNSSNITT** väljer du de virtuella datorerna **myVM** och **myVM2** och deras associerade nätverksgränssnitt i listrutorna.

   ![Lägga till serverdelsservrar](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Välj **Spara**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Skapa ett lagringskonto och konfigurera diagnostik

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

I den här självstudien används ett lagringskonto till att lagra data för identifiering och förebyggande åtgärder. Du kan även använda Azure Monitor-loggar eller Event Hub till att registrera data.

1. Klicka på **New** hittades på det övre vänstra hörnet i Azure-portalen.
2. Välj **Lagring** och sedan **Koppla undernät – blob, fil, tabell, kö**.
3. Ange namnet på lagringskontot, Välj **Använd befintlig** för resursgruppen och välj sedan **myResourceGroupAG**. I det här exemplet är namnet på lagringskontot *myagstore1*. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **skapa**.

### <a name="configure-diagnostics"></a>Konfigurera diagnostiken

Konfigurera diagnostik för registrering av data i loggarna ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog och ApplicationGatewayFirewallLog.

1. I den vänstra menyn, klickar du på **alla resurser**, och välj sedan *myAppGateway*.
2. Under övervakning klickar du på **diagnostikloggar**.
3. Klicka på **Lägg till diagnostikinställning**.
4. Ange *myDiagnosticsSettings* som namn på diagnostikinställningarna.
5. Välj **arkivet till ett lagringskonto**, och klicka sedan på **konfigurera** att välja den *myagstore1* storage-konto som du skapade tidigare.
6. Välj application gateway-loggar att samla in och behålla.
7. Klicka på **Spara**.

    ![Konfigurera diagnostiken](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>Testa programgatewayen

IIS krävs inte för skapande av programgatewayen, men du installerade det i den här snabbstarten för att kontrollera om Azure lyckades skapa programgatewayen. Använd IIS för att testa programgatewayen:

1. Hitta den offentliga IP-adressen för application gateway på dess **översikt** sidan.![ Registrera program gatewayens offentliga IP-adress](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)du kan även välja **alla resurser**, ange *myAGPublicIPAddress* i sökningen och väljer den i sökningen resultat. Azure visar den offentliga IP-adressen på sidan **Översikt**.
2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.
3. Kontrollera svaret. Ett giltigt svar verifierar att application gateway har skapats och är det kunna ansluta med serverdelen.![Testa programgatewayen](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen kan du ta bort resursgruppen. När du tar bort resursgruppen tas även programgatewayen och alla dess relaterade resurser bort. 

Så här tar du bort resursgruppen:

1. Välj **Resursgrupper** på den vänstra menyn på Azure-portalen.
2. På sidan **Resursgrupper** söker du efter och väljer **myResourceGroupAG**.
3. Välj **Ta bort resursgrupp** på sidan **Resursgrupp**.
4. Ange *myResourceGroupAG* för **Skriv resursgruppens namn** och välj sedan **Ta bort**

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa en programgateway med WAF aktiverat
> * Skapa de virtuella datorerna som används som backend-servrar
> * Skapa ett lagringskonto och konfigurera diagnostik

Om du vill veta mer om application gateway och deras associerade resurser kan du fortsätta i instruktionsartiklarna.
