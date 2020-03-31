---
title: Delegera en underdomän - Azure PowerShell - Azure DNS
description: Med den här utbildningssökvägen kommer du igång med att delegera en Azure DNS-underdomän med Azure PowerShell.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: 7e019afaae98422b8d5a3c8fa7a5f79e26c6a149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937710"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delegera en Azure DNS-underdomän med Azure PowerShell

Du kan använda Azure PowerShell för att delegera en DNS-underdomän. Om du till exempel äger contoso.com domänen kan du delegera en underdomän som kallas *teknik* till en annan, separat zon som du kan administrera separat från zonen contoso.com.

Om du vill kan du delegera en underdomän med hjälp av [Azure Portal](delegate-subdomain.md).

> [!NOTE]
> Contoso.com används som exempel i hela den här artikeln. Använd ditt eget domännamn i stället för contoso.com.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

Om du vill delegera en Azure DNS-underdomän måste du först delegera din offentliga domän till Azure DNS. Se [Delegera en domän till Azure DNS](./dns-delegate-domain-azure-dns.md) för instruktioner om hur du konfigurerar dina namnservrar för delegering. När domänen har delegerats till din Azure DNS-zon kan du delegera underdomänen.

## <a name="create-a-zone-for-your-subdomain"></a>Skapa en zon för underdomänen

Skapa först zonen för **den tekniska** underdomänen.

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Observera namnservrarna

Observera sedan de fyra namnservrarna för underdomänen för teknik.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Skapa en testpost

Skapa en **A-post** i den tekniska zonen som ska användas för testning.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Skapa en NS-post

Skapa sedan en namnserverpost (NS) för **teknikzonen** i zonen contoso.com.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Testa delegeringen

Använd nslookup för att testa delegeringen.

1. Öppna ett PowerShell-fönster.
2. Skriv i kommandotolken`nslookup www.engineering.contoso.com.`
3. Du bör få ett icke-auktoritativt svar som visar adressen **10.10.10.10**.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar omvänd DNS för tjänster som finns i Azure](dns-reverse-dns-for-azure-services.md).