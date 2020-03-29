---
title: HTTP till HTTPS-omdirigering i portalen - Azure Application Gateway
description: Lär dig hur du skapar en programgateway med omdirigerad trafik från HTTP till HTTPS med Hjälp av Azure-portalen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 51c191a7815bb64243e2324e150c00c2dcb7ec4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705334"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Skapa en programgateway med HTTP till HTTPS-omdirigering med Azure-portalen

Du kan använda Azure-portalen för att skapa en [programgateway](overview.md) med ett certifikat för SSL-avslutning. En routningsregel används för att omdirigera HTTP-trafik till HTTPS-porten i programgatewayen. I det här exemplet skapar du också en [skalningsuppsättning](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) för den virtuella datorn för serverdapoolen för programgatewayen som innehåller två instanser av virtuella datorer.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa ett självsignerat certifikat
> * Konfigurera ett nätverk
> * Skapa en programgateway med certifikatet
> * Lägga till en lyssnare och omdirigeringsregel
> * Skapa en VM-skalningsuppsättning med serverdelens standardpool

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här självstudien kräver azure PowerShell-modul version 1.0.0 eller senare för att skapa ett certifikat och installera IIS. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du vill köra kommandona i den `Login-AzAccount` här självstudien måste du också köra för att skapa en anslutning med Azure.

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

För produktionsanvändning bör du importera ett giltigt certifikat som signerats av en betrodd provider. I den här självstudiekursen skapar du ett självsignerat certifikat med [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). Du kan använda [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) med det tumavtryck som returnerades och exportera en pfx-fil från certifikatet.

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

1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
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
   - *10.0.0.0/24* – Undernätets adressutrymme.

     ![Skapa det virtuella nätverket](./media/create-url-route-portal/application-gateway-vnet.png)

7. Klicka på **OK** för att skapa det virtuella nätverket och undernätet.
8. Under **Frontend IP-konfiguration**kontrollerar du att **IP-adresstypen** är **Offentlig**och **Skapa ny** är markerad. Ange *myAGPublicIPAddress* för namnet. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
9. Under **Lyssnarkonfiguration**väljer du **HTTPS**och väljer sedan Välj **en fil** och navigera till filen *c:\appgwcert.pfx* och välj **Öppna**.
10. Skriv *appgwcert* för cert-namnet och *Azure123456!* som lösenord.
11. Lämna brandväggen för webbprogrammet inaktiverad och välj sedan **OK**.
12. Granska inställningarna på sammanfattningssidan och välj sedan **OK** för att skapa nätverksresurserna och programgatewayen. Det kan ta flera minuter innan programgatewayen skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="add-a-subnet"></a>Lägga till ett undernät

1. Välj **Alla resurser** på menyn till vänster och välj sedan **myVNet** i resurslistan.
2. Markera **Undernät**och klicka sedan på **Undernät**.

    ![Skapa undernät](./media/create-url-route-portal/application-gateway-subnet.png)

3. Skriv *myBackendSubnet* för namnet på undernätet.
4. Skriv *10.0.2.0/24* för adressintervallet och välj sedan **OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Lägga till en lyssnare och omdirigeringsregel

### <a name="add-the-listener"></a>Lägg till lyssnaren

Lägg först till lyssnaren som heter *myListener* för port 80.

1. Öppna resursgruppen **myResourceGroupAG** och välj **myAppGateway**.
2. Välj **Lyssnare** och välj sedan **+ Basic**.
3. Skriv *MyListener* för namnet.
4. Skriv *httpPort* för det nya klientdelsportnamnet och *80* för porten.
5. Kontrollera att protokollet är inställt på **HTTP**och välj sedan **OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Lägga till en routningsregel med en omdirigeringskonfiguration

1. På **myAppGateway**väljer du **Regler** och väljer sedan **+Routningsregel för begäran**.
2. Skriv *Rule2*för **namnet Rule2**.
3. Se till att **MyListener** är valt för lyssnaren.
4. Klicka på fliken **Backend-mål** och välj **Måltyp** som *omdirigering*.
5. För **omdirigeringstyp**väljer du **Permanent**.
6. För **omdirigeringsmål**väljer du **Lyssnare**.
7. Kontrollera att **target-lyssnaren** är inställd på **appGatewayHttpListener**.
8. För **include-frågesträngen** och **inkludera sökvägen** väljer du *Ja*.
9. Välj **Lägg till**.

