---
title: Skapa anpassade DNS-poster för en webbapp | Microsoft Docs
description: Så här skapar du anpassade domäner DNS-poster för webbapp med Azure DNS.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: kumud
ms.openlocfilehash: 00a56a2683e95e70bb13acd6b936e766f044e1cd
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Skapa DNS-poster för ett webbprogram i en anpassad domän

Du kan använda DNS för Azure som värd för en anpassad domän för ditt webbprogram. Till exempel du skapar en Azure webbapp och du vill att användarna ska komma åt den genom att antingen använda contoso.com eller www.contoso.com som ett fullständigt domännamn.

Om du vill göra detta måste du skapa två poster:

* En rot ”A”-post som pekar på contoso.com
* En ”CNAME” för www-namn som pekar på A-post

Kom ihåg att om du skapar en A-post för en webbapp i Azure, A-posten måste vara manuellt uppdateras om den underliggande IP-adressen för web app-ändringar.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar måste du först skapa en DNS-zon i Azure DNS och delegera zonen i Registratorn till Azure DNS.

1. Om du vill skapa en DNS-zon, följer du stegen i [skapa en DNS-zon](dns-getstarted-create-dnszone.md).
2. Om du vill delegera din DNS-server till Azure DNS, följer du stegen i [DNS-delegering i domänen](dns-domain-delegation.md).

När du skapar en zon och delegera till Azure DNS kan skapar du sedan poster för domänen.

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Skapa en A-post för den anpassade domänen

En A-post används för att mappa ett namn till dess IP-adress. I följande exempel kommer vi tilldela som en A-post till en IPv4-adress:

### <a name="step-1"></a>Steg 1

Skapa en A-post och tilldela en variabel $rs

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>Steg 2

Lägg till IPv4-värde i den tidigare skapade postuppsättningen ”@” med hjälp av variabeln $rs tilldelas. IPv4-värdet som tilldelas kommer att IP-adressen för ditt webbprogram.

För att hitta IP-adressen för en webbapp, följer du stegen i [konfigurera ett anpassat domännamn i Azure App Service](../app-service/app-service-web-tutorial-custom-domain.md).

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>Steg 3

Ändringarna i uppsättningen av poster. Använd `Set-AzureRMDnsRecordSet` så att ändringarna till posten till Azure DNS:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Skapa en CNAME-post för den anpassade domänen

Om din domän hanteras redan av Azure DNS (se [DNS-delegering i domänen](dns-domain-delegation.md), så kan du använda följande exempel för att skapa en CNAME-post för contoso.azurewebsites.net.

### <a name="step-1"></a>Steg 1

Öppna PowerShell och skapa en ny CNAME-postuppsättning och tilldela en variabel $rs. Det här exemplet skapas en postuppsättning typen CNAME med en ”time to live” 600 sekunder i DNS-zonen med namnet ”contoso.com”.

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

När du har skapat CNAME-postuppsättning måste du skapa ett Aliasvärde som pekar på webbprogrammet.

Med hjälp av variabeln tidigare tilldelade ”$rs” använda du PowerShell-kommandot nedan för att skapa alias för web app contoso.azurewebsites.net.

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

Genomför ändringarna med det `Set-AzureRMDnsRecordSet` cmdlet:

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

## <a name="create-an-awverify-record-for-web-apps"></a>Skapa en ”awverify” post för webbprogram

Om du vill använda en A-post för ditt webbprogram måste du gå igenom en verifieringsprocessen för att säkerställa att du äger den anpassade domänen. Den här kontrollen görs genom att skapa en särskild CNAME-post med namnet ”awverify”. Det här avsnittet gäller enbart poster.

### <a name="step-1"></a>Steg 1

Skapa posten ”awverify”. I exemplet nedan skapar vi ”aweverify” post för contoso.com verifiera ägarskapet för den anpassade domänen.

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

Tilldela den CNAME-postuppsättning alias när postuppsättning ”awverify” har skapats. I exemplet nedan tilldelas vi ange alias till awverify.contoso.azurewebsites.net CNAMe-posten.

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

Genomför ändringarna med det `Set-AzureRMDnsRecordSet cmdlet`som visas i kommandot nedan.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>Nästa steg

Följ stegen i [konfigurera ett anpassat domännamn för Apptjänst](../app-service/app-service-web-tutorial-custom-domain.md) att konfigurera ditt webbprogram om du vill använda en anpassad domän.
