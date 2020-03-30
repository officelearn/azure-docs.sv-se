---
title: Omvänd DNS för Azure-tjänster - Azure DNS
description: Med den här utbildningssökvägen kommer du igång med att konfigurera omvända DNS-sökninger för tjänster som finns i Azure.
services: dns
documentationcenter: na
author: rohinkoul
manager: KumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 073e84ece11f6817bfe2c5a94735ec6e16dac4fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932377"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Konfigurera omvänd DNS för tjänster som finns i Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I den här artikeln beskrivs hur du konfigurerar omvända DNS-sökningar för tjänster som finns i Azure.

Tjänster i Azure använder IP-adresser som tilldelats av Azure och ägs av Microsoft. Dessa omvända DNS-poster (PTR-poster) måste skapas i motsvarande Microsoft-ägda omvänd DNS-sökningszoner. I den här artikeln beskrivs hur du gör detta.

Det här scenariot bör inte förväxlas med möjligheten att [vara värd för de omvända DNS-sökzonerna för dina tilldelade IP-intervall i Azure DNS](dns-reverse-dns-hosting.md). I det här fallet måste IP-intervallen som representeras av zonen för omvänd sökning tilldelas din organisation, vanligtvis av din Isp.

Innan du läser den här artikeln bör du känna till den här [översikten över omvänd DNS och support i Azure](dns-reverse-dns-overview.md).

I Azure DNS visas beräkningsresurser (till exempel virtuella datorer, skalningsuppsättningar för virtuella datorer eller Kluster för tjänstinfrastruktur) via en PublicIpAddress-resurs. Omvänd DNS-sökning konfigureras med egenskapen ReverseFqdn för PublicIpAddress.


Omvänd DNS stöds för närvarande inte för Azure App Service.

## <a name="validation-of-reverse-dns-records"></a>Validering av omvända DNS-poster

En tredje part bör inte kunna skapa omvända DNS-poster för sin Azure-tjänstmappning till dina DNS-domäner. För att förhindra detta tillåter Azure endast skapandet av en omvänd DNS-post där domännamnet som anges i den omvända DNS-posten är samma som eller matchar till DNS-namnet eller IP-adressen för en PublicIpAddress eller Cloud Service i samma Azure-prenumeration.

Den här valideringen utförs endast när den omvända DNS-posten har angetts eller ändrats. Periodisk omvalidning utförs inte.

Anta till exempel: anta att PublicIpAddress-resursen har DNS-namnet contosoapp1.northus.cloudapp.azure.com och IP-adressen 23.96.52.53. ReverseFqdn för PublicIpAddress kan anges som:
* DNS-namnet för PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* DNS-namnet för en annan PublicIpAddress i samma prenumeration, till exempel contosoapp2.westus.cloudapp.azure.com
* Ett fåfänga DNS-namn, till exempel app1.contoso.com, så länge det här namnet *först* konfigureras som ett CNAME för att contosoapp1.northus.cloudapp.azure.com, eller till en annan PublicIpAddress i samma prenumeration.
* Ett fåfänga DNS-namn, till exempel app1.contoso.com, så länge det här namnet *först* konfigureras som en A-post till IP-adressen 23.96.52.53, eller till IP-adressen för en annan PublicIpAddress i samma prenumeration.

Samma begränsningar gäller för omvänd DNS för molntjänster.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Omvänd DNS för publicipaddress-resurser

Det här avsnittet innehåller detaljerade instruktioner för hur du konfigurerar omvänd DNS för PublicIpAddress-resurser i Resource Manager-distributionsmodellen, med antingen Azure PowerShell, Azure classic CLI eller Azure CLI. Det stöds för närvarande inte att konfigurera omvänd DNS för PublicIpAddress-resurser via Azure-portalen.

Azure stöder för närvarande omvänd DNS endast för IPv4 PublicIpAddress-resurser. Det stöds inte för IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Lägga till omvänd DNS i befintliga PublicIpAddresses

#### <a name="powershell"></a>PowerShell

Så här uppdaterar du omvänd DNS till en befintlig PublicIpAddress:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

Om du vill lägga till omvänd DNS till en befintlig PublicIpAddress som inte redan har ett DNS-namn måste du också ange ett DNS-namn:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

