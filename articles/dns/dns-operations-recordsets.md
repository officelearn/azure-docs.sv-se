---
title: Hantera DNS-poster i Azure DNS med hjälp av Azure PowerShell | Microsoft Docs
description: Hantera DNS-postuppsättningar och på Azure DNS-poster när värd för din domän på Azure DNS. Alla PowerShell-kommandon för åtgärder på uppsättningar av poster och poster.
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: kumud
ms.openlocfilehash: 511af342727dc46369ae70d60a7e9a3171bf986d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
ms.locfileid: "32778843"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Hantera DNS-poster och postuppsättningar i Azure DNS med hjälp av Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Den här artikeln visar hur du hanterar DNS-posterna för DNS-zonen med hjälp av Azure PowerShell. DNS-poster kan också hanteras med hjälp av plattformsoberoende [Azure CLI](dns-operations-recordsets-cli.md) eller [Azure-portalen](dns-operations-recordsets-portal.md).

Exemplen i den här artikeln förutsätter att du redan har [installerat Azure PowerShell loggat in, och skapa en DNS-zon](dns-operations-dnszones.md).

## <a name="introduction"></a>Introduktion

Innan du skapar DNS-poster i Azure DNS, måste du först förstå hur Azure DNS organiserar DNS-poster i DNS-postuppsättningar.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Mer information om DNS-poster i Azure DNS finns i [DNS-zoner och poster](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Skapa en ny DNS-post

Om din nya posten har samma namn och typ som en befintlig post, behöver du [lägga till den befintliga postuppsättning](#add-a-record-to-an-existing-record-set). Om din nya posten har ett annat namn och typ för alla befintliga poster, måste du skapa en ny uppsättning av poster. 

### <a name="create-a-records-in-a-new-record-set"></a>Skapa ”A” poster i en ny uppsättning av poster

Du skapar postuppsättningar med hjälp av cmdleten `New-AzureRmDnsRecordSet`. När du skapar en uppsättning poster, måste du ange postuppsättning namn, zonen, tiden live (TTL), posttyp och posterna som ska skapas.

Parametrarna för att lägga till poster i en postuppsättning varierar beroende på typen av postuppsättning. Till exempel när du använder en postuppsättning av typen ”A”, du måste ange IP-adressen med parametern `-IPv4Address`. Andra parametrar används för andra typer av poster. Se [ytterligare posttyp exempel](#additional-record-type-examples) mer information.

I följande exempel skapas en postuppsättning med det relativa namnet ”www” i DNS-zonen ”contoso.com”. Det fullständigt kvalificerade namnet på uppsättningen av poster är ”www.contoso.com”. Posttypen är ”A”, och är TTL 3600 sekunder. Uppsättningen poster innehåller en post med IP-adressen '1.2.3.4'.

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Att skapa en postuppsättning på apex för en zon (i det här fallet ”contoso.com”), använder du postuppsättningsnamnet ”\@” (utan citattecken):

```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Om du behöver skapa en postuppsättning innehåller fler än en post först skapa en lokal matris och lägga till poster och sedan skicka för matrisen på `New-AzureRmDnsRecordSet` på följande sätt:

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Postuppsättning metadata](dns-zones-records.md#tags-and-metadata) kan användas för att koppla programspecifika data till varje postuppsättningen, som nyckel / värde-par. I följande exempel visas hur du skapar en postuppsättning med två metadataposter ”Avd = ekonomi ' och ' miljö = produktion”.

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS stöder också 'empty-postuppsättningar som kan fungera som en platshållare för att reservera en DNS-namn innan du skapar DNS-poster. Tom postuppsättningar syns i Azure DNS-kontrollplan, men visas på Azure DNS-namnservrar. I följande exempel skapas en tom postuppsättning:

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Skapa poster för andra typer

Har sett i detalj hur du skapar ”A” poster, visar i följande exempel hur du skapar andra posttyper som stöds av Azure DNS-poster.

I varje fall visar vi hur du skapar en postuppsättning med en enskild post. Tidigare exemplen för ”A” poster kan anpassas att skapa postuppsättningar av andra typer som innehåller flera poster med metadata, eller skapa tom postuppsättningar.

Vi inte ger ett exempel för att skapa en SOA-poster, eftersom SOAs skapas och tas bort tillsammans med varje DNS-zon och kan inte skapas eller tas bort separat. Dock [SOA kan ändras, som visas i en senare exempel](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Skapa en AAAA-postuppsättning med en post

```powershell
New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Skapa en CAA-postuppsättning med en post

```powershell
New-AzureRmDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Skapa en CNAME-postuppsättning med en post

> [!NOTE]
> DNS-standarden inte tillåter CNAME-poster på apex för en zon (`-Name '@'`), eller tillåter att postuppsättningar som innehåller fler än en post.
> 
> Mer information finns i [CNAME-poster](dns-zones-records.md#cname-records).


```powershell
New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Skapa en MX-postuppsättning med en post

I det här exemplet använder vi postuppsättningsnamnet ”\@” så här skapar du en MX-post på zonens apex (i det här fallet ”contoso.com”).


```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Skapa en NS-postuppsättning med en post

```powershell
New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Skapa en PTR-postuppsättning med en post

I det här fallet ' min-arpa-zone.com som representerar den ARPA zon för omvänd sökning som motsvarar IP-adressintervall. Varje PTR-post som har angetts i den här zonen motsvarar en IP-adress i IP-intervallet. Postnamnet ”10” är den sista oktetten i IP-adress inom den här IP-adressintervall som representeras av den här posten.

```powershell
New-AzureRmDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Skapa en SRV-postuppsättning med en post

När du skapar en [SRV-postuppsättning](dns-zones-records.md#srv-records), ange den  *\_service* och  *\_protokollet* i postuppsättningens namn. Behöver du inte inkludera ”\@” i namnet på postuppsättningen när du skapar en SRV-post på zonens apex.

```powershell
New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Skapa en TXT-postuppsättning med en post

I följande exempel visas hur du skapar en TXT-post. Mer information om den maximala stränglängden som stöds i TXT-poster finns [TXT-poster](dns-zones-records.md#txt-records).

```powershell
New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Hämta en uppsättning poster

Använd för att hämta en befintlig postuppsättning `Get-AzureRmDnsRecordSet`. Denna cmdlet returnerar ett lokalt objekt som representerar postuppsättningen i Azure DNS.

Precis som med `New-AzureRmDnsRecordSet`, namnet på postuppsättningen anges måste vara en *relativa* namn, vilket innebär att den inte får innehålla zonnamnet. Du måste också ange typ av post och ange den zon som innehåller posten.

I följande exempel visas hur du hämtar en postuppsättning. I det här exemplet zonen har angetts med hjälp av den `-ZoneName` och `-ResourceGroupName` parametrar.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternativt kan du också ange zonen med en zon-objekt som överförts med hjälp av den `-Zone` parameter.

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Lista över postuppsättningar

Du kan också använda `Get-AzureRmDnsZone` till listan uppsättningar av poster i en zon genom att utelämna den `-Name` och/eller `-RecordType` parametrar.

I följande exempel returneras alla postuppsättningar i zonen:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

I följande exempel visas hur alla uppsättningar av en viss typ kan hämtas genom att ange typ av post när utelämna posten anges namn:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Om du vill hämta alla postuppsättningar med ett angivet namn över posttyper, måste du hämta alla postuppsättningar och filtrera resultatet:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

I alla ovanstående exempel zonen anges antingen med hjälp av den `-ZoneName` och `-ResourceGroupName`parametrar (som visas), eller genom att ange ett objekt i zonen:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Lägga till en post i en befintlig postuppsättning

Lägg till en post i en befintlig postuppsättningen, gör du följande tre steg:

1. Hämta uppsättningen av befintliga poster

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Lägga till den nya posten i den lokala postuppsättningen. Detta är en åtgärd som är offline.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Tillämpa ändringen tillbaka till Azure DNS-tjänsten. 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

Med hjälp av `Set-AzureRmDnsRecordSet` *ersätter* befintliga posten i Azure DNS (och alla poster som den innehåller) med uppsättningen av poster anges. [ETag kontrollerar](dns-zones-records.md#etags) används för att kontrollera samtidiga ändringar inte att skrivas över. Du kan använda det valfria `-Overwrite` växel för att ignorera dessa kontroller.

Den här sekvensen av åtgärder kan också vara *skickas*, vilket innebär att du skickar postuppsättning objektet genom att använda pipe i stället för att skickas som en parameter:

```powershell
Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

Exemplen ovan visar hur du kan lägga till en ”A” till en uppsättning poster av typen ”A”. En liknande sekvens med åtgärder som används för att lägga till poster i postuppsättningar av andra typer som ersätter den `-Ipv4Address` -parametern för `Add-AzureRmDnsRecordConfig` med andra parametrar som är specifika för varje posttyp. Parametrarna för varje posttyp är samma som för den `New-AzureRmDnsRecordConfig` cmdlet, som visas i [ytterligare posttyp exempel](#additional-record-type-examples) ovan.

Postuppsättningar av typen 'CNAME' eller 'SOA-' får inte innehålla fler än en post. Den här begränsningen uppstår från DNS-standarden. Det är inte en begränsning i Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Ta bort en post från en befintlig post

Processen för att ta bort en post från en postuppsättning liknar processen att lägga till en post i en befintlig postuppsättning:

1. Hämta uppsättningen av befintliga poster

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Ta bort posten från lokala postuppsättning-objekt. Detta är en åtgärd som är offline. Posten som tas bort måste vara en exakt matchning med en befintlig post över alla parametrar.

    ```powershell
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Tillämpa ändringen tillbaka till Azure DNS-tjänsten. Använd det valfria `-Overwrite` växel för att undertrycka [Etag kontrollerar](dns-zones-records.md#etags) för samtidiga ändringar.

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $Rs
    ```

Med hjälp av sekvensen ovan för att ta bort den sista posten från en uppsättning poster tas inte bort uppsättningen av poster, i stället de lämnar en tom postuppsättning. Om du vill ta bort en postuppsättning helt finns [ta bort en postuppsättning](#delete-a-record-set).

På liknande sätt att lägga till poster i en postuppsättning kan sekvens med åtgärder för att ta bort en uppsättning poster också skickas:

```powershell
Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

Olika posttyper stöds genom att skicka lämpliga parametrar för typspecifika att `Remove-AzureRmDnsRecordSet`. Parametrarna för varje posttyp är samma som för den `New-AzureRmDnsRecordConfig` cmdlet, som visas i [ytterligare posttyp exempel](#additional-record-type-examples) ovan.


## <a name="modify-an-existing-record-set"></a>Ändra en befintlig postuppsättning

Stegen för att ändra en befintlig postuppsättning liknar de steg du kan vidta när du lägger till eller ta bort poster från en postuppsättning:

1. Hämta den befintliga-postuppsättning med hjälp av `Get-AzureRmDnsRecordSet`.
2. Ändra objektet lokala postuppsättningen av:
    * Tillägg eller borttagning av poster
    * Ändra parametrarna för befintliga poster
    * Ändra posten ange metadata och tid till live (TTL)
3. Genomför ändringarna med hjälp av den `Set-AzureRmDnsRecordSet` cmdlet. Detta *ersätter* befintliga posten i Azure DNS med uppsättningen av poster anges.

När du använder `Set-AzureRmDnsRecordSet`, [Etag kontrollerar](dns-zones-records.md#etags) används för att kontrollera samtidiga ändringar inte att skrivas över. Du kan använda det valfria `-Overwrite` växel för att ignorera dessa kontroller.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Att uppdatera en post i en befintlig postuppsättning

I det här exemplet ändrar vi IP-adressen för en befintlig ”A” post:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Att ändra en SOA-post

Du kan inte lägga till eller ta bort poster från den automatiskt skapade SOA-postuppsättning på zonens apex (`-Name "@"`, inklusive citattecken). Men du kan ändra någon av parametrarna i SOA-post (utom ”värd”) och postuppsättning TTL-värde.

I följande exempel visas hur du ändrar den *e-post* -egenskapen för SOA-post:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Att ändra NS-poster på zonens apex

NS-postuppsättning på zonens apex skapas automatiskt med varje DNS-zon. Den innehåller namnen på Azure DNS-namnservrar tilldelas zonen.

Du kan lägga till ytterligare servrar till den här NS-post inställda på en värd domäner med mer än en DNS-leverantör har stöd ett namn. Du kan också ändra TTL-värde och metadata för den här postuppsättningen. Du kan inte ta bort eller ändra de i förväg Azure DNS-namnservrarna.

Observera att detta gäller endast NS-postuppsättning på zonens apex. Andra NS postuppsättningar i zonen (som används för att delegera underordnade zoner) kan ändras utan begränsning.

I följande exempel visas hur du lägger till en ytterligare namnserver NS-postuppsättning på zonens apex:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Att ändra postuppsättning metadata

[Postuppsättning metadata](dns-zones-records.md#tags-and-metadata) kan användas för att koppla programspecifika data till varje postuppsättningen, som nyckel / värde-par.

I följande exempel visas hur du ändrar metadata för en befintlig postuppsättning:

```powershell
# Get the record set
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzureRmDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Ta bort en uppsättning poster

Postuppsättningar kan tas bort med hjälp av den `Remove-AzureRmDnsRecordSet` cmdlet. En postuppsättning också tar du bort alla posterna i uppsättningen av poster.

> [!NOTE]
> Du kan inte ta bort SOA och NS-post anger på zonens apex (`-Name '@'`).  Azure DNS skapas de automatiskt när zonen skapades och tar bort dem automatiskt när zonen tas bort.

I följande exempel visas hur du tar bort en uppsättning poster. I det här exemplet anges postuppsättningsnamnet, postuppsättning typ, zonnamnet och resursgruppen varje explicit.

```powershell
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Uppsättningen av poster kan också anges med namn och typ och zonen anges med ett objekt:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Som ett tredje alternativ, anges postuppsättningen själva med en postuppsättning objektet:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -RecordSet $rs
```

När du anger posten som ska tas bort med hjälp av en postuppsättning objektet [Etag kontrollerar](dns-zones-records.md#etags) används för att kontrollera samtidiga ändringar inte tas bort. Du kan använda det valfria `-Overwrite` växel för att ignorera dessa kontroller.

Postuppsättning-objekt kan även skickas i stället för att skickas som en parameter:

```powershell
Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzureRmDnsRecordSet
```

## <a name="confirmation-prompts"></a>Bekräftelsemeddelanden

Cmdletarna `New-AzureRmDnsRecordSet`, `Set-AzureRmDnsRecordSet` och `Remove-AzureRmDnsRecordSet` stöder alla bekräftelsemeddelanden.

Varje cmdlet uppmanas att bekräfta om den `$ConfirmPreference` PowerShell inställningsvariabeln har värdet `Medium` eller lägre. Eftersom standardvärdet för `$ConfirmPreference` är `High`, dessa frågor inte anges när du använder standardinställningarna för PowerShell.

Du kan åsidosätta den aktuella `$ConfirmPreference`-inställningen med hjälp av parametern `-Confirm`. Om du anger `-Confirm` eller `-Confirm:$True`, så uppmanar cmdleten dig att bekräfta detta innan den körs. Om du anger `-Confirm:$False`, som ber cmdleten dig inte om bekräftelse. 

Mer information om `-Confirm` och `$ConfirmPreference` finns i [Om inställningsvariabler](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [zoner och poster i Azure DNS](dns-zones-records.md).
<br>
Lär dig hur du [skydda zoner och poster](dns-protect-zones-recordsets.md) när du använder Azure DNS.
<br>
Granska de [Azure DNS-PowerShell referensdokumentationen](/powershell/module/azurerm.dns).
