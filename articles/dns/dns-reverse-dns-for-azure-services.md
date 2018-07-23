---
title: Omvänd DNS för Azure-tjänster | Microsoft Docs
description: Lär dig hur du konfigurerar omvänd DNS-sökning för tjänster som hanteras i Azure
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: 0ff14ec2100d47e0edc5288f1c46f4fdd63fa683
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171535"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Konfigurera omvänd DNS för tjänster som hanteras i Azure

Den här artikeln förklarar hur du konfigurerar omvänd DNS-sökning för tjänster som hanteras i Azure.

Tjänster i Azure använder IP-adresser som tilldelats av Azure och ägs av Microsoft. De här omvända DNS-poster (PTR-poster) måste skapas i de motsvarande ägs av Microsoft DNS-zonerna för omvänd sökning. Den här artikeln förklarar hur du gör detta.

Det här scenariot ska inte förväxlas med möjlighet att [vara värd för omvänd DNS-sökningszoner för dina tilldelade IP-adressintervall i Azure DNS](dns-reverse-dns-hosting.md). I det här fallet måste IP-adressintervall som representeras av zon för omvänd sökning tilldelas till din organisation, vanligtvis av din Internetleverantör.

Innan du läser den här artikeln bör du vara bekant med det [översikt över omvänd DNS- och stöd i Azure](dns-reverse-dns-overview.md).

Compute-resurser (till exempel virtuella datorer, skalningsuppsättningar för virtuella datorer eller Service Fabric-kluster) blir tillgängliga via en PublicIpAddress-resurs i Azure DNS. Omvänd DNS-sökning är konfigurerade med hjälp av egenskapen 'Värdet för ReverseFqdn' för PublicIpAddress.


Omvänd DNS stöds inte för Azure App Service.

## <a name="validation-of-reverse-dns-records"></a>Verifiering av omvända DNS-poster

En tredje part bör inte kunna skapa omvända DNS-poster för deras Azure-tjänst-mappning till dina DNS-domäner. Om du vill förhindra detta Azure endast tillåter skapandet av en omvänd DNS-post där domännamnet som angetts i omvänd DNS-posten är samma som eller matchas till DNS-namn eller IP-adressen för en PublicIpAddress eller en molntjänst i samma Azure-prenumeration.

Verifieringen utförs endast när omvänd DNS-posten har angetts eller ändrats. Periodiska nytt verifieringen utförs inte.

Till exempel: Anta att PublicIpAddress-resursen har contosoapp1.northus.cloudapp.azure.com för DNS-namn och IP-adress 23.96.52.53. Värdet för ReverseFqdn för PublicIpAddress kan anges som:
* DNS-namnet för PublicIpAddress contosoapp1.northus.cloudapp.azure.com
* DNS-namnet för en annan PublicIpAddress i samma prenumeration, till exempel contosoapp2.westus.cloudapp.azure.com
* En anpassad DNS-namn, till exempel app1.contoso.com, så länge som det här namnet är *första* konfigurerad som en CNAME-post till contosoapp1.northus.cloudapp.azure.com eller till en annan PublicIpAddress i samma prenumeration.
* En anpassad DNS-namn, till exempel app1.contoso.com, så länge som det här namnet är *första* konfigurerad som en A-post till IP-adressen 23.96.52.53 eller IP-adressen för en annan PublicIpAddress i samma prenumeration.

Samma begränsningar gäller för att omvänd DNS för molntjänster.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Omvänd DNS för PublicIpAddress-resurser

Det här avsnittet innehåller detaljerade anvisningar för hur du konfigurerar omvänd DNS för PublicIpAddress-resurser i Resource Manager-distributionsmodellen, med hjälp av Azure PowerShell, Azure CLI 1.0 eller Azure CLI 2.0. Konfigurera omvänd DNS för PublicIpAddress-resurser stöds inte för närvarande via Azure portal.

Azure för närvarande har stöd för omvänd DNS endast för IPv4 PublicIpAddress-resurser. Det finns inte stöd för IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Lägg till omvänd DNS i en befintlig PublicIpAddresses