Så här lägger du till omvänd DNS i en befintlig PublicIpAddress:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Om du vill lägga till omvänd DNS till en befintlig PublicIpAddress som inte redan har ett DNS-namn måste du också ange ett DNS-namn:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

Så här lägger du till omvänd DNS i en befintlig PublicIpAddress:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Om du vill lägga till omvänd DNS till en befintlig PublicIpAddress som inte redan har ett DNS-namn måste du också ange ett DNS-namn:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Skapa en offentlig IP-adress med omvänd DNS

Så här skapar du en ny PublicIpAddress med den omvända DNS-egenskapen som redan har angetts:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Visa omvänd DNS för en befintlig PublicIpAddress

Så här visar du det konfigurerade värdet för en befintlig PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Ta bort omvänd DNS från befintliga offentliga IP-adresser

Så här tar du bort en omvänd DNS-egenskap från en befintlig PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Konfigurera omvänd DNS för molntjänster

Det här avsnittet innehåller detaljerade instruktioner för hur du konfigurerar omvänd DNS för Molntjänster i den klassiska distributionsmodellen med Azure PowerShell. Det går inte att konfigurera omvänd DNS för Molntjänster via Azure-portalen, Azure Classic CLI eller Azure CLI.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Lägga till omvänd DNS till befintliga molntjänster

Så här lägger du till en omvänd DNS-post i en befintlig molntjänst:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Skapa en molntjänst med omvänd DNS

Så här skapar du en ny molntjänst med den omvända DNS-egenskapen redan angiven:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Visa omvänd DNS för befintliga molntjänster

Så här visar du den omvända DNS-egenskapen för en befintlig molntjänst:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Ta bort omvänd DNS från befintliga molntjänster

Så här tar du bort en omvänd DNS-egenskap från en befintlig molntjänst:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="how-much-do-reverse-dns-records-cost"></a>Hur mycket kostar omvända DNS-poster?

De är fria!  Det finns ingen extra kostnad för omvända DNS-poster eller frågor.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Kommer mina omvända DNS-poster att lösa från internet?

Ja. När du har angett den omvända DNS-egenskapen för din Azure-tjänst hanterar Azure alla DNS-delegeringar och DNS-zoner som krävs för att säkerställa att omvänd DNS-post matchas för alla Internet-användare.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Skapas standardalternativ för omvänd DNS-poster för mina Azure-tjänster?

Nej. Omvänd DNS är en opt-in-funktion. Inga standardrepdrings-DNS-poster skapas om du väljer att inte konfigurera dem.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Vilket är formatet för det fullständigt kvalificerade domännamnet (FQDN)?

FQDN anges i framåt ordning och måste avslutas med en punkt (till exempel "app1.contoso.com.").

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Vad händer om valideringskontrollen för den omvända DNS som jag har angett misslyckas?

Om den omvända DNS-valideringskontrollen misslyckas misslyckas åtgärden för att konfigurera den omvända DNS-posten. Korrigera det omvända DNS-värdet efter behov och försök igen.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Kan jag konfigurera omvänd DNS för Azure App Service?

Nej. Omvänd DNS stöds inte för Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Kan jag konfigurera flera omvända DNS-poster för min Azure-tjänst?

Nej. Azure stöder en enda omvänd DNS-post för varje Azure Cloud Service eller PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Kan jag konfigurera omvänd DNS för IPv6 PublicIpAddress-resurser?

Nej. Azure stöder för närvarande omvänd DNS endast för IPv4 PublicIpAddress-resurser och Molntjänster.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Kan jag skicka e-postmeddelanden till externa domäner från mina Azure Compute-tjänster?

Den tekniska möjligheten att skicka e-post direkt från en Azure-distribution beror på prenumerationstypen. Oavsett prenumerationstyp rekommenderar Microsoft att du använder betrodda relätjänster för e-post för att skicka utgående e-post. Mer information finns i [Förbättrad Azure-säkerhet för att skicka e-postmeddelanden – november 2017 Update](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>Nästa steg

Mer information om omvänd DNS finns i [omvänd DNS-sökning på Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Lär dig hur du [är värd för zonen för omvänd sökning för ditt IP-intervall som tilldelats isp i Azure DNS](dns-reverse-dns-for-azure-services.md).

