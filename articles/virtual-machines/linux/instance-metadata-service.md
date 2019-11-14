---
title: Azure-Instance Metadata Service
description: RESTful-gränssnitt för att hämta information om virtuella Linux-datorers beräknings-, nätverks-och kommande underhålls händelser.
services: virtual-machines-linux
documentationcenter: ''
author: KumariSupriya
manager: harijayms
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: d18822cf7d4827016a55fa1b1a1408a003e933ab
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035979"
---
# <a name="azure-instance-metadata-service"></a>Azure-instansens metadatatjänst

Azure Instance Metadata Service innehåller information om att köra virtuella dator instanser som kan användas för att hantera och konfigurera dina virtuella datorer.
Detta omfattar information som SKU, nätverks konfiguration och kommande underhålls händelser. Mer information om vilken typ av information som är tillgänglig finns i [API: er för metadata](#metadata-apis).

Azures Instance Metadata Service är en REST-slutpunkt som är tillgänglig för alla IaaS-VM: ar som skapats via [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).
Slut punkten är tillgänglig för en välkänd icke-flyttbar IP-adress (`169.254.169.254`) som bara kan nås från den virtuella datorn.

> [!IMPORTANT]
> Den här tjänsten är **allmänt tillgänglig** i alla Azure-regioner.  Den tar regelbundet emot uppdateringar som visar ny information om virtuella dator instanser. Den här sidan visar aktuella [API: er för metadata](#metadata-apis) som är tillgängliga.

## <a name="service-availability"></a>Tjänsttillgänglighet

Tjänsten är tillgänglig i allmänt tillgängliga Azure-regioner. Ingen API-version kan vara tillgänglig i alla Azure-regioner.

Regioner                                        | Offlinetillgänglighet?                                 | Versioner som stöds
-----------------------------------------------|-----------------------------------------------|-----------------
[Alla allmänt tillgängliga globala Azure-regioner](https://azure.microsoft.com/regions/)     | Allmänt tillgänglig | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Allmänt tillgänglig | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30
[Azure Kina](https://www.azure.cn/)                                                     | Allmänt tillgänglig | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | Allmänt tillgänglig | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30

Tabellen uppdateras när det finns tjänst uppdateringar och nya versioner som stöds är tillgängliga.

Om du vill testa Instance Metadata Service skapar du en virtuell dator från [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) eller [Azure Portal](https://portal.azure.com) i regionerna ovan och följer sedan exemplen nedan.

## <a name="usage"></a>Användning

### <a name="versioning"></a>Versionshantering

Instance Metadata Service har versions hantering och angett att API-versionen i HTTP-begäran är obligatorisk.

Du kan se de senaste versionerna som anges i den här [tillgänglighets tabellen](#service-availability).

När nya versioner läggs till kan äldre versioner fortfarande nås för kompatibilitet om skripten har beroenden för vissa data format.

Om ingen version anges returneras ett fel med en lista över de senaste versionerna som stöds.

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempel svar är ganska utskrivet för läsbarhet.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Svar**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

### <a name="using-headers"></a>Använda rubriker

När du frågar Instance Metadata Service måste du ange huvud `Metadata: true` för att se till att begäran inte oavsiktligt omdirigeras.

### <a name="retrieving-metadata"></a>Metadata hämtas

Instansens metadata är tillgängliga för att köra virtuella datorer som skapats/hanterats med [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Åtkomst till alla data kategorier för en virtuell dator instans med följande begäran:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Alla instanser av instans-metadata är Skift läges känsliga.

### <a name="data-output"></a>Data utdata

Som standard returnerar Instance Metadata Service data i JSON-format (`Content-Type: application/json`). Olika API: er returnerar dock data i olika format om det begärs.
Följande tabell är en referens till andra API: er för data format som kan ha stöd för.

API | Standard data format | Andra format
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | ingen
/attested | json | ingen

Om du vill komma åt ett svar som inte är standardformat anger du det begärda formatet som en frågesträngparametern i begäran. Exempel:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> `format=json` fungerar inte för noder på lövnivå. För dessa frågor måste `format=text` uttryckligen anges om standardformatet är JSON.

### <a name="security"></a>Säkerhet

Instance Metadata Service slut punkten kan bara nås från den virtuella dator instansen som körs på en IP-adress som inte är dirigerbart. Dessutom avvisas begäran med ett `X-Forwarded-For` huvud av tjänsten.
Begär Anden måste också innehålla ett `Metadata: true` rubrik för att säkerställa att den faktiska begäran har direkt avsikt och inte en del av oavsiktlig omdirigering.

### <a name="error"></a>Fel

Om det inte går att hitta ett data element eller en felaktig begäran, returnerar Instance Metadata Service vanliga HTTP-fel. Exempel:

HTTP-statuskod | Orsak
----------------|-------
200 OK |
400 Felaktig begäran | `Metadata: true` rubrik saknas eller så saknas formatet vid förfrågan till en lövnod
404 Hittades inte | Det begärda elementet finns inte
metoden 405 tillåts inte | Endast `GET`-och `POST`-begäranden stöds
429 för många begär Anden | API: et stöder för närvarande högst 5 frågor per sekund
500-tjänst fel     | Försök igen om en stund

### <a name="examples"></a>Exempel

> [!NOTE]
> Alla API-svar är JSON-strängar. Alla följande exempel svar är ganska utskrivna för läsbarhet.

#### <a name="retrieving-network-information"></a>Nätverks information hämtas

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Svar**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempel svar är ganska utskrivet för läsbarhet.

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

#### <a name="retrieving-public-ip-address"></a>Hämtar offentlig IP-adress

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Hämtar alla metadata för en instans

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-03-11"
```

**Svar**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempel svar är ganska utskrivet för läsbarhet.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
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

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Metadata hämtas på den virtuella Windows-datorn

**Förfrågan**

Instansens metadata kan hämtas i Windows via PowerShell-verktyget `curl`: 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-03-11 | select -ExpandProperty Content
```

Eller via `Invoke-RestMethod`-cmdlet:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2019-03-11 -Method get 
```

**Svar**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempel svar är ganska utskrivet för läsbarhet.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
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
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>API: er för metadata

#### <a name="the-following-apis-are-available-through-the-metadata-endpoint"></a>Följande API: er är tillgängliga via metadata-slutpunkten:

Data | Beskrivning | Version introducerad
-----|-------------|-----------------------
godkänd | Se [attesterade data](#attested-data) | 2018-10-01
identity | Hanterade identiteter för Azure-resurser. Se [Hämta en åtkomsttoken](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
instance | Se [instans-API](#instance-api) | 2017-04-02
scheduledevents | Se [schemalagda händelser](scheduled-events.md) | 2017-08-01

#### <a name="instance-api"></a>Instans-API
##### <a name="the-following-compute-categories-are-available-through-the-instance-api"></a>Följande beräknings kategorier är tillgängliga via instans-API: et:

> [!NOTE]
> Via slut punkten för metadata kan följande kategorier nås via instans/Compute

Data | Beskrivning | Version introducerad
-----|-------------|-----------------------
azEnvironment | Azure-miljö där den virtuella datorn körs i | 2018-10-01
customData | Se [anpassade data](#custom-data) | 2019-02-01
location | Azure-regionen som den virtuella datorn körs i | 2017-04-02
namn | Namn på den virtuella datorn | 2017-04-02
offer | Erbjudande information för den virtuella dator avbildningen och finns bara för avbildningar som distribuerats från Azures avbildnings Galleri | 2017-04-02
osType | Linux eller Windows | 2017-04-02
placementGroupId | [Placerings grupp](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) för den virtuella datorns skalnings uppsättning | 2017-08-01
projektplan | [Planera](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) som innehåller namn, produkt och utgivare för en virtuell dator om den är en Azure Marketplace-avbildning | 2018-04-02
platformUpdateDomain |  [Uppdatera den domän](manage-availability.md) som den virtuella datorn körs i | 2017-04-02
platformFaultDomain | [Feldomän](manage-availability.md) som den virtuella datorn körs i | 2017-04-02
CSP | Provider för den virtuella datorn | 2018-10-01
publicKeys | [Samling offentliga nycklar](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) som har tilldelats den virtuella datorn och sökvägar | 2018-04-02
publisher | Utgivare av VM-avbildningen | 2017-04-02
resourceGroupName | [Resurs grupp](../../azure-resource-manager/resource-group-overview.md) för den virtuella datorn | 2017-08-01
resourceId | Resursens [fullständigt kvalificerade](https://docs.microsoft.com/rest/api/resources/resources/getbyid) ID | 2019-03-11
sku | En speciell SKU för VM-avbildningen | 2017-04-02
subscriptionId | Azure-prenumeration för den virtuella datorn | 2017-08-01
taggar | [Taggar](../../azure-resource-manager/resource-group-using-tags.md) för den virtuella datorn  | 2017-08-01
tagsList | Taggar formaterade som en JSON-matris för enklare programmerings parsning  | 2019-06-04
version | Version av VM-avbildningen | 2017-04-02
vmId | [Unikt ID](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) för den virtuella datorn | 2017-04-02
vmScaleSetName | [ScaleSet namn](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) på virtuell dator för den virtuella datorns skal uppsättning | 2017-12-01
vmSize | [Storlek på virtuell dator](sizes.md) | 2017-04-02
zon | [Tillgänglighets zon](../../availability-zones/az-overview.md) för den virtuella datorn | 2017-12-01

##### <a name="the-following-network-categories-are-available-through-the-instance-api"></a>Följande nätverks kategorier är tillgängliga via instans-API: et:

> [!NOTE]
> Via metadata-slutpunkten nås följande kategorier via instans/nätverk/gränssnitt

Data | Beskrivning | Version introducerad
-----|-------------|-----------------------
ipv4/privateIpAddress | Lokal IPv4-adress för den virtuella datorn | 2017-04-02
ipv4/publicIpAddress | Offentlig IPv4-adress för den virtuella datorn | 2017-04-02
undernät/adress | Den virtuella datorns under näts adress | 2017-04-02
undernät/prefix | Undernätsprefixet, exempel 24 | 2017-04-02
ipv6/ipAddress | Lokal IPv6-adress för den virtuella datorn | 2017-04-02
macAddress | Mac-adress för virtuell dator | 2017-04-02

## <a name="attested-data"></a>Bestyrkade data

Instansens metadata svarar vid http-slutpunkten på 169.254.169.254. En del av scenariot som hanteras av Instance Metadata Service är att ge garantier för att de data som besvaras kommer från Azure. Vi loggar en del av den här informationen så att Marketplace-avbildningar kan vara säker på att de är en image som körs på Azure.

### <a name="example-attested-data"></a>Exempel på bestyrkade data

> [!NOTE]
> Alla API-svar är JSON-strängar. Följande exempel svar är ganska utskrivna för läsbarhet.

 **Förfrågan**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

API-version är ett obligatoriskt fält. Se [avsnittet tjänst tillgänglighet](#service-availability) för API-versioner som stöds.
Nonce är en valfri 10-siffrig sträng angiven. Nonce kan användas för att spåra begäran och om det inte anges, i svars kodad sträng returneras den aktuella UTC-tidsstämpeln.

 **Svar**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempel svar är ganska utskrivet för läsbarhet.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> Signatur-bloben är en [PKCS7](https://aka.ms/pkcs7) -signerad version av dokumentet. Det innehåller det certifikat som används för att signera tillsammans med den virtuella dator informationen som vmId, nonce, subscriptionId, timeStamp för att skapa och upphör ande av dokumentet och plan informationen om avbildningen. Plan informationen är bara ifylld för Azures avbildningar på marknaden. Certifikatet kan extraheras från svaret och används för att verifiera att svaret är giltigt och kommer från Azure.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Hämtar bestyrkade metadata i virtuell Windows-dator

 **Förfrågan**

Instansens metadata kan hämtas i Windows via PowerShell-verktyget `curl`:

 ```bash
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Eller via `Invoke-RestMethod`-cmdlet:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

API-version är ett obligatoriskt fält. Se avsnittet tjänst tillgänglighet för API-versioner som stöds.
Nonce är en valfri 10-siffrig sträng angiven. Nonce kan användas för att spåra begäran och om det inte anges, i svars kodad sträng returneras den aktuella UTC-tidsstämpeln.

 **Svar**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempel svar är ganska utskrivet för läsbarhet.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> Signatur-bloben är en [PKCS7](https://aka.ms/pkcs7) -signerad version av dokumentet. Det innehåller det certifikat som används för att signera tillsammans med den virtuella dator informationen som vmId, nonce, subscriptionId, timeStamp för att skapa och upphör ande av dokumentet och plan informationen om avbildningen. Plan informationen är bara ifylld för Azures avbildningar på marknaden. Certifikatet kan extraheras från svaret och används för att verifiera att svaret är giltigt och kommer från Azure.


## <a name="example-scenarios-for-usage"></a>Exempel scenarier för användning  

### <a name="tracking-vm-running-on-azure"></a>Spåra virtuell dator som körs i Azure

Som tjänst leverantör kan du behöva spåra antalet virtuella datorer som kör program varan eller ha agenter som behöver spåra unika virtuella datorer. Om du vill kunna hämta ett unikt ID för en virtuell dator använder du fältet `vmId` från Instance Metadata Service.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Svar**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Placering av containrar, datapartitionsbaserad feldomän/uppdateringsdomän

För vissa scenarier är placeringen av olika data repliker av primär betydelse. Till exempel, [HDFS-replik placering](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) eller container placering via en [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) kan du behöva känna till `platformFaultDomain` och `platformUpdateDomain` den virtuella datorn körs på.
Du kan också använda [Tillgänglighetszoner](../../availability-zones/az-overview.md) för instanserna för att fatta beslut.
Du kan fråga dessa data direkt via Instance Metadata Service.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Svar**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Hämta mer information om den virtuella datorn under supportbegäran

Som tjänst leverantör kan du få ett support samtal där du vill ha mer information om den virtuella datorn. Att be kunden att dela dina beräknings-metadata kan ge grundläggande information om support teknikern för att veta om vilken typ av virtuell dator som finns i Azure.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Svar**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempel svar är ganska utskrivet för läsbarhet.

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

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Hämta Azure-miljön där den virtuella datorn körs

Azure har olika suveräna moln som [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Ibland behöver du Azure-miljön för att kunna göra vissa körnings beslut. I följande exempel visas hur du kan få det här beteendet.

**Förfrågan**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Svar**
```bash
AzurePublicCloud
```
Molnet och värdena i Azure-miljön visas nedan.

 Molnet   | Azure Environment
---------|-----------------
[Alla allmänt tillgängliga globala Azure-regioner](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure Kina](https://azure.microsoft.com/global-infrastructure/china/)                  | AzureChinaCloud
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>Hämtar taggar för den virtuella datorn

Taggar kan ha tillämpats på den virtuella Azure-datorn för att logiskt organisera dem i en taxonomi. Taggarna som tilldelas till en virtuell dator kan hämtas med hjälp av begäran nedan.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Svar**

```text
Department:IT;Environment:Test;Role:WebRole
```

Fältet `tags` är en sträng med taggarna avgränsade med semikolon. Detta kan vara ett problem om semikolon används i själva taggarna. Om en parser skrivs för att program mässigt extrahera taggarna bör du förlita dig på fältet `tagsList` som är en JSON-matris utan avgränsare och därmed lättare att parsa.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=text"
```

**Svar**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

### <a name="validating-that-the-vm-is-running-in-azure"></a>Verifiera att den virtuella datorn körs i Azure

Marketplace-leverantörer vill se till att deras program vara licensieras att köras endast i Azure. Om någon kopierar den virtuella hård disken till en lokal plats, ska de ha möjlighet att identifiera den. Genom att ringa till Instance Metadata Service kan Marketplace-leverantörer Hämta signerade data som endast garanterar svar från Azure.

> [!NOTE]
> Kräver att JQ installeras.

**Förfrågan**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Svar**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"
}
```

Data | Beskrivning
-----|------------
Nnär | Användaren angav valfri sträng med begäran. Om inget nonce angavs i begäran returneras den aktuella UTC-tidsstämpeln
projektplan | [Planera](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) för en virtuell dator i den är en Azure Marketplace-avbildning som innehåller namn, produkt och utgivare
timestamp/createdOn | Tidsstämpeln då det första signerade dokumentet skapades
timestamp/expiresOn | Tidsstämpeln då det signerade dokumentet upphör att gälla
vmId |  [Unikt ID](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) för den virtuella datorn
subscriptionId | Azure-prenumeration för den virtuella datorn som introducerades i `2019-04-30`

#### <a name="verifying-the-signature"></a>Verifiera signaturen

När du har hämtat signaturen ovan kan du kontrol lera att signaturen är från Microsoft. Du kan också kontrol lera mellanliggande certifikat och certifikat kedjan. Slutligen kan du kontrol lera att prenumerations-ID: t är korrekt.

> [!NOTE]
> Certifikatet för det offentliga molnet och det suveräna molnet är annorlunda.

 Molnet | Certifikat
---------|-----------------
[Alla allmänt tillgängliga globala Azure-regioner](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure Kina](https://azure.microsoft.com/global-infrastructure/china/)                  | metadata.azure.cn
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | metadata.microsoftazure.de

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

I de fall där det mellanliggande certifikatet inte kan laddas ned på grund av nätverks begränsningar under verifieringen, kan det mellanliggande certifikatet fästas. Azure kommer dock att återställas över certifikaten enligt PKI-praxis enligt standard. De fästa certifikaten måste uppdateras när detta sker. När en ändring av uppdateringen av mellanliggande certifikat planeras uppdateras Azure-bloggen och Azure-kunder meddelas. Du hittar de mellanliggande certifikaten [här](https://www.microsoft.com/pki/mscorp/cps/default.htm). De mellanliggande certifikaten för varje region kan vara olika.

### <a name="failover-clustering-in-windows-server"></a>Kluster för växling vid fel i Windows Server

Vid frågor om Instance Metadata Service med kluster för växling vid fel i vissa scenarier är det nödvändigt att lägga till en väg i routningstabellen.

1. Öppna kommando tolken med administratörs behörighet.

2. Kör följande kommando och anteckna adressen till gränssnittet för nätverks målet (`0.0.0.0`) i IPv4-väg tabellen.

```bat
route print
```

> [!NOTE] 
> Följande exempel på utdata från en virtuell Windows Server-dator med redundanskluster aktiverat innehåller bara IPv4-routningstabellen för enkelhetens skull.

```bat
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

1. Kör följande kommando och Använd adressen till gränssnittet för nätverks målet (`0.0.0.0`) som är (`10.0.1.10`) i det här exemplet.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="custom-data"></a>Anpassade data
Instance Metadata Service ger den virtuella datorn möjlighet att få åtkomst till dess anpassade data. Binära data måste vara mindre än 64 KB och anges för den virtuella datorn i Base64-kodat format.

Azures anpassade data kan infogas i den virtuella datorn via REST-API: er, PowerShell-cmdletar, Azure Command Line Interface (CLI) eller en ARM-mall.

Ett exempel på ett Azure Command line-gränssnitt finns i [anpassade data och Cloud-Init på Microsoft Azure](https://azure.microsoft.com/blog/custom-data-and-cloud-init-on-windows-azure/).

Ett exempel på en ARM-mall finns i [distribuera en virtuell dator med CustomData](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

Anpassade data är tillgängliga för alla processer som körs på den virtuella datorn. Vi rekommenderar att kunder inte infogar hemlig information i anpassade data.

För närvarande garanterar vi att anpassade data är tillgängliga vid start av en virtuell dator. Om uppdateringar görs till den virtuella datorn, till exempel att lägga till diskar eller ändra storlek på den virtuella datorn, kommer Instance Metadata Service inte att tillhandahålla anpassade data. Att tillhandahålla anpassade data permanent via Instance Metadata Service pågår just nu.

#### <a name="retrieving-custom-data-in-virtual-machine"></a>Hämtar anpassade data på den virtuella datorn
Instance Metadata Service tillhandahåller anpassade data till den virtuella datorn i Base64-kodat format. I följande exempel avkodas den base64-kodade strängen.

> [!NOTE]
> Anpassade data i det här exemplet tolkas som en ASCII-sträng som läser "mina anpassade data".

**Förfrågan**

```bash
curl -H "Metadata:true" "http://169.254.169.254/metadata/instance/compute/customData?api-version=2019-02-01&&format=text" | base64 --decode
```

**Svar**

```text
My custom data.
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Exempel på hur du anropar metadata service med olika språk i den virtuella datorn

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

1. Jag får fel meddelandet `400 Bad Request, Required metadata header not specified`. Vad betyder detta?
   * Instance Metadata Service kräver att huvud `Metadata: true` skickas i begäran. Om du skickar den här rubriken i REST-anropet får du till gång till Instance Metadata Service.
2. Varför får jag inte beräknings information för min virtuella dator?
   * För närvarande stöder Instance Metadata Service endast instanser som skapats med Azure Resource Manager. I framtiden kan stöd för virtuella datorer i moln tjänsten läggas till.
3. Jag har skapat min virtuella dator genom att Azure Resource Manager en och tillbaka. Varför visas inte information om att beräkna metadata?
   * För virtuella datorer som skapats efter sep 2016 lägger du till en [tagg](../../azure-resource-manager/resource-group-using-tags.md) för att börja se Compute metadata. För äldre virtuella datorer (som skapats före sep 2016) lägger du till/tar bort tillägg eller data diskar till den virtuella datorn för att uppdatera metadata.
4. Jag ser inte alla data som är ifyllda för nya versioner
   * För virtuella datorer som skapats efter sep 2016 lägger du till en [tagg](../../azure-resource-manager/resource-group-using-tags.md) för att börja se Compute metadata. För äldre virtuella datorer (som skapats före sep 2016) lägger du till/tar bort tillägg eller data diskar till den virtuella datorn för att uppdatera metadata.
5. Varför får jag fel meddelandet `500 Internal Server Error`?
   * Gör om din begäran baserat på exponentiellt system. Kontakta Azure-supporten om problemet kvarstår.
6. Var kan jag dela fler frågor/kommentarer?
   * Skicka kommentarer till https://feedback.azure.com.
7. Skulle detta fungera för den virtuella datorns skalnings uppsättnings instans?
   * Tjänsten Yes metadata är tillgänglig för instanser av skalnings uppsättningar.
8. Hur gör jag för att få support för tjänsten?
   * Om du vill få support för tjänsten skapar du ett support ärende i Azure Portal för den virtuella datorn där du inte kan få svar på metadata efter långa återförsök.
9. Jag får en timeout för mitt samtal till tjänsten?
   * Anrop till metadata måste göras från den primära IP-adress som tilldelats till nätverkskortet på den virtuella datorn, och om du har ändrat dina vägar måste det finnas en väg för 169.254.0.0/16-adressen från nätverkskortet.
10. Jag uppdaterade mina taggar i den virtuella datorns skalnings uppsättning men de visas inte i de här instanserna till skillnad från virtuella datorer?
    * För närvarande för ScaleSets-taggar visas bara för den virtuella datorn vid en omstart/återavbildning/eller en disk ändring till instansen.

    ![Stöd för instansen metadata](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [schemalagda händelser](scheduled-events.md)