#### <a name="powershell"></a>PowerShell

Lägga till omvänd DNS i en befintlig PublicIpAddress:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

Om du vill lägga till omvänd DNS i en befintlig PublicIpAddress som inte redan har ett DNS-namn, måste du även ange ett DNS-namn:

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

Om du vill lägga till omvänd DNS i en befintlig PublicIpAddress som inte redan har ett DNS-namn, måste du även ange ett DNS-namn:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

Lägga till omvänd DNS i en befintlig PublicIpAddress:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Om du vill lägga till omvänd DNS i en befintlig PublicIpAddress som inte redan har ett DNS-namn, måste du även ange ett DNS-namn:

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

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Visa omvänd DNS för en befintlig PublicIpAddress

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


## <a name="configure-reverse-dns-for-cloud-services"></a>Konfigurera omvänd DNS för Cloud Services

Det här avsnittet innehåller detaljerade anvisningar för hur du konfigurerar omvänd DNS för molntjänster i den klassiska distributionsmodellen med hjälp av Azure PowerShell. Konfigurera omvänd DNS för Cloud Services stöds inte via Azure-portalen, Azure CLI 1.0 eller Azure CLI 2.0.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Lägg till omvänd DNS i befintliga Cloud Services

Lägga till en omvänd DNS-post till en befintlig molntjänst:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Skapa en molntjänst med omvänd DNS

Skapa en ny molntjänst med omvänd DNS-egenskapen har redan angetts:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Visa omvänd DNS för befintliga Cloud Services

Visa omvända DNS-egenskapen för en befintlig molntjänst:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Ta bort omvänd DNS från den befintliga Cloud Services

Ta bort en omvänd DNS-egenskap från en befintlig molntjänst:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="how-much-do-reverse-dns-records-cost"></a>Hur mycket omvänd DNS-poster kostnader?

De är helt gratis!  Det finns ingen extra kostnad för omvänd DNS-poster eller frågor.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Löser min omvända DNS-poster från internet?

Ja. När du har angett egenskapen omvänd DNS för Azure service, hanterar Azure alla DNS-delegering och DNS-zoner som krävs för att säkerställa att omvänd DNS-posten matchar för alla Internet-användare.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Skapas standard omvända DNS-poster för min Azure-tjänster?

Nej. Omvänd DNS är en valbar funktion. Ingen standard omvända DNS-poster skapas om du väljer att inte konfigureras.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Vad är formatet för fullständigt kvalificerade domännamnet (FQDN)?

Fullständiga domännamn har angetts i ordningsföljd framåt och måste avslutas med en punkt (till exempel ”app1.contoso.com”.).

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Vad händer om valideringskontrollen för omvänd DNS som jag har angett misslyckas?

Om omvänd DNS-verifieringen misslyckas, misslyckas åtgärden att konfigurera omvänd DNS-posten. Korrigera omvänd DNS-värdet som krävs och försök igen.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Kan jag konfigurera omvänd DNS för Azure App Service?

Nej. Omvänd DNS stöds inte för Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Kan jag konfigurera flera omvända DNS-poster för min Azure-tjänsten?

Nej. Azure stöder en enskild omvänd DNS-post för varje Azure-molntjänst eller en PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Kan jag konfigurera omvänd DNS för IPv6 PublicIpAddress-resurser?

Nej. Azure för närvarande har stöd för omvänd DNS endast för IPv4 PublicIpAddress-resurser och molntjänster.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Kan jag skicka e-postmeddelanden till externa domäner från min Azure Compute services?

Teknisk möjlighet att skicka e-post direkt från en Azure-distribution beror på vilken prenumeration. Microsoft rekommenderar oavsett prenumerationstyp, skicka utgående e-post med hjälp av betrodda e relay-tjänster. Mer information finns i [förbättrad säkerhet i Azure för att skicka e-post – November 2017 Update](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>Nästa steg

Läs mer om omvänd DNS [omvänd DNS-sökning på Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Lär dig hur du [värdar för zonen för omvänd sökning för din ISP-tilldelad IP-adressintervall i Azure DNS](dns-reverse-dns-for-azure-services.md).

