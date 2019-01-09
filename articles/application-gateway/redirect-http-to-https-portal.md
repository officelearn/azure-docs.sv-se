---
title: Skapa en Programgateway med HTTP till HTTPS-omdirigering i Azure Portal
description: Lär dig hur du skapar en Programgateway med omdirigerade trafik från HTTP till HTTPS med Azure-portalen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 12/7/2018
ms.author: victorh
ms.openlocfilehash: a10314d4c396298f018459e56252d0d0d1656f08
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107794"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Skapa en Programgateway med HTTP till HTTPS-omdirigering i Azure Portal

Du kan använda Azure-portalen för att skapa en [Programgateway](overview.md) med ett certifikat för SSL-avslutning. En routningsregel används för att omdirigera HTTP-trafik till HTTPS-porten i application gateway. I det här exemplet kan du också skapa en [virtual machine scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) för serverdelspoolen för application gateway som innehåller två instanser av virtuella datorer.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa ett självsignerat certifikat
> * Konfigurera ett nätverk
> * Skapa en programgateway med certifikatet
> * Lägga till en lyssnare och omdirigering av regel
> * Skapa en VM-skalningsuppsättning med serverdelens standardpool

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Den här självstudien kräver Azure PowerShell Modulversion 3.6 eller senare för att skapa ett certifikat och installera IIS. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). För att köra kommandona i den här självstudien, behöver du också köra `Login-AzureRmAccount` att skapa en anslutning till Azure.

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

För produktion bör du importera ett giltigt certifikat som signerats av en betrodd provider. I den här självstudiekursen skapar du ett självsignerat certifikat med [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). Du kan använda [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) med det tumavtryck som returnerades och exportera en pfx-fil från certifikatet.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Du bör se något som liknar det här resultatet:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Använd tumavtrycket till att skapa pfx-filen:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Det krävs ett virtuellt nätverk för kommunikation mellan de resurser som du skapar. I det här exemplet skapas två undernät: ett för programgatewayen och ett annat för serverdelen. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
3. Välj **Nätverk** och sedan **Application Gateway** i listan Aktuella.
4. Ange följande värden för programgatewayen:

    - *myAppGateway* – Namnet på programgatewayen.
    - *myResourceGroupAG* – Den nya resursgruppen.

    ![Skapa en ny programgateway](./media/create-url-route-portal/application-gateway-create.png)

5. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
6. Klicka på **Välj ett virtuellt nätverk**, klicka på **Skapa nytt** och ange sedan följande värden för det virtuella nätverket:

    - *myVnet* – Det virtuella nätverkets namn.
    - *10.0.0.0/16* – Det virtuella nätverkets adressutrymme.
    - *myBackendSubnet* – Undernätsnamnet.
    - *10.0.1.0/24* – för undernätsadressutrymmet.

    ![Skapa det virtuella nätverket](./media/create-url-route-portal/application-gateway-vnet.png)

7. Klicka på **OK** för att skapa det virtuella nätverket och undernätet.
8. Under **Frontend-IP-konfiguration**, se till att **IP-adresstyp** är **offentliga**, och **Skapa ny** har valts. Ange *myAGPublicIPAddress* för namnet. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
9. Under **Lyssnarkonfigurationen**väljer **HTTPS**och välj sedan **Välj en fil** och navigera till den *c:\appgwcert.pfx* fil och Välj **öppna**.
10. Typ *appgwcert* för cert-namn och *Azure123456!* som lösenord.
11. Lämna inaktiverad brandväggen för webbaserade program och välj sedan **OK**.
12. Granska inställningarna på sidan Sammanfattning och välj sedan **OK** att skapa nätverksresurser och application gateway. Det kan ta flera minuter för application gateway kan skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="add-a-subnet"></a>Lägga till ett undernät

1. Välj **alla resurser** i den vänstra menyn och välj sedan **myVNet** i resurslistan.
2. Välj **undernät**, och klicka sedan på **undernät**.

    ![Skapa undernät](./media/create-url-route-portal/application-gateway-subnet.png)

3. Typ *myBackendSubnet* för namnet på undernätet.
4. Typ *10.0.2.0/24* för adressintervallet och välj sedan **OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Lägga till en lyssnare och omdirigering av regel

### <a name="add-the-listener"></a>Lägg till lyssnaren

Lägg först till lyssnaren med namnet *myListener* för port 80.

1. Öppna den **myResourceGroupAG** resource gruppen och välj **myAppGateway**.
2. Välj **lyssnare** och välj sedan **+ grundläggande**.
3. Typ *MyListener* för namnet.
4. Typ *httpPort* för det nya namnet för frontend-port och *80* för porten.
5. Kontrollera protokollet är inställd på **HTTP**, och välj sedan **OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Lägg till en regel för vidarebefordran med en omdirigeringskonfiguration av

