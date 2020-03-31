---
title: Hantera DNS-poster i Azure DNS med Azure PowerShell | Microsoft-dokument
description: Hantera DNS-postuppsättningar och poster på Azure DNS när du är värd för din domän på Azure DNS. Alla PowerShell-kommandon för åtgärder på postuppsättningar och poster.
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: b9244d9b2bdc9cb20195bbc103c0b1eb48a9de63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932533"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Hantera DNS-poster och postmängder i Azure DNS med Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Klassisk Azure CLI](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [Powershell](dns-operations-recordsets.md)

I den här artikeln visas hur du hanterar DNS-poster för din DNS-zon med hjälp av Azure PowerShell. DNS-poster kan också hanteras med hjälp av [plattformsoberoende Azure CLI](dns-operations-recordsets-cli.md) eller [Azure-portalen](dns-operations-recordsets-portal.md).

Exemplen i den här artikeln förutsätter att du redan har [installerat Azure PowerShell, loggat in och skapat en DNS-zon](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Introduktion

Innan du skapar DNS-poster i Azure DNS, måste du först förstå hur Azure DNS organiserar DNS-poster i DNS-postuppsättningar.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Mer information om DNS-poster i Azure DNS finns i [DNS-zoner och poster](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Skapa en ny DNS-post

Om den nya posten har samma namn och text som en befintlig post måste du lägga till [den i den befintliga postuppsättningen](#add-a-record-to-an-existing-record-set). Om den nya posten har ett annat namn och en annan typ till alla befintliga poster måste du skapa en ny postuppsättning. 

### <a name="create-a-records-in-a-new-record-set"></a>Skapa A-poster i en ny postuppsättning

Du skapar postuppsättningar med hjälp av cmdleten `New-AzDnsRecordSet`. När du skapar en postuppsättning måste du ange postuppsättningens namn, zonen, tiden att leva (TTL), posttypen och de poster som ska skapas.

Parametrarna för att lägga till poster i en postuppsättning varierar beroende på typen av postuppsättning. När du till exempel använder en postuppsättning av typen "A" måste `-IPv4Address`du ange IP-adressen med parametern . Andra parametrar används för andra posttyper. Mer information finns i Ytterligare exempel på posttyp.

I följande exempel skapas en postuppsättning med det relativa namnet "www" i DNS-zonen "contoso.com". Det fullständigt kvalificerade namnet på den postuppsättning som är "www.contoso.com". Posttypen är "A" och TTL är 3600 sekunder. Postuppsättningen innehåller en enda post med IP-adressen "1.2.3.4".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Om du vill skapa en post som har angetts vid "apex" i en zon\@(i det här fallet "contoso.com") använder du postuppsättningsnamnet ' ' (exklusive citattecken):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Om du behöver skapa en postuppsättning som innehåller mer än en post skapar du `New-AzDnsRecordSet` först en lokal matris och lägger till posterna och skickar sedan matrisen till följande:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Datauppsättningsmetadata](dns-zones-records.md#tags-and-metadata) kan användas för att associera programspecifika data med varje postuppsättning, som nyckelvärdespar. I följande exempel visas hur du skapar en postuppsättning med två metadataposter, "dept=finance" och "environment=production".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS stöder också "tomma" postuppsättningar, som kan fungera som platshållare för att reservera ett DNS-namn innan DNS-poster skapas. Tomma postuppsättningar visas i Azure DNS-kontrollplanet, men visas på Azure DNS-namnservrarna. I följande exempel skapas en tom postuppsättning:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Skapa poster av andra typer

Efter att ha sett i detalj hur du skapar A-poster visar följande exempel hur du skapar poster av andra posttyper som stöds av Azure DNS.

I varje enskilt fall visar vi hur du skapar en postuppsättning som innehåller en enda post. De tidigare exemplen på A-poster kan anpassas för att skapa postuppsättningar av andra typer som innehåller flera poster, med metadata eller för att skapa tomma postuppsättningar.

Vi ger inte ett exempel för att skapa en SOA-postuppsättning, eftersom SOA skapas och tas bort med varje DNS-zon och inte kan skapas eller tas bort separat. SOA kan dock [ändras, vilket visas i ett senare exempel](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Skapa en AAAA-postuppsättning med en post

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Skapa en CAA-postuppsättning med en enda post

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Skapa en CNAME-postuppsättning med en post

> [!NOTE]
> DNS-standarderna tillåter inte CNAME-poster i toppen`-Name '@'`av en zon ( ), inte heller tillåter de postuppsättningar som innehåller mer än en post.
> 
> Mer information finns i [CNAME-poster](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Skapa en MX-postuppsättning med en post

I det här exemplet använder vi\@postuppsättningsnamnet ' ' för att skapa en MX-post i zonapexen (i det här fallet "contoso.com").


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Skapa en NS-postuppsättning med en post

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Skapa en PTR-postuppsättning med en post

I det här fallet representerar "my-arpa-zone.com" arpa-zonen för omvänd sökning som representerar ditt IP-intervall. Varje PTR-post som har angetts i den här zonen motsvarar en IP-adress i IP-intervallet. Postnamnet '10' är den sista oktetten av IP-adressen inom det här IP-intervallet som representeras av den här posten.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Skapa en SRV-postuppsättning med en post

När du skapar en [SRV-postuppsättning](dns-zones-records.md#srv-records)anger du * \_tjänsten* och * \_protokollet* i postuppsättningens namn. Det finns ingen anledning\@att inkludera ' ' i postuppsättningsnamnet när du skapar en SRV-post som anges vid zonsponsen.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Skapa en TXT-postuppsättning med en enda post

I följande exempel visas hur du skapar en TXT-post. Mer information om den maximala stränglängden som stöds i TXT-poster finns i [TXT-poster](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Hämta en postuppsättning

Om du vill hämta `Get-AzDnsRecordSet`en befintlig postuppsättning använder du . Den här cmdleten returnerar ett lokalt objekt som representerar posten som anges i Azure DNS.

Som `New-AzDnsRecordSet`med måste det angivna postnamnet vara ett *relativt* namn, vilket innebär att det måste utesluta zonnamnet. Du måste också ange posttypen och zonen som innehåller postuppsättningen.

I följande exempel visas hur du hämtar en postuppsättning. I det här exemplet anges zonen `-ZoneName` `-ResourceGroupName` med hjälp av parametrarna och.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Du kan också ange zonen med hjälp av ett `-Zone` zonobjekt som skickas med parametern.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Listpostuppsättningar

Du kan `Get-AzDnsZone` också använda för att lista postuppsättningar `-Name` i `-RecordType` en zon genom att utelämna och/eller parametrarna.

I följande exempel returneras alla postuppsättningar i zonen:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

I följande exempel visas hur alla postuppsättningar av en viss typ kan hämtas genom att ange posttypen när du utelämnar postuppsättningsnamnet:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Om du vill hämta alla postuppsättningar med ett visst namn måste du i posttyper hämta alla postuppsättningar och sedan filtrera resultaten:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

I alla ovanstående exempel kan zonen anges antingen `-ZoneName` med `-ResourceGroupName`hjälp av parametrarna och (som visas) eller genom att ange ett zonobjekt:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Lägga till en post i en befintlig postuppsättning

Så här lägger du till en post i en befintlig postuppsättning:

1. Hämta den befintliga postuppsättningen

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Lägg till den nya posten i den lokala postuppsättningen. Det här är en off-line operation.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Genomför ändringen tillbaka till Azure DNS-tjänsten. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Med `Set-AzDnsRecordSet` *hjälp ersätter* den befintliga posten som anges i Azure DNS (och alla poster den innehåller) med den angivna postuppsättningen. [Etag-kontroller](dns-zones-records.md#etags) används för att säkerställa att samtidiga ändringar inte skrivs över. Du kan använda `-Overwrite` den valfria växeln för att undertrycka dessa kontroller.

Den här sekvensen av åtgärder kan också *ledas,* vilket innebär att du skickar postobjektet med hjälp av pipe i stället för att skicka det som en parameter:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Exemplen ovan visar hur du lägger till en A-post i en befintlig postuppsättning av typen "A". En liknande sekvens av åtgärder används för att lägga till `-Ipv4Address` poster `Add-AzDnsRecordConfig` i postuppsättningar av andra typer, vilket ersätter parametern för med andra parametrar som är specifika för varje posttyp. Parametrarna för varje posttyp är `New-AzDnsRecordConfig` desamma som för cmdlet, som visas i Ytterligare posttypsexempel ovan.

Postuppsättningar av typen "CNAME" eller "SOA" får inte innehålla mer än en post. Den här begränsningen beror på DNS-standarderna. Det är inte en begränsning av Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Ta bort en post från en befintlig postuppsättning

Processen för att ta bort en post från en postuppsättning liknar processen för att lägga till en post i en befintlig postuppsättning:

1. Hämta den befintliga postuppsättningen

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Ta bort posten från det lokala postuppsättningsobjektet. Det här är en off-line operation. Posten som tas bort måste vara en exakt matchning med en befintlig post över alla parametrar.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Genomför ändringen tillbaka till Azure DNS-tjänsten. Använd den `-Overwrite` valfria växeln för att undertrycka [Etag-kontroller](dns-zones-records.md#etags) för samtidiga ändringar.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Om du använder ovanstående sekvens för att ta bort den sista posten från en postuppsättning tas inte postuppsättningen bort, utan en tom postuppsättning. Om du vill ta bort en postuppsättning helt och hållet läser du [Ta bort en postuppsättning](#delete-a-record-set).

På samma sätt som om du lägger till poster i en postuppsättning kan även sekvensen av åtgärder för att ta bort en postuppsättning ledas:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Olika posttyper stöds genom att lämpliga typspecifika parametrar överförs till `Remove-AzDnsRecordSet`. Parametrarna för varje posttyp är `New-AzDnsRecordConfig` desamma som för cmdlet, som visas i Ytterligare posttypsexempel ovan.


## <a name="modify-an-existing-record-set"></a>Ändra en befintlig postuppsättning

Stegen för att ändra en befintlig postuppsättning liknar de steg du tar när du lägger till eller tar bort poster från en postuppsättning:

1. Hämta den befintliga posten `Get-AzDnsRecordSet`med hjälp av .
2. Ändra det lokala postobjektet genom att:
    * Lägga till eller ta bort poster
    * Ändra parametrarna för befintliga poster
    * Ändra metadata och tid för att visa postuppsättningen (TTL)
3. Genomför ändringarna med `Set-AzDnsRecordSet` hjälp av cmdleten. Detta *ersätter* den befintliga posten som anges i Azure DNS med den angivna postuppsättningen.

När `Set-AzDnsRecordSet`du använder används [Etag-kontroller](dns-zones-records.md#etags) för att säkerställa att samtidiga ändringar inte skrivs över. Du kan använda `-Overwrite` den valfria växeln för att undertrycka dessa kontroller.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Så här uppdaterar du en post i en befintlig postuppsättning

I det här exemplet ändrar vi IP-adressen för en befintlig A-post:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Så här ändrar du en SOA-post

Du kan inte lägga till eller ta bort poster från den`-Name "@"`automatiskt skapade SOA-postuppsättningen vid zonapexen ( , inklusive citattecken). Du kan dock ändra någon av parametrarna i SOA-posten (förutom "Host") och posten som TTL.

I följande exempel visas hur du ändrar egenskapen *E-post* för SOA-posten:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Så här ändrar du NS-poster i zonsponsen

NS-posten som anges vid zonapexen skapas automatiskt med varje DNS-zon. Den innehåller namnen på Azure DNS-namnservrar som tilldelats zonen.

Du kan lägga till ytterligare namnservrar i den här NS-postuppsättningen för att stödja samhostingdomäner med mer än en DNS-provider. Du kan också ändra TTL och metadata för den här postuppsättningen. Du kan dock inte ta bort eller ändra de förifyllda Azure DNS-namnservrarna.

Observera att detta endast gäller för NS-posten som anges vid zonsponsan. Andra NS-postuppsättningar i zonen (som används för att delegera underordnade zoner) kan ändras utan begränsning.

I följande exempel visas hur du lägger till ytterligare en namnserver i NS-posten som angetts vid zonsponsen:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Så här ändrar du metadata för postuppsättning

[Datauppsättningsmetadata](dns-zones-records.md#tags-and-metadata) kan användas för att associera programspecifika data med varje postuppsättning, som nyckelvärdespar.

I följande exempel visas hur du ändrar metadata för en befintlig postuppsättning:

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

Postuppsättningar kan tas `Remove-AzDnsRecordSet` bort med hjälp av cmdlet. Om du tar bort en postuppsättning tas även alla poster i postuppsättningen bort.

> [!NOTE]
> Du kan inte ta bort SOA- och NS-postuppsättningarna vid zonapexen (`-Name '@'`).  Azure DNS skapade dessa automatiskt när zonen skapades och tar bort dem automatiskt när zonen tas bort.

I följande exempel visas hur du tar bort en postuppsättning. I det här exemplet anges postuppsättningens namn, postuppsättningstyp, zonnamn och resursgrupp uttryckligen.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternativt kan postuppsättningen anges efter namn och typ, och den zon som anges med ett objekt:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Som ett tredje alternativ kan själva postuppsättningen anges med hjälp av ett postuppsättningsobjekt:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

När du anger den postuppsättning som ska tas bort med hjälp av ett postuppsättningsobjekt används [Etag-kontroller](dns-zones-records.md#etags) för att se till att samtidiga ändringar inte tas bort. Du kan använda `-Overwrite` den valfria växeln för att undertrycka dessa kontroller.

Postuppsättningsobjektet kan också ledas i stället för att skickas som en parameter:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Bekräftelsemeddelanden

Cmdletarna `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` och `Remove-AzDnsRecordSet` stöder alla bekräftelsemeddelanden.

Varje cmdlet uppmanas att `$ConfirmPreference` bekräfta om PowerShell-inställningsvariabeln har ett värde på `Medium` eller lägre. Eftersom standardvärdet `$ConfirmPreference` för `High`är, dessa uppmaningar inte ges när du använder standard PowerShell-inställningar.

Du kan åsidosätta den aktuella `$ConfirmPreference`-inställningen med hjälp av parametern `-Confirm`. Om du anger `-Confirm` eller `-Confirm:$True`, så uppmanar cmdleten dig att bekräfta detta innan den körs. Om du anger `-Confirm:$False`, som ber cmdleten dig inte om bekräftelse. 

Mer information om `-Confirm` och `$ConfirmPreference` finns i [Om inställningsvariabler](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Nästa steg

Läs mer om [zoner och poster i Azure DNS](dns-zones-records.md).
<br>
Lär dig hur du [skyddar dina zoner och poster](dns-protect-zones-recordsets.md) när du använder Azure DNS.
<br>
Granska [referensdokumentationen för Azure DNS PowerShell](/powershell/module/az.dns).
