---
title: Importera och exportera en domänzonsfil - Azure CLI
titleSuffix: Azure DNS
description: Lär dig hur du importerar och exporterar en DNS-zonfil till Azure DNS med hjälp av Azure CLI
services: dns
author: rohinkoul
ms.service: dns
ms.date: 4/3/2019
ms.author: rohink
ms.topic: conceptual
ms.openlocfilehash: a5c2fdde564eba2d95e7f14f4d47e4d381739d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365176"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importera och exportera en DNS-zonfil med Azure CLI

I den här artikeln får du lära dig hur du importerar och exporterar DNS-zonfiler för Azure DNS med Hjälp av Azure CLI.

## <a name="introduction-to-dns-zone-migration"></a>Introduktion till MIGRERING AV DNS-zon

En DNS-zonfil är en textfil som innehåller information om varje DNS-post (Domain Name System) i zonen. Den följer ett standardformat, vilket gör det lämpligt för överföring av DNS-poster mellan DNS-system. Att använda en zonfil är ett snabbt, tillförlitligt och bekvämt sätt att överföra en DNS-zon till eller från Azure DNS.

Azure DNS stöder import och export av zonfiler med hjälp av Azure command-line interface (CLI). Import av zonfiler stöds för närvarande **inte** via Azure PowerShell eller Azure-portalen.

