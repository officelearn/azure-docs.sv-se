---
title: Självstudie – Skapa anpassade Azure DNS-poster för en webbapp
description: I den här självstudien skapar du DNS-poster för anpassade domäner för webbappar med hjälp av Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 2/19/2019
ms.author: victorh
ms.openlocfilehash: 9ed0c8763835add485d6c60a43f4e4113ecde12e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429289"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>Självstudier: Skapa DNS-poster i en anpassad domän för en webbapp 

Du kan konfigurera Azure DNS för att vara värd för en anpassad domän för dina webbprogram. Du kan till exempel skapa en Azure-webbapp och göra så att dina användare kommer åt den via www.contoso.com eller contoso.com som ett fullständigt kvalificerat domännamn (FQDN).

> [!NOTE]
> Contoso.com används som exempel i den här självstudien. Använd ditt eget domännamn i stället för contoso.com.

Om du vill göra detta måste du skapa tre poster:

* En ”A”-rotpost som pekar på contoso.com
* En ”TXT”-rotpost för verifiering
* En ”CNAME”-post för www-namnet som pekar på A-posten

Tänk på att om du skapar en A-post för en webbapp i Azure måste A-posten uppdateras manuellt om den underliggande IP-adressen för webbappen ändras.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en A- och TXT-post för den anpassade domänen
> * Skapa en CNAME-post för den anpassade domänen
> * Testa de nya posterna
> * Lägg till anpassade värdnamn till din webbapp
> * Testa de anpassade domännamnen


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- [Skapa en App Service-app](../app-service/app-service-web-get-started-html.md), eller använd en app som du har skapat för en annan kurs.

- Skapa en DNS-zon i Azure DNS och delegera zonen i registratorn till Azure DNS.

   1. Om du vill skapa en DNS-zon följer du stegen i [Skapa en DNS-zon](dns-getstarted-create-dnszone.md).
   2. Om du vill delegera zonen till Azure DNS följer du stegen i [DNS-domändelegering](dns-domain-delegation.md).

När du har skapat en zon och delegerat den till Azure DNS kan du sedan skapa poster för din anpassade domän.

## <a name="create-an-a-record-and-txt-record"></a>Skapa en A-post och en TXT-post

En A-post används för att mappa ett namn till dess IP-adress. I följande exempel tilldelar du ”\@” som en A-post med hjälp ad webbappens IPv4-adress. \@ representerar vanligtvis rotdomänen.

### <a name="get-the-ipv4-address"></a>Hämta IPv4-adressen

I det vänstra navigeringsfönstret på App Services-sidan i Azure-portalen väljer du **Anpassade domäner**. 

![Meny för anpassad domän](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

På sidan **Anpassade domäner** kopierar du appens IPv4-adress:

![Portalnavigering till Azure-app](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>Skapa en A-post

```powershell
New-AzDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>Skapa TXT-posten

Den här posten används av App Services endast vid konfigurationen, för att verifiera att du äger den anpassade domänen. Du kan ta bort den här TXT-posten när din anpassade domän har verifierats och konfigurerats i App Service.

```powershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name "@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>Skapa CNAME-posten

Om din domän redan hanteras av Azure DNS (se [DNS-domändelegering](dns-domain-delegation.md) kan du använda följande exempel för att skapa en CNAME-post för contoso.azurewebsites.net.

Öppna Azure PowerShell och skapa en ny CNAME-post. Det här exemplet skapar en CNAME-postuppsättningstyp med en ”time to live” på 600 sekunder i DNS-zonen med namnet ”contoso.com” med alias för webbappen contoso.azurewebsites.net.

### <a name="create-the-record"></a>Skapa posten

```powershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -cname "contoso.azurewebsites.net")
```

Följande exempel är svaret:

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

## <a name="test-the-new-records"></a>Testa de nya posterna

Du kan verifiera att posterna har skapats korrekt genom att köra en fråga mot ”www.contoso.com” och ”contoso.com” med hjälp av nslookup, enligt nedan:

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

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>Lägga till anpassade domännamn

Nu kan du lägga till de anpassade värdnamnen till din webbapp:

```powershell
set-AzWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>Testa de anpassade domännamnen

Öppna en webbläsare och gå till `http://www.<your domainname>` och `http://<you domain name>`.

> [!NOTE]
> Se till att du inkluderar prefixet `http://`. Annars kan webbläsaren försöka förutsäga en URL åt dig.

Du bör se samma sida för båda URL:er. Exempel:

![Contoso-apptjänst](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som skapades i den här självstudien tar du bort resursgruppen **myresourcegroup**.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar privata Azure DNS-zoner.

> [!div class="nextstepaction"]
> [Komma igång med privata Azure DNS-zoner med PowerShell](private-dns-getstarted-powershell.md)
