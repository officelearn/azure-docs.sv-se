---
title: Importera och exportera en domän zonfil till Azure DNS använder Azure CLI 2.0 | Microsoft Docs
description: Lär dig hur du importerar och exporterar en DNS-zonfilen till Azure DNS med hjälp av Azure CLI 2.0
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: f5797782-3005-4663-a488-ac0089809010
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: gwallace
ms.openlocfilehash: 2bd833ab7eb8c7888228a556acdb8b2a24214cd9
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli-20"></a>Importera och exportera en DNS-zonfilen som använder Azure CLI 2.0 

Den här artikeln får du veta hur du importerar och exporterar DNS-zon för filerna för Azure DNS använder Azure CLI 2.0.

## <a name="introduction-to-dns-zone-migration"></a>Introduktion till DNS-zonen migrering

En DNS-zonfilen är en textfil som innehåller information om alla Domain Name System (DNS)-poster i zonen. Här följer ett standardformat, vilket gör den lämplig för överföring av DNS-poster mellan DNS-system. Med hjälp av en zonfil är en snabb, tillförlitlig och enkelt sätt att överföra en DNS-zon i eller utanför Azure DNS.

Azure DNS stöder import och export av filer med hjälp av Azure-kommandoradsgränssnittet (CLI). Zonen filimport är **inte** stöds för närvarande via Azure PowerShell eller Azure-portalen.