1. På **myAppGateway**väljer **regler** och välj sedan **+ grundläggande**.
2. För den **namn**, typ *2*.
3. Se till att **MyListener** har valts för lyssnaren.
4. Välj den **Konfigurera omdirigering** markerar du kryssrutan.
5. För **omdirigeringstyp**väljer **Permanent**.
6. För **omdirigering av mål**väljer **lyssnare**.
7. Se till att den **mållyssnare** är inställd på **appGatewayHttpListener**.
8. Välj den **ta med frågesträng** och **sökvägen** kryssrutorna.
9. Välj **OK**.

## <a name="create-a-virtual-machine-scale-set"></a>Skapa en VM-skalningsuppsättning

I det här exemplet skapar du en VM-skalningsuppsättning som ska tillhandahålla servrar till serverdelspoolen i programgatewayen.

1. På portalen övre vänstra hörnet, väljer **+ skapa en resurs**.
2. Välj **Compute**.
3. I sökrutan skriver *skalningsuppsättning* och tryck på RETUR.
4. Välj **Virtual machine scale Sets**, och välj sedan **skapa**.
5. För **namn för VM-skalningsuppsättningen**, typ *myvmss*.
6. För avbildning av operativsystemdisk, ** kontrollera **Windows Server 2016 Datacenter** har valts.
7. För **resursgrupp**väljer **myResourceGroupAG**.
8. För **användarnamn**, typ *azureuser*.
9. För **lösenord**, typ *Azure123456!* och bekräfta lösenordet.
10. För **antal instanser**, se till att värdet är **2**.
11. För **Instansstorlek**väljer **D2s_v3**.
12. Under **nätverk**, se till att **Välj Belastningsutjämningsalternativ** är inställd på **Application Gateway**.
13. Se till att **programgatewayen** är inställd på **myAppGateway**.
14. Se till att **undernät** är inställd på **myBackendSubnet**.
15. Välj **Skapa**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Associera skalningsuppsättning med rätt serverdelspoolen

VM scale set portalens användargränssnitt skapar en ny serverdelspool för skalningsuppsättningen, men du vill associera det med dina befintliga appGatewayBackendPool.

1. Öppna den **myResourceGroupAg** resursgrupp.
2. Välj **myAppGateway**.
3. Välj **serverdelspooler**.
4. Välj **myAppGatewaymyvmss**.
5. Välj **ta bort alla mål från serverdelspoolen**.
6. Välj **Spara**.
7. När den här processen är klar väljer du den **myAppGatewaymyvmss** serverdelspoolen, Välj **ta bort** och sedan **OK** att bekräfta.
8. Välj **appGatewayBackendPool**.
9. Under **mål**väljer **VMSS**.
10. Under **VMSS**väljer **myvmss**.
11. Under **Nätverksgränssnittskonfigurationer**väljer **myvmssNic**.
12. Välj **Spara**.

### <a name="upgrade-the-scale-set"></a>Uppgradera skalningsuppsättningen

Slutligen måste du uppgradera skalningsuppsättning med de här ändringarna.

1. Välj den **myvmss** skalningsuppsättning.
2. Under **inställningar**väljer **instanser**.
3. Välj båda instanserna och välj sedan **uppgradera**.
4. Bekräfta genom att välja **Ja**.
5. När du är klar går du tillbaka till den **myAppGateway** och välj **serverdelspooler**. Du bör nu se att den **appGatewayBackendPool** har två mål och **myAppGatewaymyvmss** har noll mål.
6. Välj **myAppGatewaymyvmss**, och välj sedan **ta bort**.
7. Välj **OK** att bekräfta.

### <a name="install-iis"></a>Installera IIS

Ett enkelt sätt att installera IIS på skalningsuppsättningen är att använda PowerShell. Klicka på ikonen för Cloud Shell från portalen och se till att **PowerShell** har valts.

Klistra in följande kod i PowerShell-fönstret och tryck på RETUR.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzureRmVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>Uppgradera skalningsuppsättningen

När du har ändrat instanser med IIS, måste du uppgradera skalningsuppsättning med den här ändringen igen.

1. Välj den **myvmss** skalningsuppsättning.
2. Under **inställningar**väljer **instanser**.
3. Välj båda instanserna och välj sedan **uppgradera**.
4. Bekräfta genom att välja **Ja**.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Du kan hämta den offentliga IP-adressen för programmet från översiktssidan för application gateway.

1. Välj **myAppGateway**.
2. På den **översikt** , Lägg den IP-adressen under **klientdel offentliga IP-adressen**.

3. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Till exempel, http://52.170.203.149

   ![Säkerhetsvarning](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Om du använde ett självsignerat certifikat och vill acceptera säkerhetsvarningen väljer du **Information** och sedan **Fortsätt till webbsidan**. Din skyddade IIS-webbplats visas sedan som i exemplet nedan:

   ![Testa basadressen i programgatewayen](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapa en Programgateway med intern omdirigering](redirect-internal-site-powershell.md).