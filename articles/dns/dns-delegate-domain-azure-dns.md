---
title: "Delegera din domän till Azure DNS | Microsoft Docs"
description: "Lär dig hur du ändrar domändelegering och använder Azure DNS-namnservrar för att tillhandahålla domänvärdtjänster."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: kumud
ms.openlocfilehash: a13d9b360e2c43aa2a3d4f62215e4570ce42cd5b
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="delegate-a-domain-to-azure-dns"></a>Delegera en domän till Azure DNS

Med Azure DNS kan du vara värd för en DNS-zon och hantera DNS-posterna för en domän i Azure. För att DNS-frågor för en domän ska nå Azure DNS måste domänen delegeras till Azure DNS från den överordnade domänen. Tänk på att Azure DNS inte är domänregistratorn. I artikeln förklaras hur du delegerar din domän till Azure DNS.

Om domänerna köpts från en registrator kan registratorn konfigurera dessa NS-poster. Du behöver inte äga en domän för att kunna skapa en DNS-zon med det domännamnet i Azure DNS. Du behöver äga domänen för att konfigurera delegeringen till Azure DNS med registratorn.

Anta exempelvis att du köper domänen contoso.net och skapar en zon med namnet contoso.net i Azure DNS. Eftersom du är ägare till domänen erbjuder sig registratorn att konfigurera namnserveradresserna (det vill säga NS-posterna) för din domän. Registratorn lagrar dessa NS-poster i den överordnade domänen, ”.net”. Klienter över hela världen kan sedan omdirigeras till din domän i Azure DNS-zonen när du försöker matcha DNS-poster i contoso.net.

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

1. Logga in på Azure Portal.
1. På menyn **Nav** väljer du **Nytt**  > **Nätverk** > **DNS-zon** för att öppna sidan **Skapa en DNS-zon**.

   ![DNS-zon](./media/dns-domain-delegation/dns.png)

