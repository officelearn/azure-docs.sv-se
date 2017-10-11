---
title: "Omvänd DNS för Azure-tjänster | Microsoft Docs"
description: "Lär dig hur du konfigurerar omvänd DNS-sökning för tjänster i Azure"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.openlocfilehash: 63701e1ce0c1c6dcf2ce02ebce272b8280395e7f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Konfigurera omvänd DNS för tjänster i Azure

Den här artikeln förklarar hur du konfigurerar omvänd DNS-sökning för tjänster i Azure.

Tjänster i Azure använda IP-adresser tilldelas av Azure och som ägs av Microsoft. Dessa omvänd DNS-poster (PTR-poster) måste skapas i de motsvarande ägs av Microsoft omvänd DNS-zonerna för sökning. Den här artikeln förklarar hur du gör detta.

Det här scenariot ska inte förväxlas med möjlighet att [värd zoner omvänd DNS-sökning för din tilldelade IP-adressintervall i Azure DNS](dns-reverse-dns-hosting.md). I det här fallet måste IP-adressintervall som representeras av zon för omvänd sökning tilldelas för din organisation, vanligtvis av din Internetleverantör.

Innan du läser den här artikeln bör du vara bekant med den här [översikt över omvänd DNS- och support i Azure](dns-reverse-dns-overview.md).

Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md).
* I Resource Manager-distributionsmodellen exponeras beräkningsresurser (till exempel virtuella datorer, virtuella datorer eller Service Fabric-kluster) via en PublicIpAddress-resurs. Omvänd DNS-sökningar har konfigurerats med hjälp av egenskapen 'ReverseFqdn' för PublicIpAddress.
* I den klassiska distributionsmodellen exponeras beräkningsresurser med molntjänster. Omvänd DNS-sökningar har konfigurerats med hjälp av egenskapen 'ReverseDnsFqdn' för Molntjänsten.

Omvänd DNS stöds inte för Azure App Service.

## <a name="validation-of-reverse-dns-records"></a>Validering av omvänd DNS-poster

En tredje part ska inte skapa omvänd DNS-poster för sina Azure-mappning till DNS-domäner. Om du vill förhindra detta Azure bara kan skapas en omvänd DNS-post där domännamnet som anges i omvänd DNS-posten är samma som eller matchas till DNS-namn eller IP-adressen för en molntjänst eller en PublicIpAddress i samma Azure-prenumeration.

Verifieringen utförs endast när omvänd DNS-posten anges eller ändras. Periodiska ny verifiering utförs inte.

Exempel: Anta att PublicIpAddress-resursen har contosoapp1.northus.cloudapp.azure.com för DNS-namn och IP-adress 23.96.52.53. ReverseFqdn för PublicIpAddress kan anges som:
* DNS-namn för PublicIpAddress contosoapp1.northus.cloudapp.azure.com
* DNS-namn för en annan PublicIpAddress med samma prenumeration, till exempel contosoapp2.westus.cloudapp.azure.com
* En alternativa DNS-namn, till exempel app1.contoso.com, så länge det här namnet är *första* konfigurerad som en CNAME-post till contosoapp1.northus.cloudapp.azure.com eller till en annan PublicIpAddress med samma prenumeration.
* En alternativa DNS-namn, till exempel app1.contoso.com, så länge det här namnet är *första* konfigurerad som en A-post till IP-adressen 23.96.52.53 eller IP-adressen för en annan PublicIpAddress med samma prenumeration.

Samma begränsningar gäller för omvänd DNS för molntjänster.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Omvänd DNS för PublicIpAddress-resurser

Det här avsnittet innehåller detaljerade anvisningar om hur du konfigurerar omvänd DNS för PublicIpAddress resurser i Resource Manager-distributionsmodellen, med hjälp av Azure PowerShell, Azure CLI 1.0 eller 2.0 för Azure CLI. Konfigurera omvänd DNS för PublicIpAddress resurser stöds inte för närvarande via Azure portal.

Azure för närvarande har stöd för omvänd DNS endast för IPv4 PublicIpAddress resurser. Det finns inte stöd för IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Lägg till omvänd DNS i en befintlig PublicIpAddresses