Azure CLI är ett plattformsoberoende kommandoradsverktyg som används för att hantera Azure-tjänster. Den är tillgänglig för Windows-, Mac- och Linux-plattformarna från [sidan Azure downloads](https://azure.microsoft.com/downloads/). Plattformsoberoende stöd är viktigt för import och export av zonfiler, eftersom den vanligaste namnserverprogramvaran, [BIND](https://www.isc.org/downloads/bind/), vanligtvis körs på Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Skaffa din befintliga DNS-zonfil

Innan du importerar en DNS-zonfil till Azure DNS måste du skaffa en kopia av zonfilen. Filens källa beror på var DNS-zonen för närvarande finns.

* Om dns-zonen är värd för en partnertjänst (till exempel en domänregistrator, dedikerad DNS-värdleverantör eller alternativ molnleverantör) bör den tjänsten ge möjlighet att hämta DNS-zonfilen.
* Om DNS-zonen finns i Windows DNS är standardmappen för zonfilerna **%systemroot%\system32\dns**. Den fullständiga sökvägen till varje zonfil visas också på fliken **Allmänt** i DNS-konsolen.
* Om DNS-zonen är värd med BIND anges platsen för zonfilen för varje zon i BIND-konfigurationsfilen **named.conf**.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importera en DNS-zonfil till Azure DNS

Om du importerar en zonfil skapas en ny zon i Azure DNS om det inte redan finns någon. Om zonen redan finns måste postuppsättningarna i zonfilen slås samman med de befintliga postuppsättningarna.

### <a name="merge-behavior"></a>Koppla beteende

* Som standard slås befintliga och nya postuppsättningar samman. Identiska poster i en kopplad postuppsättning avd dupliceras.
* När postuppsättningar slås samman används tiden för att leva (TTL) för befintliga postuppsättningar.
* Soa-parametrar (Start of `host`Authority) (utom) hämtas alltid från den importerade zonfilen. På samma sätt tas TTL alltid från den importerade zonfilen för namnserverposten som anges vid zonapexen.
* En importerad CNAME-post ersätter inte en befintlig CNAME-post med samma namn.  
* När en konflikt uppstår mellan en CNAME-post och en annan post med samma namn men annan typ (oavsett vilket som finns eller är ny) behålls den befintliga posten. 

### <a name="additional-information-about-importing"></a>Ytterligare information om hur du importerar

Följande anteckningar innehåller ytterligare teknisk information om zonimportprocessen.

* Direktivet `$TTL` är frivilligt, och det stöds. När `$TTL` inget direktiv anges importeras poster utan explicit TTL som är inställda på en standard-TTL på 3600 sekunder. När två poster i samma postuppsättning anger olika TTLs används det lägre värdet.
* Direktivet `$ORIGIN` är frivilligt, och det stöds. När `$ORIGIN` nej anges är standardvärdet som används det zonnamn som anges på kommandoraden (plus avslutande ".").
* Direktiven `$GENERATE` och direktiven `$INCLUDE` stöds inte.
* Dessa posttyper stöds: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV och TXT.
* SOA-posten skapas automatiskt av Azure DNS när en zon skapas. När du importerar en zonfil hämtas alla SOA-parametrar från zonfilen *utom* parametern. `host` Den här parametern använder värdet som tillhandahålls av Azure DNS. Detta beror på att den här parametern måste referera till den primära namnservern som tillhandahålls av Azure DNS.
* Namnserverposten som anges vid zonen apex skapas också automatiskt av Azure DNS när zonen skapas. Endast TTL för den här postuppsättningen importeras. Dessa poster innehåller namnservernamnen som tillhandahålls av Azure DNS. Postdata skrivs inte över av värdena i den importerade zonfilen.
* Under offentlig förhandsversion stöder Azure DNS endast TXT-poster med en sträng. Multistring TXT poster är sammanfogade och trunkeras till 255 tecken.

### <a name="cli-format-and-values"></a>CLI-format och värden

Formatet för Azure CLI-kommandot för att importera en DNS-zon är:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Värden:

* `<resource group>`är namnet på resursgruppen för zonen i Azure DNS.
* `<zone name>`är namnet på zonen.
* `<zone file name>`är sökvägen/namnet på den zonfil som ska importeras.

Om det inte finns en zon med det här namnet i resursgruppen skapas den åt dig. Om zonen redan finns slås de importerade postuppsättningarna samman med befintliga postuppsättningar. 

### <a name="step-1-import-a-zone-file"></a>Steg 1. Importera en zonfil

Så här importerar du en zonfil för zonen **contoso.com**.

1. Om du inte redan har någon måste du skapa en Resurshanterarens resursgrupp.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. Om du vill importera zonen **contoso.com** från filen **contoso.com.txt** till en ny DNS-zon i resursgruppen **myresourcegroup**kör du kommandot `az network dns zone import`.<BR>Det här kommandot läser in zonfilen och tolkar den. Kommandot kör en serie kommandon på Azure DNS-tjänsten för att skapa zonen och alla postuppsättningar i zonen. Kommandot rapporterar förloppet i konsolfönstret, tillsammans med eventuella fel eller varningar. Eftersom postuppsättningar skapas i serie kan det ta några minuter att importera en stor zonfil.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Steg 2. Verifiera zonen

Om du vill verifiera DNS-zonen när du har importerat filen kan du använda någon av följande metoder:

* Du kan lista posterna med hjälp av följande Azure CLI-kommando:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Du kan lista posterna med `az network dns record-set ns list`kommandot Azure CLI .
* Du kan `nslookup` använda för att verifiera namnmatchning för posterna. Eftersom zonen inte är delegerad ännu måste du uttryckligen ange rätt Azure DNS-namnservrar. Följande exempel visar hur du hämtar namnservernamnen som tilldelats zonen. Detta visar också hur du frågar `nslookup`"www"-posten med hjälp av .

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="step-3-update-dns-delegation"></a>Steg 3. Uppdatera DNS-delegering

När du har verifierat att zonen har importerats korrekt måste du uppdatera DNS-delegeringen så att den pekar på Azure DNS-namnservrarna. Mer information finns i artikeln [Uppdatera DNS-delegeringen](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportera en DNS-zonfil från Azure DNS

Formatet för Azure CLI-kommandot för att exportera en DNS-zon är:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Värden:

* `<resource group>`är namnet på resursgruppen för zonen i Azure DNS.
* `<zone name>`är namnet på zonen.
* `<zone file name>`är sökvägen/namnet på den zonfil som ska exporteras.

Precis som med zonimporten måste du först logga in, välja din prenumeration och konfigurera Azure CLI för att använda Resource Manager-läge.

### <a name="to-export-a-zone-file"></a>Så här exporterar du en zonfil

Om du vill exportera den befintliga Azure DNS-zonen **contoso.com** i resursgruppen **myresourcegroup** till filen **contoso.com.txt** (i den aktuella mappen) kör du `azure network dns zone export`. Det här kommandot anropar Azure DNS-tjänsten för att räkna upp postuppsättningar i zonen och exportera resultaten till en BIND-kompatibel zonfil.

```azurecli
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [hanterar postuppsättningar och poster](dns-getstarted-create-recordset-cli.md) i DNS-zonen.

* Lär dig hur du [delegerar domänen till Azure DNS](dns-domain-delegation.md).