Azure CLI 2.0 är ett kommandoradsverktyg för flera plattformar som används för att hantera Azure-tjänster. Den är tillgänglig för Windows, Mac och Linux-plattformarna från den [Azure nedladdningar](https://azure.microsoft.com/downloads/). Plattformsoberoende stöd är viktigt för att importera och exportera filer, eftersom namnet vanligaste serverprogrammet [BINDA](https://www.isc.org/downloads/bind/), vanligtvis körs på Linux.


## <a name="obtain-your-existing-dns-zone-file"></a>Hämta din befintliga DNS-zonfilen

Du måste ha en kopia av filen zonen innan du importerar en DNS-zonfilen till Azure DNS. Källan för den här filen är beroende av DNS-zon som finns för närvarande.

* Om DNS-zonen finns av en partner-tjänst (till exempel en domänregistrator, dedikerad DNS-värdtjänsten eller alternativa molnprovider), ska tjänsten ger möjlighet att hämta DNS-zonfilen.
* Om DNS-zonen finns på Windows DNS standardmappen för zonen filerna är **%systemroot%\system32\dns**. Den fullständiga sökvägen till en fil för varje zon visas också på den **allmänna** för DNS-konsolen.
* Om DNS-zonen finns med hjälp av BIND, platsen för zonfilen för varje zon har angetts i konfigurationsfilen BIND **named.conf**.

> [!NOTE]
> Zonen filer som hämtas från GoDaddy har ett avvikande något format. Du behöver åtgärda detta innan du importerar dessa filer till Azure DNS.
>
> DNS-namn i RDATA för varje DNS-posten har angetts som ett fullständigt kvalificerat namn, men de inte har en avslutande ””. Det innebär att de tolkas av andra DNS-system som relativa namn. Du måste redigera zonfilen för att lägga till den avslutande ””. i sina namn innan du importerar dem till Azure DNS.
>
> Till exempel bör CNAME-posten ”www 3600 i CNAME contoso.com” ändras till ”www 3600 i CNAME contoso.com”.
> (med en avslutande ””.).

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importera en DNS-zonfilen till Azure DNS

Om du importerar en zonfil skapas en ny zon i Azure DNS om det inte redan finns. Om zonen redan slås postuppsättningar i zonen filen samman med de befintliga postuppsättningar.

### <a name="merge-behavior"></a>Sammanfoga beteende

* Som standard kombineras befintliga och nya postuppsättningar. Identiska poster i en sammanfogad postuppsättning är ta bort dubbletter.
* Time to live (TTL) redan befintliga uppsättningar av poster används när postuppsättningar slås samman.
* Start av Authority (SOA)-parametrar (utom `host`) alltid tas från importerade zonfilen. På samma sätt för namnserverposten inställd på zonens apex, hämtas TTL-värdet alltid från den importera zonfilen.
* En importerad CNAME ersätter inte en befintlig CNAME-post med samma namn.  
* En konflikt uppstår mellan en CNAME-post och en annan post med samma namn men med annan typ (oavsett vilken finns en befintlig eller ny), bevaras den befintliga posten. 

### <a name="additional-information-about-importing"></a>Mer information om hur du importerar

Följande information ger ytterligare teknisk information om zonen import-processen.

* Den `$TTL` direktiv är valfritt och stöds. När ingen `$TTL` direktiv ges, utan en explicit TTL-poster är importerade inställd på en standard-TTL 3600 sekunder. När två poster i samma postuppsättning ange olika TTLs, används det lägsta värdet.
* Den `$ORIGIN` direktiv är valfritt och stöds. När ingen `$ORIGIN` har angetts används standardvärdet är zonnamnet som anges på kommandoraden (plus den avslutande ””.).
* Den `$INCLUDE` och `$GENERATE` direktiven stöds inte.
* Dessa typer stöds: A, AAAA, CNAME, MX, NS, SOA, SRV och TXT.
* SOA-posten skapas automatiskt av Azure DNS när en zon skapas. När du importerar en zonfil alla SOA-parametrarna hämtas från zonfilen *utom* den `host` parameter. Den här parametern används det värde som tillhandahålls av Azure DNS. Det beror på att den här parametern måste referera till den primära namnservern som tillhandahålls av Azure DNS.
* Ange på zonens apex namnserverposten skapas också automatiskt av Azure DNS när zonen skapas. Endast TTL-värdet för den här postuppsättning importeras. Dessa poster innehålla servernamnen namn tillhandahålls av Azure DNS. Postdata skrivs inte över med de värden som finns i filen importerade zoner.
* Under Public Preview stöder Azure DNS bara en sträng TXT-poster. Flersträngiga TXT-poster är sammanfogas och trunkeras till 255 tecken.

### <a name="cli-format-and-values"></a>CLI-format och värden

Formatet på Azure CLI-kommandot för att importera en DNS-zon är:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Värden:

* `<resource group>` är namnet på resursgruppen för zonen i Azure DNS.
* `<zone name>` är namnet på zonen.
* `<zone file name>` är sökvägen för zonen-fil som ska importeras.

Om en zon med det här namnet inte finns i resursgruppen, skapas den åt dig. Om zonen redan slås importerade postuppsättningar samman med befintliga postuppsättningar. 


### <a name="step-1-import-a-zone-file"></a>Steg 1. Importera en zonfil

Så här importerar du en zonfil för zonen **contoso.com**.

1. Om du inte har någon redan måste du skapa en resursgrupp för Resource Manager.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. Så här importerar du zonen **contoso.com** från filen **contoso.com.txt** till en ny DNS-zon i resursgruppen **myresourcegroup**, ska du köra kommandot `az network dns zone import` .<BR>Det här kommandot laddar zonfilen och Parsar den. Kommandot Kör en serie kommandon på Azure DNS-tjänsten för att skapa zonen och alla postuppsättningar i zonen. Kommandot rapporterar förlopp i konsolfönstret, tillsammans med eventuella fel eller varningar. Det kan ta några minuter att importera en stor zonfil eftersom postuppsättningar skapas i serien.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Steg 2. Verifiera zonen

För att verifiera DNS-zonen när du importerar filen, kan du använda någon av följande metoder:

* Du kan ange poster med hjälp av följande Azure CLI-kommando:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Du kan visa en lista med poster med hjälp av PowerShell-cmdleten `Get-AzureRmDnsRecordSet`.
* Du kan använda `nslookup` att verifiera namnmatchning för posterna. Eftersom zonen inte delegerad ännu, måste du ange rätt Azure DNS-namnservrar explicit. I följande exempel visas hur du hämtar servernamnen namn tilldelas zonen. Detta visar även hur du fråga ”www”-post med hjälp av `nslookup`.

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z  --output json 
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

När du har kontrollerat att zonen har importerats korrekt, måste du uppdatera DNS-delegeringen för att peka till Azure DNS-namnservrar. Mer information finns i artikeln [uppdatera DNS-delegeringen](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportera en DNS-zonfilen från Azure DNS

Formatet på Azure CLI-kommandot för att importera en DNS-zon är:

```azurecli
az network dns zone export -g <resource group> -z <zone name> -f <zone file name>
```

Värden:

* `<resource group>` är namnet på resursgruppen för zonen i Azure DNS.
* `<zone name>` är namnet på zonen.
* `<zone file name>` är sökvägen för zonen-fil som ska exporteras.

Som med importen zon måste du först logga in, väljer din prenumeration och konfigurera Azure CLI för att använda Resource Manager-läge.

### <a name="to-export-a-zone-file"></a>Så här exporterar du en zonfil

Så här exporterar du den befintliga Azure DNS-zonen **contoso.com** i resursgruppen **myresourcegroup** till filen **contoso.com.txt** (i den aktuella mappen), kör `azure network dns zone export`. Det här kommandot anropar Azure DNS-tjänsten för att räkna upp postuppsättningar i zonen och exportera resultaten till en kompatibel BINDNING zonfil.

    ```
    az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```