## <a name="create-a-virtual-machine-scale-set"></a>Skapa en VM-skalningsuppsättning

I det här exemplet skapar du en VM-skalningsuppsättning som ska tillhandahålla servrar till serverdelspoolen i programgatewayen.

1. I portalens övre vänstra hörn väljer du **+Skapa en resurs**.
2. Välj **Compute**.
3. Skriv *skaluppsättningen* i sökrutan och tryck på Retur.
4. Välj **Skaluppsättning för virtuell dator**och välj sedan **Skapa**.
5. Skriv *myvmss*för **skalningsuppsättning för virtuell dator.**
6. För diskavbildning i operativsystemet,** se till att **Windows Server 2016 Datacenter** är valt.
7. För **resursgrupp**väljer du **myResourceGroupAG**.
8. För **Användarnamn**skriver du *azureuser*.
9. För **Lösenord**skriver du *Azure123456!* och bekräfta lösenordet.
10. För **antal instanser,** se till att värdet är **2**.
11. Välj **D2s_v3**för **Instansstorlek**.
12. Under **Nätverk**säkerställer du **att alternativ för utjämning av belastning** är inställt på Application **Gateway**.
13. Se till att **programgateway** är inställd på **myAppGateway**.
14. Se till att **undernätet** är inställt på **myBackendSubnet**.
15. Välj **Skapa**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Associera skalningsuppsättningen med rätt backend-pool

Portalgränssnittet för virtual machine scale set skapar en ny serverdelspool för skalningsuppsättningen, men du vill associera den med din befintliga appGatewayBackendPool.

1. Öppna resursgruppen **myResourceGroupAg.**
2. Välj **myAppGateway**.
3. Välj **Backend-pooler**.
4. Välj **myAppGatewaymyvmss**.
5. Välj **Ta bort alla mål från backend-poolen**.
6. Välj **Spara**.
7. När den här processen är klar väljer du backend-poolen **för myAppGatewaymyvmss,** väljer **Ta bort** och sedan **OK** för att bekräfta.
8. Välj **appGatewayBackendPool**.
9. Under **Mål**väljer du **VMSS**.
10. Under **VMSS**väljer du **myvmss**.
11. Under **Nätverksgränssnittskonfigurationer**väljer du **myvmssNic**.
12. Välj **Spara**.

### <a name="upgrade-the-scale-set"></a>Uppgradera skalningsuppsättningen

Slutligen måste du uppgradera skalningsuppsättningen med dessa ändringar.

1. Välj **skalan myvmss-skalan.**
2. Gå till **Inställningar** och välj **Instanser**.
3. Markera båda instanserna och välj sedan **Uppgradera**.
4. Välj **Ja** för att bekräfta.
5. När detta är klart, gå tillbaka till **myAppGateway** och välj **Backend pooler**. Du bör nu se att **appenGatewayBackendPool** har två mål, och **myAppGatewaymyvmss** har noll mål.
6. Välj **myAppGatewaymyvmss**och välj sedan **Ta bort**.
7. Välj **Ja** för att bekräfta.

### <a name="install-iis"></a>Installera IIS

Ett enkelt sätt att installera IIS på skalningsuppsättningen är att använda PowerShell. Klicka på ikonen Cloud Shell på portalen och se till att **PowerShell** är markerat.

Klistra in följande kod i PowerShell-fönstret och tryck på Retur.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>Uppgradera skalningsuppsättningen

När du har ändrat instanserna med IIS måste du återigen uppgradera skalningsuppsättningen med den här ändringen.

1. Välj **skalan myvmss-skalan.**
2. Gå till **Inställningar** och välj **Instanser**.
3. Markera båda instanserna och välj sedan **Uppgradera**.
4. Välj **Ja** för att bekräfta.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Du kan hämta programmets offentliga IP-adress från sidan översiktssida för programgateway.

1. Välj **myAppGateway**.
2. På sidan **Översikt** noterar du IP-adressen under **Frontend public IP-adress**.

3. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Till exempel, http://52.170.203.149

   ![Säkerhetsvarning](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Om du använde ett självsignerat certifikat och vill acceptera säkerhetsvarningen väljer du **Information** och sedan **Fortsätt till webbsidan**. Din skyddade IIS-webbplats visas sedan som i exemplet nedan:

   ![Testa basadressen i programgatewayen](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en programgateway med intern omdirigering](redirect-internal-site-powershell.md).
