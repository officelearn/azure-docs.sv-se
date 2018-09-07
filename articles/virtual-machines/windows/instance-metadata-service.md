---
title: Azure Instance Metadata Service | Microsoft Docs
description: RESTful-gränssnitt att få information om Windows VM-beräkning, nätverk och kommande underhåll.
services: virtual-machines-windows
documentationcenter: ''
author: harijayms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: harijayms
ms.openlocfilehash: ccaa6e79d9a24409b8c905561b265c70ea781dc2
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022583"
---
# <a name="azure-instance-metadata-service"></a>Azure Instance Metadata service


Azure Instance Metadata Service innehåller information om hur du kör instanser av virtuella datorer som kan användas för att hantera och konfigurera dina virtuella datorer.
Detta omfattar information som SKU: N, nätverkskonfiguration och kommande underhåll. Mer information om vilken typ av information är tillgänglig finns [metadatakategorier](#instance-metadata-data-categories).

Azures Instance Metadata Service är en REST-slutpunkt som är tillgängliga för alla virtuella IaaS-datorer skapas via den [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Slutpunkten är tillgänglig på en välkänd icke-dirigerbara IP-adress (`169.254.169.254`) som kan nås från den virtuella datorn.

> [!IMPORTANT]
> Den här tjänsten är **allmänt tillgänglig** i alla Azure-regioner.  Den får regelbundet uppdateringar om du vill exponera ny information om instanser av virtuella datorer. Den här sidan visar det uppdaterade [datakategorier](#instance-metadata-data-categories) tillgängliga.

## <a name="service-availability"></a>Tjänsttillgänglighet
Tjänsten är tillgänglig i allmänt tillgängliga Azure-regioner. Inte alla API-versionen kan finnas i alla Azure-regioner.

Regioner                                        | Tillgänglighet?                                 | Versioner som stöds
-----------------------------------------------|-----------------------------------------------|-----------------
[Alla allmänt tillgängliga Azure-regioner globalt](https://azure.microsoft.com/regions/)     | Allmänt tillgänglig   | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Allmänt tillgänglig | 2018-02-01 till 2017-12-01 till 2017-08-01 till 2017-04-02
[Azure Kina](https://www.azure.cn/)                                                           | Allmänt tillgänglig | 2018-02-01 till 2017-12-01 till 2017-08-01 till 2017-04-02
[Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/)                    | Allmänt tillgänglig | 2018-02-01 till 2017-12-01 till 2017-08-01 till 2017-04-02

Den här tabellen uppdateras när det finns uppdateringar av tjänsten och eller nya versioner som stöds är tillgängliga

Om du vill prova Instance Metadata Service, skapa en virtuell dator från [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) eller [Azure-portalen](http://portal.azure.com) i ovanstående regioner och följer exemplen nedan.

## <a name="usage"></a>Användning

### <a name="versioning"></a>Versionshantering
Instance Metadata Service är en ny version. Versioner är obligatoriska och den aktuella versionen på Global Azure är `2018-04-02`. Aktuella versioner som stöds är (2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02)

> [!NOTE] 
> Föregående förhandsversionerna av schemalagda händelser stöds {senaste} som den api-versionen. Det här formatet stöds inte längre och kommer att bli inaktuella i framtiden.

När nya versioner läggs kan äldre versioner fortfarande användas för kompatibilitet om skripten har beroenden på specifika dataformat. Men kanske den föregående förhandsversionen (2017-03-01) inte tillgänglig när tjänsten är allmänt tillgänglig.

### <a name="using-headers"></a>Med hjälp av rubriker
När du frågar Instance Metadata Service måste du ange rubriken `Metadata: true` att se till att begäran inte har omdirigerats oavsiktligt.

### <a name="retrieving-metadata"></a>Hämta metadata

Instans-metadata är tillgängliga för att köra virtuella datorer som skapas/hanteras med hjälp av [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Åtkomst till alla datakategorier för en virtuell datorinstans med hjälp av följande begäran:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE] 
> Alla instans metadatafrågor är skiftlägeskänsliga.

### <a name="data-output"></a>Utdata
Som standard Instance Metadata Service returnerar data i JSON-format (`Content-Type: application/json`). Dock returnerar olika API: er data i olika format om så krävs.
I följande tabell är en referens för andra dataformat som kan ha stöd för API: er.

API | Standardformat för Data | Andra format
--------|---------------------|--------------
/Instance | JSON | text
/scheduledevents | JSON | inga

Ange det begärda formatet som en frågesträngsparameter i begäran för att komma åt en icke-standard svarsformat. Exempel:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

### <a name="security"></a>Säkerhet
Instance Metadata Service-slutpunkten är enbart tillgänglig från den VM-instansen som som körs på en icke-dirigerbara IP-adress. Dessutom kan ett begärande med ett `X-Forwarded-For` rubrik avvisas av tjänsten.
Begäranden måste också innehålla en `Metadata: true` rubrik så att den faktiska begäran direkt var avsett och inte en del av oavsiktlig omdirigering. 

### <a name="error"></a>Fel
Om det finns ett dataelement hittades inte eller en felaktig begäran, returnerar Instance Metadata Service standard HTTP-fel. Exempel:

HTTP-statuskod | Orsak
----------------|-------
200 OK |
400 Felaktig förfrågan | Saknas `Metadata: true` rubrik
404 Hittades inte | Det begärda elementet finns inte 
405 Metoden tillåts inte | Endast `GET` och `POST` stöds
429 för många begäranden | API: et stöder för närvarande upp till 5 frågor per sekund
500 tjänstfel     | Försök igen om en stund

### <a name="examples"></a>Exempel

> [!NOTE] 
> Alla API-svar är JSON-strängar. Alla följande exempelsvar är pretty ut för läsbarhet.

#### <a name="retrieving-network-information"></a>Hämtar nätverksinformation

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Svar**

> [!NOTE] 
> Svaret är en JSON-sträng. Följande exempelsvar är pretty ut för läsbarhet.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>Hämta offentlig IP-adress

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Hämta alla metadata för en instans

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-12-01"
```

**Svar**

> [!NOTE] 
> Svaret är en JSON-sträng. Följande exempelsvar är pretty ut för läsbarhet.

```json
{
  "compute": {
    "location": "westus",
    "name": "avset2",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "placementGroupId": "",
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "publisher": "Canonical",
    "resourceGroupName": "myrg",
    "sku": "16.04-LTS",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "",
    "version": "16.04.201708030",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_D1",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Hämta metadata på Windows-datorer

**Förfrågan**

Instans metadata kan hämtas i Windows via den `curl` program: 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-08-01 | select -ExpandProperty Content
```

Eller via den `Invoke-RestMethod` PowerShell-cmdlet:
    
```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-08-01 -Method get 
```

**Svar**

> [!NOTE] 
> Svaret är en JSON-sträng. Följande exempelsvar är pretty ut för läsbarhet.

```json
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>Instans datakategorier för metadata
Följande datakategorier är tillgängliga via Instance Metadata Service:

Data | Beskrivning | Version introducerades 
-----|-------------|-----------------------
location | Azure-Region den virtuella datorn körs i | 2017-04-02 
namn | Namnet på den virtuella datorn | 2017-04-02
erbjudande | Ger information om VM-avbildning. Det här värdet är endast tillgänglig för avbildningar som distribueras från Azures avbildningsgalleri. | 2017-04-02
utgivare | Utgivaren av den Virtuella datoravbildningen | 2017-04-02
SKU: n | Specifika SKU för VM-avbildning | 2017-04-02
version | Versionen av VM-avbildning | 2017-04-02
osType | Linux eller Windows | 2017-04-02
platformUpdateDomain |  [Uppdateringsdomän](manage-availability.md) Virtuellt datorn körs | 2017-04-02
platformFaultDomain | [Feldomän](manage-availability.md) Virtuellt datorn körs | 2017-04-02
vmId | [Unik identifierare](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) för den virtuella datorn | 2017-04-02
vmSize | [VM-storlek](sizes.md) | 2017-04-02
subscriptionId | Azure-prenumeration för den virtuella datorn | 2017-08-01
tags | [Taggar](../../azure-resource-manager/resource-group-using-tags.md) för den virtuella datorn  | 2017-08-01
resourceGroupName | [Resursgrupp](../../azure-resource-manager/resource-group-overview.md) för den virtuella datorn | 2017-08-01
placementGroupId | [Placeringsgrupp](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) för VM-skalningsuppsättning | 2017-08-01
plan | [Planera] (https://docs.microsoft.com/en-us/rest/api/compute/virtualmachines/createorupdate#plan) för en virtuell dator i den är en Azure Marketplace-avbildning, som innehåller namn, produkt och utgivare | 2017-04-02
publicKeys | Samling med offentliga nycklar [https://docs.microsoft.com/en-us/rest/api/compute/virtualmachines/createorupdate#sshpublickey] tilldelats den virtuella datorn och sökvägar | 2017-04-02
vmScaleSetName | [Namn på virtuell dator ScaleSet](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) för VM-skalningsuppsättning | 2017-12-01
zon | [Tillgänglighetszon](../../availability-zones/az-overview.md) för den virtuella datorn | 2017-12-01 
ipv4/privateIpAddress | Lokala IPv4-adressen för den virtuella datorn | 2017-04-02
IPv4/publicIpAddress | Offentliga IPv4-adressen för den virtuella datorn | 2017-04-02
undernät/adress | Undernätadress för den virtuella datorn | 2017-04-02 
undernätsprefix / | Undernätets prefix, exempel 24 | 2017-04-02 
IPv6/IP-adress | Den lokala IPv6-adressen för den virtuella datorn | 2017-04-02 
MAC-adress | Mac-adress för virtuell dator | 2017-04-02 
scheduledevents | Se [schemalagda händelser](scheduled-events.md) | 2017-08-01
identity | (Förhandsversion) Hanterad tjänstidentitet. Se [hämta en åtkomsttoken](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01

## <a name="example-scenarios-for-usage"></a>Exempelscenarier för användning  

### <a name="tracking-vm-running-on-azure"></a>Spårning av virtuell dator som kör på Azure

Du kan behöva att spåra hur många virtuella datorer som kör ditt program eller agenter som behöver spåra unikhet för den virtuella datorn som en tjänsteleverantör. För att kunna hämta ett unikt ID för en virtuell dator, använder de `vmId` från Instance Metadata Service.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Svar**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Placering av behållare, data-partitionerna baserat fel/uppdatera domän 

För vissa scenarier, placering av olika repliker är av yttersta vikt. Till exempel [HDFS repliken placering](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) eller placering av behållare via en [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) kanske du behöver känna till den `platformFaultDomain` och `platformUpdateDomain` Virtuellt datorn körs.
Du kan också använda [Tillgänglighetszoner](../../availability-zones/az-overview.md) att fatta beslut om dessa-instanserna.
Du kan fråga data direkt via Instance Metadata Service.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" 
```

**Svar**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Få mer information om den virtuella datorn under supportärende 

En tjänstleverantör, kan du få en supportsamtal där du vill veta mer om den virtuella datorn. Be kunden att dela beräkning metadata kan ge grundläggande information för supportpersonal veta om vilken typ av virtuell dator på Azure. 

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Svar**

> [!NOTE] 
> Svaret är en JSON-sträng. Följande exempelsvar är pretty ut för läsbarhet.

```json
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```


### <a name="getting-azure-environment-where-the-vm-is-running"></a>Hämta Azure-miljön där Virtuellt datorn körs 

Azure har olika soverign moln som [Azure Government](https://azure.microsoft.com/overview/clouds/government/) , du ibland behöva till Azure-miljön att fatta beslut om vissa runtime. Följande exempel visar hur du kan åstadkomma detta

**Förfrågan**

```
  $metadataResponse = Invoke-WebRequest "http://169.254.169.254/metadata/instance/compute?api-version=2018-02-01" -H @{"Metadata"="true"} -UseBasicParsing
  $metadata = ConvertFrom-Json ($metadataResponse.Content)
 
  $endpointsResponse = Invoke-WebRequest "https://management.azure.com/metadata/endpoints?api-version=2017-12-01" -UseBasicParsing
  $endpoints = ConvertFrom-Json ($endpointsResponse.Content)
 
  foreach ($cloud in $endpoints.cloudEndpoint.PSObject.Properties) {
    $matchingLocation = $cloud.Value.locations | Where-Object {$_ -match $metadata.location}
    if ($matchingLocation) {
      $cloudName = $cloud.name
      break
    }
  }
 
  $environment = "Unknown"
  switch ($cloudName) {
    "public" { $environment = "AzureCloud"}
    "usGovCloud" { $environment = "AzureUSGovernment"}
    "chinaCloud" { $environment = "AzureChinaCloud"}
    "germanCloud" { $environment = "AzureGermanCloud"}
  }
 
  Write-Host $environment
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Exempel på hur metadatatjänsten med olika språk på den virtuella datorn 

Språk | Exempel 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go            
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata
    

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
1. Jag får felet `400 Bad Request, Required metadata header not specified`. Vad betyder detta?
   * Instance Metadata Service kräver rubriken `Metadata: true` som ska skickas i begäran. Skicka den här rubriken i REST-anrop ger åtkomst till Instance Metadata Service. 
2. Varför får jag information om beräkning för den virtuella datorn inte?
   * Instance Metadata Service stöder för närvarande endast instanser som skapats med Azure Resource Manager. I framtiden, stöd för virtuella datorer molnet kan läggas till.
3. Jag har skapat Min virtuella dator via Azure Resource Manager en tid sedan. Varför kan jag inte se compute metadatainformation?
   * För virtuella datorer som skapats efter Sep 2016, lägga till en [taggen](../../azure-resource-manager/resource-group-using-tags.md) att börja Se compute metadata. För äldre virtuella datorer (som skapats före 2016 Sep), Lägg till/ta bort tillägg eller data diskar till den virtuella datorn att uppdatera metadata.
4. Jag ser inte alla data som har fyllts i för ny version
   * För virtuella datorer som skapats efter Sep 2016, lägga till en [taggen](../../azure-resource-manager/resource-group-using-tags.md) att börja Se compute metadata. För äldre virtuella datorer (som skapats före 2016 Sep), Lägg till/ta bort tillägg eller data diskar till den virtuella datorn att uppdatera metadata.
5. Varför får jag felet `500 Internal Server Error`?
   * Försök igen med din begäran utifrån exponentiell backoff-system. Kontakta Azure-supporten om problemet kvarstår.
6. Där delar ytterligare frågor/kommentarer?
   * Skicka dina kommentarer om http://feedback.azure.com.
7. Fungerar detta för skala ange instansen för virtuella datorer?
   * Ja är Metadata service tillgänglig för skala ange instanser. 
8. Hur får jag support för tjänsten?
   * Om du vill få support för tjänsten, skapa ett supportärende i Azure portal för den virtuella datorn där det inte går att hämta metadata svar efter långt återförsök 
9. Jag får timeout för min anrop för begäran i tjänsten?
   * Metadata-anrop måste göras från den primära IP-adress som tilldelats till nätverkskortet för den virtuella datorn, även om du har ändrat vägarna det måste vara en väg för 169.254.0.0/16 adress utanför nätverkskortet.
10. Jag har uppdaterat min taggarna i skalningsuppsättningen för virtuella datorer, men de visas inte i instanser till skillnad från virtuella datorer?
   * För närvarande för ScaleSets visar taggar endast att den virtuella datorn på en omstart/reimage/eller ändrar du en disk till instansen. 

   ![Stöd för instans-Metadata](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>Nästa steg

- Läs mer om [schemalagda händelser](scheduled-events.md)
