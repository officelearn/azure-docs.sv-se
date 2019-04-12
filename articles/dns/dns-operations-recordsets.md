---
title: Hantera DNS-poster i Azure DNS med Azure PowerShell | Microsoft Docs
description: Hantera DNS-postuppsättningar och poster i Azure DNS när du har din domän i Azure DNS. Alla PowerShell-kommandon för åtgärder på postuppsättningar och poster.
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: victorh
ms.openlocfilehash: 61871ada0679a68b7f9d872a0df36d22cfb1f0de
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491210"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Hantera DNS-poster och postuppsättningar i Azure DNS med Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Klassisk Azure CLI](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Den här artikeln visar hur du hanterar DNS-poster för din DNS-zon med hjälp av Azure PowerShell. DNS-poster kan också hanteras med hjälp av plattformsoberoende [Azure CLI](dns-operations-recordsets-cli.md) eller [Azure-portalen](dns-operations-recordsets-portal.md).

Exemplen i den här artikeln förutsätter att du redan har [installerat Azure PowerShell, loggat in och skapat en DNS-zon](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Introduktion

Innan du skapar DNS-poster i Azure DNS, måste du först förstå hur Azure DNS organiserar DNS-poster i DNS-postuppsättningar.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Mer information om DNS-poster i Azure DNS finns i [DNS-zoner och poster](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Skapa en ny DNS-post

Om den nya posten har samma namn och typ som en befintlig post kan du behöva [lägga till den i den befintliga postuppsättningen](#add-a-record-to-an-existing-record-set). Om den nya posten har ett annat namn och typ för alla befintliga poster, måste du skapa en ny postuppsättning. 

### <a name="create-a-records-in-a-new-record-set"></a>Skapa ”A” poster i en ny postuppsättning

Du skapar postuppsättningar med hjälp av cmdleten `New-AzDnsRecordSet`. När du skapar en postuppsättning kan du behöva ange postuppsättningens namn, zonen, tiden till live (TTL), posttypen och posterna som ska skapas.

Parametrarna för att lägga till poster i en postuppsättning varierar beroende på typen av postuppsättning. Till exempel när du använder en postuppsättning av typen ”A” kan du behöva ange IP-adressen med hjälp av parametern `-IPv4Address`. Andra parametrar används för andra posttyper. Se ytterligare posttyper mer information.

I följande exempel skapas en post med det relativa namnet ”www” i DNS-zonen ”contoso.com”. Det fullständigt kvalificerade namnet på postuppsättningen är ”www.contoso.com”. Posttypen är ”A” och TTL är 3600 sekunder. Uppsättningen av poster innehåller en post med IP-adress ”1.2.3.4”.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Skapa en post på apex för en zon (i det här fallet ”contoso.com”), Använd namnet på postuppsättningen '\@”(förutom citattecken):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Om du vill skapa en post som innehåller fler än en post först skapa en lokal matris och lägga till poster och sedan skicka matris till `New-AzDnsRecordSet` på följande sätt:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Postuppsättningens metadata](dns-zones-records.md#tags-and-metadata) kan användas för att associera programspecifika data med varje uppsättning av poster, som nyckel / värde-par. I följande exempel visas hur du skapar en postuppsättning med två metadataposter ”Avd = ekonomi” och ”miljö = produktion”.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS stöder också ”tom” postuppsättningar som kan fungera som en platshållare för att reservera ett DNS-namn innan du skapar DNS-poster. Tom postuppsättningar visas i Azure DNS kontrollplanet, men visas på Azure DNS-namnservrarna. I följande exempel skapas en tom postuppsättning:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Skapa poster för andra typer

Att ha sett i detalj hur du kan skapa ”A”-poster, visas i följande exempel hur du skapar poster för andra posttyper som stöds av Azure DNS.

I varje fall visar vi hur du skapar en post som innehåller en enda post. I tidigare exempel för ”A” poster kan anpassas till skapar post uppsättningar av andra typer som innehåller flera poster med metadata, eller att skapa tom postuppsättningar.

Vi ger inte ett exempel för att skapa en SOA-postuppsättning eftersom SOAs skapas och tas bort med varje DNS-zon och kan inte skapas eller tas bort separat. Dock [SOA kan ändras, som visas i en senare exempel](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Skapa en AAAA-postuppsättning med en post

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Skapa en CAA-postuppsättning med en post

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Skapa en CNAME-postuppsättning med en post

> [!NOTE]
> DNS-standarden inte tillåter CNAME-poster överst i en zon (`-Name '@'`), eller tillåter att uppsättningar av poster som innehåller fler än en post.
> 
> Mer information finns i [CNAME-poster](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Skapa en MX-postuppsättning med en post

I det här exemplet använder vi postuppsättningsnamnet ”\@” skapa en MX-post i basdomänen (i det här fallet ”contoso.com”).


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Skapa en NS-postuppsättning med en post

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Skapa en PTR-postuppsättning med en post

I det här fallet ”mitt-arpa-zone.com” ARPA-omvänd sökning zonen som representerar ditt IP-adressintervall. Varje PTR-post som har angetts i den här zonen motsvarar en IP-adress i IP-intervallet. Postnamnet ”10” är den sista oktetten för IP-adress i IP-intervallet som representeras av den här posten.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Skapa en SRV-postuppsättning med en post

När du skapar en [SRV-postuppsättning](dns-zones-records.md#srv-records), ange den  *\_service* och  *\_protokollet* i namnet på postuppsättningen. Det finns inget behov att inkludera ”\@” i namnet på postuppsättningen när du skapar en SRV-posten i basdomänen.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Skapa en TXT-postuppsättning med en post

I följande exempel visas hur du skapar en TXT-post. Mer information om den maximala stränglängden som stöds i TXT-poster finns i [TXT-poster](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Hämta en uppsättning av poster

Använd för att hämta en befintlig uppsättning av poster `Get-AzDnsRecordSet`. Denna cmdlet returnerar ett lokalt objekt som representerar postuppsättningen i Azure DNS.

Precis som med `New-AzDnsRecordSet`, namnet på postuppsättningen beroende måste vara en *relativa* namn, vilket innebär att den får inte zonnamnet. Du måste också ange typ av post och ange zonen som innehåller posten.

I följande exempel visas hur du hämtar en postuppsättning. I det här exemplet zonen anges med hjälp av den `-ZoneName` och `-ResourceGroupName` parametrar.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternativt kan du även ange zonen med ett objekt för zonen skickas med hjälp av den `-Zone` parametern.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Listan över postuppsättningar

Du kan också använda `Get-AzDnsZone` i listan över postuppsättningar i en zon genom att utelämna den `-Name` och/eller `-RecordType` parametrar.

I följande exempel returneras alla postuppsättningar i zonen:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

I följande exempel visas hur alla postuppsättningar av en viss typ kan hämtas genom att ange typ av post när du om du utesluter posten namn:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Om du vill hämta alla postuppsättningar med ett givet namn över typer av poster, måste du hämta alla postuppsättningar och sedan filtrera resultaten:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

I alla ovanstående exempel zonen anges antingen genom att använda den `-ZoneName` och `-ResourceGroupName`parametrar (enligt), eller genom att ange en zonobjektet:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Lägga till en post i en befintlig uppsättning av poster

Om du vill lägga till en post i en befintlig postuppsättningen, gör du följande tre steg:

1. Hämta den befintliga postuppsättningen

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Lägg till den nya posten i den lokala postuppsättningen. Det här är en offline-åtgärd.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Bekräfta ändringen tillbaka till Azure DNS-tjänsten. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Med hjälp av `Set-AzDnsRecordSet` *ersätter* befintliga posten i Azure DNS (och alla poster som den innehåller) med den angivna uppsättningen av poster. [ETag-kontroller](dns-zones-records.md#etags) används för att se till att samtidiga ändringar inte skrivs över. Du kan använda den valfria `-Overwrite` växel för att ignorera dessa kontroller.

Den här sekvens med åtgärder kan också vara *skickas*, vilket innebär att du skickar objektet postuppsättning genom att använda en pipe i stället för att skicka den som en parameter:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

I exemplen ovan visar hur du lägger till en ”A” post i en befintliga postuppsättningen av typen ”A”. En liknande sekvens med åtgärder som används för att lägga till poster i postuppsättningar av andra typer, och Ersätt den `-Ipv4Address` -parametern för `Add-AzDnsRecordConfig` med andra parametrar som är specifika för varje posttyp. Parametrarna för varje posttyp är desamma som för den `New-AzDnsRecordConfig` cmdlet, enligt ytterligare posttyp exemplen ovan.

Postuppsättningar av typen ”CNAME' eller 'SOA-' får inte innehålla fler än en post. Den här begränsningen uppstår från DNS-standarden. Det är inte en begränsning i Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Ta bort en post från en befintlig uppsättning av poster

Processen för att ta bort en post från en postuppsättning är ungefär på samma sätt att lägga till en post i en befintlig uppsättning av poster:

1. Hämta den befintliga postuppsättningen

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Ta bort posten från det lokala postuppsättning-objektet. Det här är en offline-åtgärd. Den post som tas bort måste vara en exakt matchning med en befintlig post för alla parametrar.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Bekräfta ändringen tillbaka till Azure DNS-tjänsten. Använd det valfria `-Overwrite` växel ska förhindras [Etag kontrollerar](dns-zones-records.md#etags) för samtidiga ändringar.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Med hjälp av ovanstående sekvensen att ta bort den sista posten från en uppsättning av poster tas inte bort postuppsättningen, i stället de lämnar en tom postuppsättning. Om du vill ta bort en postuppsättning helt och hållet, se [ta bort en postuppsättning](#delete-a-record-set).

På liknande sätt att lägga till poster i en postuppsättning kan sekvens med åtgärder att ta bort en uppsättning av poster även pipas:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Olika typer av poster stöds genom att skicka lämpliga parametrar för typspecifika att `Remove-AzDnsRecordSet`. Parametrarna för varje posttyp är desamma som för den `New-AzDnsRecordConfig` cmdlet, enligt ytterligare posttyp exemplen ovan.


## <a name="modify-an-existing-record-set"></a>Ändra en befintlig uppsättning av poster

Stegen för att ändra en befintlig uppsättning av poster liknar vilka steg du utför när du lägger till eller ta bort poster från en postuppsättning:

1. Hämta den befintliga postuppsättningen med hjälp av `Get-AzDnsRecordSet`.
2. Ändra lokala postuppsättning objektet genom att:
    * Att lägga till eller ta bort poster
    * Ändra parametrarna för befintliga poster
    * Ändra posten anger metadata och tid till live (TTL)
3. Spara ändringarna genom att använda den `Set-AzDnsRecordSet` cmdlet. Detta *ersätter* befintliga posten i Azure DNS med den angivna uppsättningen av poster.

När du använder `Set-AzDnsRecordSet`, [Etag kontrollerar](dns-zones-records.md#etags) används för att se till att samtidiga ändringar inte skrivs över. Du kan använda den valfria `-Overwrite` växel för att ignorera dessa kontroller.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Att uppdatera en post i en befintlig uppsättning av poster

I det här exemplet ändrar vi IP-adressen för en befintlig ”A”-post:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Ändra ett SOA-posten

Du kan inte lägga till eller ta bort poster från den automatiskt skapade SOA-postuppsättning på zonens apex (`-Name "@"`, inklusive citattecken). Men du kan ändra någon av parametrarna i SOA-posten (utom ”värd”) och postuppsättningens TTL-värde.

I följande exempel visas hur du ändrar den *e-post* egenskapen för SOA-posten:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Ändra NS-poster i basdomänen

NS-postuppsättning på zonens apex skapas automatiskt med varje DNS-zon. Den innehåller namnen på Azure DNS-namnservrarna som tilldelats i zonen.

Du kan lägga till ytterligare servrar till den här NS-post anger för att stödja delad hosting domäner med fler än en DNS-leverantör ett namn. Du kan också ändra TTL-värde och metadata för den här uppsättningen av poster. Du kan inte ta bort eller ändra förifyllda Azure DNS-namnservrarna.

Observera att detta gäller endast för NS-postuppsättning på zonens apex. Andra NS postuppsättningar i din zon (som används för att delegera underordnade zoner) kan ändras utan begränsning.

I följande exempel visas hur du lägger till en ytterligare namnserver NS-postuppsättning på zonens apex:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Ändra metadata för postuppsättningen

[Postuppsättningens metadata](dns-zones-records.md#tags-and-metadata) kan användas för att associera programspecifika data med varje uppsättning av poster, som nyckel / värde-par.

I följande exempel visas hur du ändrar metadata för en befintlig uppsättning av poster:

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Ta bort en postuppsättning

Uppsättningar av poster kan tas bort med hjälp av den `Remove-AzDnsRecordSet` cmdlet. Tar en postuppsättning även bort alla poster inom postuppsättningen.

> [!NOTE]
> Du kan inte ta bort SOA och NS-post anger i basdomänen (`-Name '@'`).  Azure DNS skapat dessa automatiskt när zonen skapades, och tar bort dem automatiskt när zonen tas bort.

I följande exempel visas hur du tar bort en postuppsättning. I det här exemplet anges postuppsättningsnamnet, postuppsättning typ, zonnamnet och resursgruppens namn var uttryckligen.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternativt kan postuppsättningen anges med namn och typ och zonen anges med ett objekt:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Ett tredje alternativ, kan postuppsättningen själva anges med en uppsättning av poster objektet:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

När du anger posten som ska tas bort med hjälp av en uppsättning av poster i objektet [Etag kontrollerar](dns-zones-records.md#etags) används för att se till att samtidiga ändringar inte tas bort. Du kan använda den valfria `-Overwrite` växel för att ignorera dessa kontroller.

Objektet uppsättning av poster kan även pipas i stället för att skickas som en parameter:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Bekräftelsemeddelanden

Cmdletarna `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` och `Remove-AzDnsRecordSet` stöder alla bekräftelsemeddelanden.

Varje cmdlet uppmanas att bekräfta om de `$ConfirmPreference` PowerShell-inställningsvariabeln har värdet `Medium` eller lägre. Eftersom standardvärdet för `$ConfirmPreference` är `High`, de här anvisningarna inte ges när du använder standardinställningarna för PowerShell.

Du kan åsidosätta den aktuella `$ConfirmPreference`-inställningen med hjälp av parametern `-Confirm`. Om du anger `-Confirm` eller `-Confirm:$True`, så uppmanar cmdleten dig att bekräfta detta innan den körs. Om du anger `-Confirm:$False`, som ber cmdleten dig inte om bekräftelse. 

Mer information om `-Confirm` och `$ConfirmPreference` finns i [Om inställningsvariabler](/powershell/module/microsoft.powershell.core/about/about_preference_variables.1).

## <a name="next-steps"></a>Nästa steg

Läs mer om [zoner och poster i Azure DNS](dns-zones-records.md).
<br>
Lär dig hur du [skydda zoner och poster](dns-protect-zones-recordsets.md) när du använder Azure DNS.
<br>
Granska den [referensdokumentation för Azure DNS PowerShell](/powershell/module/az.dns).