#### <a name="powershell"></a>PowerShell

Lägga till omvänd DNS i en befintlig PublicIpAddress:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

Lägg till omvänd DNS i en befintlig PublicIpAddress som inte redan har ett DNS-namn, måste du också ange ett DNS-namn:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

Lägga till omvänd DNS i en befintlig PublicIpAddress:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Lägg till omvänd DNS i en befintlig PublicIpAddress som inte redan har ett DNS-namn, måste du också ange ett DNS-namn:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

Lägga till omvänd DNS i en befintlig PublicIpAddress:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Lägg till omvänd DNS i en befintlig PublicIpAddress som inte redan har ett DNS-namn, måste du också ange ett DNS-namn:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Skapa en offentlig IP-adress med omvänd DNS

Skapa en ny PublicIpAddress med omvänd DNS-egenskapen har redan angetts:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Visa omvända DNS för en befintlig PublicIpAddress

Visa det konfigurerade värdet för en befintlig PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Ta bort omvänd DNS från befintliga offentliga IP-adresser

Ta bort en omvänd DNS-egenskap från en befintlig PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Konfigurera omvänd DNS för molntjänster

Det här avsnittet innehåller detaljerade anvisningar om hur du konfigurerar omvänd DNS för molntjänster i den klassiska distributionsmodellen med hjälp av Azure PowerShell. Konfigurera omvänd DNS för molntjänster stöds inte via Azure portal, Azure CLI 1.0 eller 2.0 för Azure CLI.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Lägg till omvänd DNS i befintliga molntjänster

Lägga till en omvänd DNS-post i en befintlig molntjänst:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Skapa en molntjänst med omvänd DNS

Skapa en ny molntjänst med omvänd DNS-egenskapen har redan angetts:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Visa omvända DNS för befintliga molntjänster

Visa omvända DNS-egenskapen för en befintlig molntjänst:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Ta bort omvänd DNS från befintliga molntjänster

Ta bort en omvänd DNS-egenskap från en befintlig molntjänst:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="how-much-do-reverse-dns-records-cost"></a>Hur mycket omvänd DNS-poster kostnaden?

De är kostnadsfritt!  Det finns utan extra kostnad för omvänd DNS-poster eller frågor.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Löser omvänd DNS-poster från internet?

Ja. När du har angett egenskapen DNS för din Azure-tjänst, hanterar Azure alla DNS-delegeringar och DNS-zoner som krävs för att säkerställa att omvänd DNS-post matchas för alla användare på Internet.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Skapas standard omvänd DNS-poster för min Azure-tjänster?

Nej. Omvänd DNS är en opt-funktion. Ingen standard omvänd DNS-poster skapas om du inte väljer att konfigurera dem.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Vad är formatet för det fullständigt kvalificerade domännamnet (FQDN)?

FQDN anges i vanlig ordning och måste avslutas med en punkt (till exempel ”app1.contoso.com”.).

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Vad händer om valideringskontrollen för omvänd DNS jag har angett misslyckas?

Om omvänd DNS-verifieringen misslyckas, misslyckas åtgärden att konfigurera omvänd DNS-posten. Korrigera omvänd DNS-värde som krävs och försök igen.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Kan jag konfigurera omvänd DNS för Azure App Service?

Nej. Omvänd DNS stöds inte för Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Kan jag konfigurera flera omvänd DNS-poster för min Azure-tjänsten?

Nej. Azure stöder en omvänd DNS-post för varje Azure-molntjänst eller en PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Kan jag konfigurera omvänd DNS för IPv6-PublicIpAddress resurser?

Nej. Azure för närvarande har stöd för omvänd DNS endast för IPv4 PublicIpAddress resurser och molntjänster.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Kan jag skicka e-post till externa domäner från min Azure Compute-tjänster?

Nej. [Skicka e-post till externa domäner har inte stöd för Azure Compute-tjänster](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)

## <a name="next-steps"></a>Nästa steg

Läs mer om omvänd DNS [omvänd DNS-sökning på Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Lär dig hur du [värd zon för omvänd sökning för din ISP-tilldelad IP-adressintervall i Azure DNS](dns-reverse-dns-for-azure-services.md).

