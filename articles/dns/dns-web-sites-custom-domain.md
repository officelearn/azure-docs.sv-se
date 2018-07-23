---
title: Skapa anpassade DNS-poster för en webbapp | Microsoft Docs
description: Hur du skapar anpassade domän DNS-poster för webbapp med Azure DNS.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: victorh
ms.openlocfilehash: f24c301cea5ef91d101206e71b69b7ceb03b0282
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172457"
---
# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Skapa DNS-poster för en webbapp i en anpassad domän

Du kan använda Azure DNS för att vara värd för en anpassad domän för dina webbprogram. Exempelvis kan du skapar en Azure webbapp och du vill att användarna ska komma åt den genom att antingen använda contoso.com eller www.contoso.com som ett fullständigt domännamn.

Om du vill göra detta måste du skapa två poster:

* En ”A”-rotpost som pekar på contoso.com
* En ”CNAME”-post för www-namn som pekar på A-post

Tänk på att om du skapar en A-post för en webbapp i Azure, A-posten måste vara manuellt uppdatera om den underliggande IP-adressen för web app-ändringar.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar måste du först skapa en DNS-zon i Azure DNS och delegera zonen i Registratorn till Azure DNS.

1. Om du vill skapa en DNS-zon, följer du stegen i [skapa en DNS-zon](dns-getstarted-create-dnszone.md).
2. Om du vill delegera din DNS till Azure DNS, följer du stegen i [DNS domändelegering](dns-domain-delegation.md).

När du skapar en zon och delegera till Azure DNS kan skapa du sedan poster för din anpassade domän.

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Skapa en A-post för din anpassade domän

En A-post används för att mappa ett namn till dess IP-adress. I följande exempel vi tilldelar \@ som en A-post till en IPv4-adress:

### <a name="step-1"></a>Steg 1

Skapa en A-post och tilldela en variabel $rs

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>Steg 2

Lägga till IPv4-värde i den tidigare skapade postuppsättningen ”\@” med hjälp av $rs variabeln som tilldelats. IPv4-värdet som tilldelas kommer att IP-adressen för din webbapp.

För att hitta IP-adressen för en webbapp, följer du stegen i [konfigurera ett anpassat domännamn i Azure App Service](../app-service/app-service-web-tutorial-custom-domain.md).

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>Steg 3

Ändringarna i postuppsättningen. Använd `Set-AzureRMDnsRecordSet` att överföra ändringarna till posten till Azure DNS:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Skapa en CNAME-post för din anpassade domän

Om din domän är redan hanteras av Azure DNS (se [DNS domändelegering](dns-domain-delegation.md), du kan använda följande exempel för att skapa en CNAME-post för contoso.azurewebsites.net.

### <a name="step-1"></a>Steg 1

Öppna PowerShell och skapa en ny CNAME-postuppsättning och tilldela en variabel $rs. Det här exemplet skapar en postuppsättning typ CNAME-post med en ”time to live” 600 sekunder i DNS-zon med namnet ”contoso.com”.

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
```

Följande exempel är svaret.

```
Name              : www
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Steg 2

När CNAME-postuppsättning skapas, måste du skapa ett Aliasvärde som pekar till webbappen.

Med hjälp av tidigare tilldelade variabeln ”$rs” kan du använda PowerShell-kommandot nedan för att skapa alias för web app contoso.azurewebsites.net.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
```

Följande exempel är svaret.

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Steg 3

Genomför ändringarna med den `Set-AzureRMDnsRecordSet` cmdlet:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

Du kan verifiera posten skapades korrekt genom att fråga den ”www.contoso.com” med nslookup, enligt nedan:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net
```

## <a name="create-an-awverify-record-for-web-apps"></a>Skapa en ”awverify”-post för web apps

Om du vill använda en A-post för din webbapp måste du gå igenom en verifieringen för att se till att du äger den anpassade domänen. Den här verifieringssteg görs genom att skapa en särskild CNAME-post med namnet ”awverify”. Det här avsnittet gäller enbart poster.

### <a name="step-1"></a>Steg 1

Skapa ”awverify”-post. I exemplet nedan skapar vi ”aweverify”-post för contoso.com och verifiera ditt ägarskap för den anpassade domänen.

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "myresourcegroup" -Name "awverify" -RecordType "CNAME" -Ttl 600
```

Följande exempel är svaret.

```
Name              : awverify
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Steg 2

När postuppsättning ”awverify” har skapats, tilldelar du den CNAME-postuppsättning alias. I exemplet nedan kommer vi tilldela alias inställd awverify.contoso.azurewebsites.net CNAMe-posten.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
```

Följande exempel är svaret.

```
    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Steg 3

Genomför ändringarna med den `Set-AzureRMDnsRecordSet cmdlet`som visas i kommandot nedan.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>Nästa steg

Följ stegen i [konfigurera ett anpassat domännamn för App Service](../app-service/app-service-web-tutorial-custom-domain.md) att konfigurera webbappen för att använda en anpassad domän.