1. På sidan **Skapa DNS-zon** anger du följande värden och klickar sedan på **Skapa**:

   | **Inställning** | **Värde** | **Detaljer** |
   |---|---|---|
   |**Namn**|contoso.net|Ange namnet på DNS-zonen.|
   |**Prenumeration**|[Din prenumeration]|Välj den prenumeration där du vill skapa programgatewayen.|
   |**Resursgrupp**|**Skapa ny:** contosoRG|Skapa en resursgrupp. Resursgruppens namn måste vara unikt inom den prenumeration du valde. Mer information om resursgrupper finns i [översikten över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Plats**|Västra USA||

> [!NOTE]
> Platsen för resursgruppen har ingen inverkan på DNS-zonen. Platsen för DNS-zonen är alltid ”global” och visas inte.

## <a name="retrieve-name-servers"></a>Hämta namnservrar

Innan du kan delegera din DNS-zon till Azure DNS måste du känna till namnservrarna för din zon. Azure DNS allokerar namnservrar från en pool varje gång en zon skapas.

1. I Azure Portal klickar du på **Alla resurser** i rutan **Favoriter** för den DNS-zon du skapade. Klicka på DNS-zonen **contoso.net** på sidan **Alla resurser**. Om den prenumeration du valde redan har flera resurser kan du ange **contoso.net** i rutan **Filtrera efter namn** för att enkelt få åtkomst till Application Gateway. 

1. Hämta namnservrarna på sidan DNS-zon. I det här exemplet har zonen contoso.net tilldelats namnservrarnans1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org och ns4-01.azure-dns.info:

   ![Lista över namnservrar](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS skapar automatiskt auktoritativa NS-poster i din zon som innehåller de tilldelade namnservrarna. Om du vill se namnservrarna via Azure PowerShell eller Azure CLI hämtar du bara dessa poster.

I följande exempel finns exempel på hur du kan hämta namnservrarna för en zon i Azure DNS med PowerShell och Azure CLI.

### <a name="powershell"></a>PowerShell

```powershell
# The record name "@" is used to refer to records at the top of the zone.
$zone = Get-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
```

Följande exempel är svaret:

```
Name              : @
ZoneName          : contoso.net
ResourceGroupName : contosorg
Ttl               : 172800
Etag              : 03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5
RecordType        : NS
Records           : {ns1-07.azure-dns.com., ns2-07.azure-dns.net., ns3-07.azure-dns.org.,
                    ns4-07.azure-dns.info.}
Metadata          :
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list --resource-group contosoRG --zone-name contoso.net --type NS --name @
```

Följande exempel är svaret:

```json
{
  "etag": "03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosoRG/providers/Microsoft.Network/dnszones/contoso.net/NS/@",
  "metadata": null,
  "name": "@",
  "nsRecords": [
    {
      "nsdname": "ns1-07.azure-dns.com."
    },
    {
      "nsdname": "ns2-07.azure-dns.net."
    },
    {
      "nsdname": "ns3-07.azure-dns.org."
    },
    {
      "nsdname": "ns4-07.azure-dns.info."
    }
  ],
  "resourceGroup": "contosoRG",
  "ttl": 172800,
  "type": "Microsoft.Network/dnszones/NS"
}
```

## <a name="delegate-the-domain"></a>Delegera domänen

Nu när du har namnservrar och DNS-zonen är skapad måste den överordnade domänen uppdateras med Azure DNS-namnservrarna. Varje registrator har sina egna DNS-hanteringsverktyg för att ändra namnserverposterna för en domän. På registratorns DNS-hanteringssida redigerar du NS-posterna och ersätter NS-posterna med dem som Azure DNS skapat.

När du delegerar en domän till Azure DNS måste du använda namnservernamnen som tillhandahålls av Azure DNS. Vi rekommenderar att du använder alla fyra namnservernamn, oavsett vilket namn din domän har. Domändelegering kräver inte att namnservern använder samma toppnivådomän som din domän.

Använd inte *fästposter* för att peka på IP-adresser för Azure DNS-namnservrar eftersom dessa IP-adresser kan komma att ändras i framtida. Delegering med hjälp av namnservrar i din egen zon, även kallat *vanity name servers*, stöds inte i Azure DNS.

## <a name="verify-that-name-resolution-is-working"></a>Kontrollera att namnmatchningen fungerar

När du har slutfört delegeringen kan du kontrollera att namnmatchningen fungerar med hjälp av ett verktyg som till exempel nslookup för att fråga efter SOA-posten för din zon. (SOA-posten skapas automatiskt när zonen har skapats.)

Du måste inte ange Azure DNS-namnservrarna. Om delegeringen är korrekt konfigurerad hittar den normala DNS-matchningsprocessen namnservrarna automatiskt.

```
nslookup -type=SOA contoso.com
```

Här följer ett exempel på ett svar från föregående kommando:

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.com
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="delegate-subdomains-in-azure-dns"></a>Delegera underdomäner i Azure DNS

Om du vill konfigurera en separat underordnad zon kan du delegera en underdomän i Azure DNS. Anta till exempel att du har konfigurerat och delegerat contoso.net i Azure DNS. Nu behöver du konfigurera en separat underordnad zon, partners.contoso.net.

1. Skapa den underordnade zonen partners.contoso.net i Azure DNS.
2. Leta upp de auktoritativa NS-posterna i den underordnade zonen för att hämta namnservrarna som är värdar för den underordnade zonen i Azure DNS.
3. Delegera den underordnade zonen genom att konfigurera NS-poster i den överordnade zonen som pekar på den underordnade zonen.

### <a name="create-a-dns-zone"></a>Skapa en DNS-zon

1. Logga in på Azure Portal.
1. På menyn **Nav** väljer du **Nytt**  > **Nätverk** > **DNS-zon** för att öppna sidan **Skapa en DNS-zon**.

   ![DNS-zon](./media/dns-domain-delegation/dns.png)

1. På sidan **Skapa DNS-zon** anger du följande värden och klickar sedan på **Skapa**:

   | **Inställning** | **Värde** | **Detaljer** |
   |---|---|---|
   |**Namn**|partners.contoso.net|Ange namnet på DNS-zonen.|
   |**Prenumeration**|[Din prenumeration]|Välj den prenumeration där du vill skapa programgatewayen.|
   |**Resursgrupp**|**Använd befintlig:** contosoRG|Skapa en resursgrupp. Resursgruppens namn måste vara unikt inom den prenumeration du valde. Mer information om resursgrupper finns i [översikten över Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Plats**|Västra USA||

> [!NOTE]
> Platsen för resursgruppen har ingen inverkan på DNS-zonen. Platsen för DNS-zonen är alltid ”global” och visas inte.

### <a name="retrieve-name-servers"></a>Hämta namnservrar

1. I Azure Portal klickar du på **Alla resurser** i rutan **Favoriter** för den DNS-zon du skapade. Markera DNS-zonen **partners.contoso.net** på sidan **Alla resurser**. Om den prenumeration du valde redan har flera resurser kan du ange **partners.contoso.net** i rutan **Filtrera efter namn** för att enkelt få åtkomst till DNS-zonen.

1. Hämta namnservrarna på sidan DNS-zon. I det här exemplet har zonen contoso.net tilldelats namnservrarnans1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org och ns4-01.azure-dns.info:

   ![Lista över namnservrar](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS skapar automatiskt auktoritativa NS-poster i din zon som innehåller de tilldelade namnservrarna.  Om du vill se namnservrarna via Azure PowerShell eller Azure CLI hämtar du bara dessa poster.

### <a name="create-a-name-server-record-in-the-parent-zone"></a>Skapa en namnserverpost i överordnad zon

1. Gå till DNS-zonen **contoso.net** i Azure Portal.
1. Välj **+ Postuppsättning**.
1. Ange följande värden på sidan **Lägg till uppsättning av poster** och välj sedan **OK**:

   | **Inställning** | **Värde** | **Detaljer** |
   |---|---|---|
   |**Namn**|partner|Ange namnet på den underordnade DNS-zonen.|
   |**Typ**|NS|Använd NS för namnserverposter.|
   |**TTL**|1|Ange time to live.|
   |**TTL-enhet**|Timmar|Ange timmar som enheter för time to live.|
   |**NAMNSERVER**|{namnservrar från zonen partners.contoso.net}|Ange alla fyra namnservrarna från partners.contoso.net.zone. |

   ![Värden för namnserverposten](./media/dns-domain-delegation/partnerzone.png)


### <a name="delegate-subdomains-in-azure-dns-by-using-other-tools"></a>Delegera underdomäner i Azure DNS med andra verktyg

I följande exempel får du anvisningar för hur du delegerar underdomäner i Azure DNS med PowerShell och Azure CLI.

#### <a name="powershell"></a>PowerShell

Följande PowerShell-exempel demonstrerar hur det fungerar. Du kan utföra samma steg från Azure Portal, eller via det plattformsoberoende verktyget Azure CLI.

```powershell
# Create the parent and child zones. These can be in the same resource group or different resource groups, because Azure DNS is a global service.
$parent = New-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
$child = New-AzureRmDnsZone -Name partners.contoso.net -ResourceGroupName contosoRG

# Retrieve the authoritative NS records from the child zone as shown in the next example. This information contains the name servers assigned to the child zone.
$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

# Create the corresponding NS record set in the parent zone to complete the delegation. The record set name in the parent zone matches the child zone name (in this case, "partners").
$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
$parent_ns_recordset.Records = $child_ns_recordset.Records
Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset
```

Använd `nslookup` för att kontrollera att allt är korrekt konfigurerat genom att leta upp SOA-posten för den underordnade zonen.

```
nslookup -type=SOA partners.contoso.com
```

```
Server: ns1-08.azure-dns.com
Address: 208.76.47.8

partners.contoso.com
    primary name server = ns1-08.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
#!/bin/bash

# Create the parent and child zones. These can be in the same resource group or different resource groups, because Azure DNS is a global service.
az network dns zone create -g contosoRG -n contoso.net
az network dns zone create -g contosoRG -n partners.contoso.net
```

Hämta namnservrarna för `partners.contoso.net`-zonen från utdata.

```
{
  "etag": "00000003-0000-0000-418f-250de2b2d201",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosorg/providers/Microsoft.Network/dnszones/partners.contoso.net",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "partners.contoso.net",
  "nameServers": [
    "ns1-09.azure-dns.com.",
    "ns2-09.azure-dns.net.",
    "ns3-09.azure-dns.org.",
    "ns4-09.azure-dns.info."
  ],
  "numberOfRecordSets": 2,
  "resourceGroup": "contosorg",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Skapa postuppsättningen och NS-poster för varje namnserver.

```azurecli
#!/bin/bash

# Create the record set
az network dns record-set ns create --resource-group contosorg --zone-name contoso.net --name partners

# Create an NS record for each name server.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns1-09.azure-dns.com.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns2-09.azure-dns.net.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns3-09.azure-dns.org.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns4-09.azure-dns.info.
```

## <a name="delete-all-resources"></a>Ta bort alla resurser

Så här tar du bort alla resurser som skapats i den här artikeln:

1. Klicka på **Alla resurser** i rutan **Favoriter** i Azure Portal. Klicka på resursgruppen **contosorg** på sidan **Alla resurser**. Om den prenumeration du valde redan har flera resurser kan du ange **contosorg** i rutan **Filtrera efter namn** för att enkelt få åtkomst till resursgruppen.
1. Klicka på knappen **Ta bort** på sidan **contosorg**.
1. Du måste ange namnet på resursgruppen i portalen som bekräftelse på att du vill ta bort den. Skriv **contosorg** som resursgruppnamn och välj sedan **Ta bort**. 

När du tar bort en resursgrupp tas alla resurser i resursgruppen bort. Se alltid till att bekräfta innehållet i en resursgrupp innan du tar bort den. Portalen tar bort alla resurser i resursgruppen och sedan tas själva resursgruppen bort. Den här processen tar flera minuter.

## <a name="next-steps"></a>Nästa steg

[Hantera DNS-zoner](dns-operations-dnszones.md)

[Hantera DNS-poster](dns-operations-recordsets.md)
