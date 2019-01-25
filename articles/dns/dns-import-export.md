---
title: Importera och exportera en zonfil domän till Azure DNS med Azure CLI | Microsoft Docs
description: Lär dig att importera och exportera en DNS-zonfil till Azure DNS med hjälp av Azure CLI
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: f5797782-3005-4663-a488-ac0089809010
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: victorh
ms.openlocfilehash: 37dd51a60d9ec6e018f58e2cfd593490d126670b
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888659"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importera och exportera en DNS-zonfil med Azure CLI 

Den här artikeln vägleder dig igenom hur du importera och exportera DNS-zonfiler för Azure DNS med Azure CLI.

## <a name="introduction-to-dns-zone-migration"></a>Introduktion till DNS-zon migrering

En DNS-zonfil är en textfil som innehåller information om varje Domain Name System (DNS) post i zonen. Det följer ett standardformat som gör det lämpligt för överföring av DNS-poster mellan DNS-system. Med hjälp av en zonfil är ett snabbt, tillförlitligt och praktiskt sätt att överföra en DNS-zon till eller från Azure DNS.

Azure DNS stöder import och export av filer med hjälp av kommandoradsgränssnittet (CLI). Zonens filimport är **inte** stöds för närvarande via Azure PowerShell eller Azure-portalen.

