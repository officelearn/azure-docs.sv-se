---
title: "Azure-instans Metadata Service för Windows | Microsoft Docs"
description: "RESTful-gränssnitt att hämta information om Windows VM beräkning, nätverk och kommande underhållshändelser."
services: virtual-machines-windows
documentationcenter: 
author: harijay
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/11/2017
ms.author: harijay
ms.openlocfilehash: 55b97b89cb297dc08dc73f6714c5159d4565a97c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="azure-instance-metadata-service-for-windows-vms"></a>Azure-instans Metadata-tjänsten för virtuella Windows-datorer


Tjänsten Azure instans Metadata innehåller information om virtuella instanser som kan användas för att hantera och konfigurera dina virtuella datorer som körs.
Detta omfattar information som SKU, nätverkskonfigurationen och kommande underhållshändelser. Mer information om vilken typ av information är tillgänglig finns [metadatakategorier](#instance-metadata-data-categories).

Azures instans Metadata Service är en REST-slutpunkt som är tillgängliga för alla IaaS-VM som skapats via den [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Slutpunkten är tillgänglig på en välkänd icke-dirigerbara IP-adress (`169.254.169.254`) som kan nås från den virtuella datorn.



> [!IMPORTANT]
> Den här tjänsten är **allmänt tillgänglig** i globala Azure-regioner. Det är i Public preview för myndigheter, Kina och tyska Azure-molnet. Den tar emot uppdateringar för att exponera ny information om virtuell datorinstans regelbundet. Den här sidan visar den uppdaterade [datakategorier](#instance-metadata-data-categories) tillgängliga.



## <a name="service-availability"></a>Tjänsttillgänglighet för
Tjänsten är tillgänglig i alla allmänt tillgänglig Global Azure-regioner. Tjänsten är tillgänglig som förhandsversion i Government, Kina eller Tyskland regioner.

Regioner                                        | Tillgänglighet?
-----------------------------------------------|-----------------------------------------------
[Alla allmänt tillgänglig Global Azure-regioner](https://azure.microsoft.com/regions/)     | Allmänt tillgänglig 
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | I förhandsversionen 
[Azure Kina](https://www.azure.cn/)                                                           | I förhandsversionen
[Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/)                    | I förhandsversionen

Den här tabellen uppdateras när tjänsten blir tillgänglig i andra Azure-moln.

Om du vill testa tjänsten instans Metadata, skapa en virtuell dator från [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) eller [Azure-portalen](http://portal.azure.com) i ovanstående områden och följ exemplen nedan.

## <a name="usage"></a>Användning

### <a name="versioning"></a>Versionshantering
Tjänsten instans Metadata är en ny version. Versioner är obligatoriska och den aktuella versionen är `2017-04-02`.

> [!NOTE] 
> Tidigare förhandsvisningarna av schemalagda händelser stöds {senaste} som den api-versionen. Det här formatet stöds inte längre och kommer att inaktualiseras i framtiden.

När vi lägger till nya versioner kan äldre versioner fortfarande användas för kompatibilitet om skripten har beroenden på specifika dataformat. Observera dock att den aktuella preview version(2017-03-01) inte kanske tillgänglig när tjänsten är allmänt tillgänglig.

### <a name="using-headers"></a>Med hjälp av rubriker
När du frågar instans Metadata tjänsten måste du ange rubriken `Metadata: true` så begäran inte omdirigerades oavsiktligt.

### <a name="retrieving-metadata"></a>Hämta metadata

Metadata för instansen är tillgängliga för att köra virtuella datorer skapas/hanteras med hjälp av [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Komma åt alla datakategorier för en virtuell dator-instans med följande begäran:

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

> [!NOTE] 
> Alla metadatafrågor för instansen är skiftlägeskänsliga.

### <a name="data-output"></a>Utdata
Som standard tjänsten instans Metadata returnerar data i JSON-format (`Content-Type: application/json`). Dock kan olika API: er returnera data i olika format om begärt.
I följande tabell är en referens för andra dataformat stöder API: er.

API | Standardformatet för Data | Andra format
--------|---------------------|--------------
/Instance | JSON | Text
/scheduledevents | JSON | Ingen

Ange det begärda formatet som en querystring-parameter i begäran för att komma åt en icke-förvalt svarsformat. Exempel:

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02&format=text"
```

### <a name="security"></a>Säkerhet
Instansen Metadata tjänstslutpunkten är enbart tillgänglig från den virtuella datorn-instansen som körs på en icke-dirigerbara IP-adress. Dessutom begäran med en `X-Forwarded-For` huvud avvisas av tjänsten.
Vi behöver också begäranden som innehåller en `Metadata: true` sidhuvud så att den faktiska begäranden var direkt avsedda och inte en del av oavsiktlig omdirigering. 

### <a name="error"></a>Fel
Om det finns en dataelement som inte hittades eller en felaktig begäran, returnerar tjänsten instans Metadata standard HTTP-fel. Exempel:

HTTP-statuskod | Orsak
----------------|-------
200 OK |
400 Felaktig förfrågan | Saknas `Metadata: true` sidhuvud
404 Hittades inte | Begärt element does't finns 
405 Metoden tillåts inte | Endast `GET` och `POST` stöds
429 för många begäranden | API: et stöder för närvarande högst 5 frågor per sekund
500 tjänstfel     | Försök igen efter en stund

### <a name="examples"></a>Exempel

> [!NOTE] 
> Alla API-svar är JSON-strängar. Alla följande exempel svar är pretty ut för läsbarhet.

#### <a name="retrieving-network-information"></a>Hämtar nätverksinformation

**Förfrågan**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-04-02"
```

**Svar**

> [!NOTE] 
> Svaret är en JSON-sträng. Följande exempel svaret är pretty ut för läsbarhet.

```
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

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-04-02&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Hämta alla metadata för en instans

**Förfrågan**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

**Svar**

> [!NOTE] 
> Svaret är en JSON-sträng. Följande exempel svaret är pretty ut för läsbarhet.

```
{
  "compute": {
    "location": "westcentralus",
    "name": "IMDSSample",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "Canonical",
    "sku": "16.04.0-LTS",
    "version": "16.04.201610200",
    "vmId": "5d33a910-a7a0-4443-9f01-6a807801b29b",
    "vmSize": "Standard_A1"
  },
  "network": {
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
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Hämtning av metadata i Windows-dator

**Förfrågan**

Instansen metadata kan hämtas i Windows via PowerShell-verktyget `curl`: 

```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-04-02 | select -ExpandProperty Content
```

Eller via den `Invoke-RestMethod` cmdlet:
    
```
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-04-02 -Method get 
```

**Svar**

> [!NOTE] 
> Svaret är en JSON-sträng. Följande exempel svaret är pretty ut för läsbarhet.

```
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

## <a name="instance-metadata-data-categories"></a>Instansen metadata datakategorier
Följande datakategorier är tillgängliga via tjänsten instans Metadata:

Data | Beskrivning
-----|------------
location | Azure-regionen för den virtuella datorn körs i
namn | Namnet på den virtuella datorn 
Erbjudande | Tillhandahåller information för VM-avbildning. Det här värdet finns bara för avbildningar som distribueras från Azure-avbildning gallery.
Publisher | Utgivaren av VM-avbildning
SKU | Specifika SKU för VM-avbildning  
Version | Version av VM-avbildning 
osType | Linux- eller Windows 
platformUpdateDomain |  [Uppdateringsdomän](manage-availability.md) den virtuella datorn körs
platformFaultDomain | [Feldomänen](manage-availability.md) den virtuella datorn körs
vmId | [Unik identifierare](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) för den virtuella datorn
vmSize | [VM-storlek](sizes.md)
IPv4/privateIpAddress | Lokala IPv4-adressen för den virtuella datorn 
IPv4/publicIpAddress | Offentliga IPv4-adressen för den virtuella datorn
undernätet och/eller adress | Undernätsadress av den virtuella datorn
undernätsprefixet / | Undernätets prefix, exempel 24
IPv6/IP-adress | Den lokala IPv6-adressen för den virtuella datorn
MAC-adress | VM mac-adress 
scheduledevents | För närvarande finns i Public Preview finns [scheduledevents](scheduled-events.md)

## <a name="example-scenarios-for-usage"></a>Exempelscenarier för användning  

### <a name="tracking-vm-running-on-azure"></a>Spårning av virtuell dator som kör på Azure

Du kan behöva hålla reda på antalet virtuella datorer som kör programvaran eller att agenter som behöver spåra unikhet för den virtuella datorn som en tjänsteleverantör. Om du vill kunna hämta ett unikt ID för en virtuell dator använder den `vmId` från Metadata-tjänsten för instansen.

**Förfrågan**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-04-02&format=text"
```

**Svar**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Placering av behållare, partitioner data baserat fel/uppdatera domän 

För vissa scenarier, placering av olika repliker är av yttersta vikt. Till exempel [HDFS replik placering](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) eller placering av behållare via en [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) kanske du behöver känna till den `platformFaultDomain` och `platformUpdateDomain` den virtuella datorn körs på.
Du kan fråga data direkt via tjänsten instans Metadata.

**Förfrågan**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-04-02&format=text" 
```

**Svar**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Mer information om den virtuella datorn under supportärende 

Som en leverantör får du ett supportsamtal där du vill veta mer om den virtuella datorn. Be kunden att dela beräknings-metadata kan ge grundläggande information för supportpersonal känna till om vilken typ av virtuell dator på Azure. 

**Förfrågan**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-04-02"
```

**Svar**

> [!NOTE] 
> Svaret är en JSON-sträng. Följande exempel svaret är pretty ut för läsbarhet.

```
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

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Exempel på hur metadatatjänsten som använder olika språk inuti den virtuella datorn 

Språk | Exempel 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/BLOB/Master/IMDSSample.RB
Gå Lan   | https://github.com/Microsoft/azureimds/BLOB/Master/imdssample.go            
python   | https://github.com/Microsoft/azureimds/BLOB/Master/IMDSSample.PY
C++      | https://github.com/Microsoft/azureimds/BLOB/Master/IMDSSample-Windows.cpp
C#       | https://github.com/Microsoft/azureimds/BLOB/Master/IMDSSample.CS
Javascript | https://github.com/Microsoft/azureimds/BLOB/Master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/BLOB/Master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/BLOB/Master/IMDSSample.SH
    

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
1. Jag får felet `400 Bad Request, Required metadata header not specified`. Vad innebär det?
   * Instansen Metadata tjänsten kräver huvudet `Metadata: true` som används i begäran. Skicka det här sidhuvudet i REST-anrop tillåter åtkomst till tjänsten instans Metadata. 
2. Varför inte inträffar beräknings-information för den virtuella datorn?
   * Tjänsten instans Metadata stöder för närvarande bara instanser som skapats med Azure Resource Manager. I framtiden, kan vi lägga till stöd för virtuella datorer för molnet tjänsten.
3. Jag har skapat Min virtuella dator via Azure Resource Manager en tid sedan. Varför kan jag inte se compute metadatainformation?
   * För alla virtuella datorer som skapats efter Sep 2016, lägga till en [taggen](../../azure-resource-manager/resource-group-using-tags.md) att starta ser compute metadata. För äldre virtuella datorer (som skapats före Sep 2016), Lägg till/ta bort tillägg eller data diskar till den virtuella datorn för att uppdatera metadata.
4. Varför får felet `500 Internal Server Error`?
   * Försök att utföra din begäran utifrån exponentiell tillbaka av systemet. Kontakta Azure-supporten om problemet kvarstår.
5. Där delar ytterligare frågor/kommentarer?
   * Skicka kommentarer om http://feedback.azure.com.
7. Fungerar detta för Virtual Machine Scale ange instansen?
   * Ja är Metadata-tjänsten tillgänglig för skala ange instanser. 
6. Hur får jag support för tjänsten?
   * Om du vill få support för tjänsten, skapa ett supportproblem i Azure-portalen för den virtuella datorn där du inte kan hämta metadata svar efter lång försök 

   ![Stöd för instans-Metadata](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den [schemalagda händelser](scheduled-events.md) API **som förhandsversion** som tillhandahålls av tjänsten för instansen Metadata.