Azure CLI är ett kommandoradsverktyg för flera plattformar som används för att hantera Azure-tjänster. Den är tillgänglig för Windows, Mac och Linux-plattformar från den [Azure hämtningssidan](https://azure.microsoft.com/downloads/). Support för alla plattformar är viktigt för import och export av filer, eftersom namnet vanligaste serverprogramvaran [BINDA](https://www.isc.org/downloads/bind/), vanligtvis körs på Linux.


## <a name="obtain-your-existing-dns-zone-file"></a>Hämta din befintliga DNS-zonfil

Innan du importerar en DNS-zonfil till Azure DNS måste du skaffa en kopia av zonfilen. Källan för den här filen är beroende av där DNS-zon finns för närvarande.

* Om din DNS-zon är värd för en partner-tjänst (till exempel en domänregistrator, dedikerad DNS-värdtjänst eller annan molnleverantör), ska tjänsten ger möjlighet att hämta DNS-zonfilen.
* Om din DNS-zon finns på Windows DNS, standardmappen för zonen filerna är **%systemroot%\system32\dns**. Den fullständiga sökvägen till varje zonfil visar även på den **Allmänt** fliken DNS-konsolen.
* Om din DNS-zon är värd med BINDNING, platsen för zonfilen för varje zon har angetts i konfigurationsfilen för BINDNING **named.conf**.

> [!NOTE]
> Zonfiler som hämtas från GoDaddy har ett något avvikande format. Du måste åtgärda detta innan du importerar zonfilerna till Azure DNS.
>
> DNS-namn i RDATA för varje DNS-post har angetts som fullständiga namn, men de inte har en avslutande ””. Det innebär att de tolkas av andra DNS-system som relativa namn. Du måste redigera zonfil för att lägga till den avslutande ””. i sina namn innan du importerar dem till Azure DNS.
>
> Till exempel bör CNAME-post ”www 3600 i CNAME contoso.com” ändras till ”www 3600 i CNAME contoso.com”.
> (med avslutande ””.).

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importera en fil för DNS-zon till Azure DNS

Importera en zonfil skapar en ny zon i Azure DNS om det inte redan finns. Om det finns redan i zonen, måste postuppsättningar i zonfilen slås samman med de befintliga uppsättningarna av poster.

### <a name="merge-behavior"></a>Sammanfoga beteende

* Som standard slås befintliga och nya postuppsättningar samman. Identiska poster i en sammanfogad postuppsättning är ta bort duplicerade.
* När postuppsättningar slås samman används time to live (TTL) redan befintliga uppsättningar av poster.
* Början av utfärdare (SOA)-parametrar (utom `host`) alltid är hämtade från zonfilen importerade. På samma sätt för namnserverposten som angetts i basdomänen, hämtas TTL-värdet alltid från den importerade zonfilen.
* En importerade CNAME ersätter inte en befintlig CNAME-post med samma namn.  
* En konflikt uppstår mellan en CNAME-post och en annan post med samma namn men olika typ (oavsett vilken finns en befintlig eller ny), bevaras den befintliga posten. 

### <a name="additional-information-about-importing"></a>Mer information om hur du importerar

Följande information ger ytterligare teknisk information om zonen importprocessen.

* Den `$TTL` direktiv är valfritt och stöds. När ingen `$TTL` direktiv ges, utan en explicit TTL-poster är importerade inställd på en standard-TTL på 3 600 sekunder. När två poster i samma postuppsättningen anger olika TTL: er, används det lägre värdet.
* Den `$ORIGIN` direktiv är valfritt och stöds. När ingen `$ORIGIN` har angetts används standardvärdet är zonnamnet som anges på kommandoraden (plus det avslutande ””.).
* Den `$INCLUDE` och `$GENERATE` direktiv stöds inte.
* Dessa typer av poster stöds: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV, and TXT.
* SOA-posten skapas automatiskt av Azure DNS när en zon skapas. När du importerar en zonfil alla SOA-parametrar är hämtade från zonfilen *utom* den `host` parametern. Den här parametern används värdet som tillhandahålls av Azure DNS. Det beror på att den här parametern måste referera till den primära namnservern som tillhandahålls av Azure DNS.
* Namn på server posten i basdomänen skapas också automatiskt av Azure DNS när zonen skapas. Endast TTL-värdet för den här uppsättningen av poster har importerats. De här posterna innehåller namnservernamnen som tillhandahålls av Azure DNS. Postdata skrivs inte över med de värden som finns i importerade zonfilen.
* During Public Preview, Azure DNS supports only single-string TXT records. Flersträngiga TXT-poster är sammanfogade och trunkeras till 255 tecken.

### <a name="cli-format-and-values"></a>CLI-format och värden

Formatet för Azure CLI-kommando för att importera en DNS-zon är:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Värden:

* `<resource group>` är namnet på resursgruppen för zonen i Azure DNS.
* `<zone name>` är namnet på zonen.
* `<zone file name>` är sökvägen för zonen-fil som ska importeras.

Om en zon med det här namnet inte finns i resursgruppen, skapas den åt dig. Om det finns redan i zonen, slås importerade postuppsättningar samman med befintliga uppsättningar av poster. 


### <a name="step-1-import-a-zone-file"></a>Steg 1. Importera en zonfil

Importera en zonfil för zonen **contoso.com**.

1. Om du inte har ett redan, måste du skapa en resursgrupp för Resource Manager.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. Importera zonen **contoso.com** från filen **contoso.com.txt** till en ny DNS-zon i resursgruppen **myresourcegroup**, ska du köra kommandot `az network dns zone import` .<BR>Det här kommandot laddar zonfilen och Parsar den. Kommandot Kör en serie med kommandon i Azure DNS-tjänsten för att skapa zonen och alla postuppsättningar i zonen. Kommandot rapporterar förlopp i konsolfönstret, tillsammans med eventuella fel eller varningar. Eftersom postuppsättningar skapas i serien, kan det ta några minuter att importera en stor zon-fil.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Steg 2. Verifiera zonen

För att verifiera DNS-zonen när du importerar filen, kan du använda någon av följande metoder:

* Du kan ange poster med hjälp av följande Azure CLI-kommando:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Du kan ange poster med hjälp av PowerShell-cmdleten `Get-AzureRmDnsRecordSet`.
* Du kan använda `nslookup` att verifiera namnmatchning för poster. Eftersom zonen inte delegeras ännu, måste du uttryckligen ange rätt Azure DNS-namnservrarna. I följande exempel visas hur du hämtar namnservernamnen tilldelas zonen. Detta visar även hur du frågar ”www”-post med hjälp av `nslookup`.

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

När du har kontrollerat att zonen har importerats kan behöva du uppdatera DNS-delegeringen så att den pekar till Azure DNS-namnservrarna. Mer information finns i artikeln [uppdatera DNS-delegeringen](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportera en DNS-zonfil från Azure DNS

Formatet för Azure CLI-kommando för att importera en DNS-zon är:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Värden:

* `<resource group>` är namnet på resursgruppen för zonen i Azure DNS.
* `<zone name>` är namnet på zonen.
* `<zone file name>` är sökvägen för zonen-fil som ska exporteras.

Som med zon importen kan du först logga in, Välj din prenumeration och konfigurera Azure CLI för att använda Resource Manager-läge.

### <a name="to-export-a-zone-file"></a>Så här exporterar du en zonfil

Så här exporterar du den befintliga Azure DNS-zonen **contoso.com** i resursgruppen **myresourcegroup** till filen **contoso.com.txt** (i den aktuella mappen), kör `azure network dns zone export`. Det här kommandot anropar Azure DNS-tjänsten för att räkna upp postuppsättningar i zonen och exportera resultaten till en BINDNING-kompatibla zonfil.

    ```
    az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```
