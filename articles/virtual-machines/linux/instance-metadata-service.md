---
title: Azure Instance Metadata Service | Microsoft Docs
description: RESTful-gränssnitt att få information om Linux VM-beräkning, nätverk och kommande underhåll.
services: virtual-machines-linux
documentationcenter: ''
author: KumariSupriya
manager: harijayms
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/28/2019
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: c3e2102b5794fb3770b1c77e241320fa7d2222c7
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58850782"
---
# <a name="azure-instance-metadata-service"></a>Azure Instance Metadata service

Azure Instance Metadata Service innehåller information om hur du kör instanser av virtuella datorer som kan användas för att hantera och konfigurera dina virtuella datorer.
Detta omfattar information som SKU: N, nätverkskonfiguration och kommande underhåll. Mer information om vilken typ av information är tillgänglig finns [metadatakategorier](#instance-metadata-data-categories).

Azures Instance Metadata Service är en REST-slutpunkt som är tillgängliga för virtuella IaaS-datorer skapas via den [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).
Slutpunkten är tillgänglig på en välkänd icke-dirigerbara IP-adress (`169.254.169.254`) som kan nås från den virtuella datorn.

> [!IMPORTANT]
> Den här tjänsten är **allmänt tillgänglig** i Azure-regioner.  Den får regelbundet uppdateringar om du vill exponera ny information om instanser av virtuella datorer. Den här sidan visar det uppdaterade [datakategorier](#instance-metadata-data-categories) tillgängliga.

## <a name="service-availability"></a>Tjänsttillgänglighet

Tjänsten är tillgänglig i allmänt tillgängliga Azure-regioner. Inte alla API-versionen kan finnas i alla Azure-regioner.

Regioner                                        | Tillgänglighet?                                 | Versioner som stöds
-----------------------------------------------|-----------------------------------------------|-----------------
[Alla allmänt tillgängliga Azure-regioner globalt](https://azure.microsoft.com/regions/)     | Allmänt tillgänglig   | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Allmänt tillgänglig | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure China](https://www.azure.cn/)                                                           | Allmänt tillgänglig | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | Allmänt tillgänglig | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01

Den här tabellen uppdateras när det finns uppdateringar av tjänsten och eller nya versioner som stöds är tillgängliga.

Om du vill prova Instance Metadata Service, skapa en virtuell dator från [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) eller [Azure-portalen](https://portal.azure.com) i ovanstående regioner och följer exemplen nedan.

## <a name="usage"></a>Användning

### <a name="versioning"></a>Versionshantering

Instance Metadata Service är en ny version. Versioner är obligatoriska och den aktuella versionen på Global Azure är `2018-10-01`. Aktuella versioner som stöds är (2017-04-02, 2018-02-01, 2018-04-02, 2018-10-01 till 2017-12-01 till 2017-08-01).

När nya versioner läggs kan äldre versioner fortfarande användas för kompatibilitet om skripten har beroenden på specifika dataformat.

När ingen version anges, returneras ett fel med en lista över de senaste versionerna som stöds.

> [!NOTE] 
> Svaret är en JSON-sträng. Följande exempelsvar är pretty ut för läsbarhet.

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
/ godkänd av | JSON | inga

Ange det begärda formatet som en frågesträngsparameter i begäran för att komma åt en icke-standard svarsformat. Exempel:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> För lövnoder den `format=json` fungerar inte. För de här frågorna `format=text` måste anges uttryckligen om standardformatet är json.

### <a name="security"></a>Säkerhet

Instance Metadata Service-slutpunkten är enbart tillgänglig från den VM-instansen som som körs på en icke-dirigerbara IP-adress. Dessutom kan ett begärande med ett `X-Forwarded-For` rubrik avvisas av tjänsten.
Begäranden måste också innehålla en `Metadata: true` rubrik så att den faktiska begäran direkt var avsett och inte en del av oavsiktlig omdirigering.

### <a name="error"></a>Fel

Om det finns ett dataelement hittades inte eller en felaktig begäran, returnerar Instance Metadata Service standard HTTP-fel. Exempel:

HTTP-statuskod | Orsak
----------------|-------
200 OK |
400 Felaktig förfrågan | Saknas `Metadata: true` rubrik eller saknar formatet när du frågar efter en lövnod
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
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"
```

**Svar**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempelsvar är pretty ut för läsbarhet.

```json
{
  "compute": {
    "azEnvironment": "AZUREPUBLICCLOUD",
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

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Hämta metadata på Windows-datorer

**Förfrågan**

Instans metadata kan hämtas i Windows via PowerShell-verktyget `curl`: 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2018-10-01 | select -ExpandProperty Content
```

Eller via den `Invoke-RestMethod` cmdlet:

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2018-10-01 -Method get 
```

**Svar**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempelsvar är pretty ut för läsbarhet.

```json
{
  "compute": {
    "azEnvironment": "AZUREPUBLICCLOUD",
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

## <a name="instance-metadata-data-categories"></a>Instans datakategorier för metadata

Följande datakategorier är tillgängliga via Instance Metadata Service:

Data | Beskrivning | Version introducerades
-----|-------------|-----------------------
azEnvironment | Azure-miljön där Virtuellt datorn körs i | 2018-10-01
location | Azure-Region den virtuella datorn körs i | 2017-04-02
namn | Namnet på den virtuella datorn | 2017-04-02
erbjudande | Ger information om VM-avbildning. Det här värdet är endast tillgänglig för avbildningar som distribueras från Azures avbildningsgalleri. | 2017-04-02
utgivare | Utgivaren av den Virtuella datoravbildningen | 2017-04-02
sku | Specifika SKU för VM-avbildning | 2017-04-02
version | Versionen av VM-avbildning | 2017-04-02
osType | Linux eller Windows | 2017-04-02
platformUpdateDomain |  [Uppdateringsdomän](manage-availability.md) Virtuellt datorn körs | 2017-04-02
platformFaultDomain | [Feldomän](manage-availability.md) Virtuellt datorn körs | 2017-04-02
vmId | [Unik identifierare](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) för den virtuella datorn | 2017-04-02
vmSize | [Storlek på virtuell dator](sizes.md) | 2017-04-02
subscriptionId | Azure-prenumeration för den virtuella datorn | 2017-08-01
tags | [Taggar](../../azure-resource-manager/resource-group-using-tags.md) för den virtuella datorn  | 2017-08-01
resourceGroupName | [Resursgrupp](../../azure-resource-manager/resource-group-overview.md) för den virtuella datorn | 2017-08-01
placementGroupId | [Placeringsgrupp](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) för VM-skalningsuppsättning | 2017-08-01
plan | [Planera](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) för en virtuell dator i dess en Azure Marketplace-avbildning som innehåller namn, produkt och utgivare | 2018-04-02
provider | Leverantör av den virtuella datorn | 2018-10-01
publicKeys | [Samling med offentliga nycklar](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) tilldelats den virtuella datorn och sökvägar | 2018-04-02
vmScaleSetName | [Namn på virtuell dator ScaleSet](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) för VM-skalningsuppsättning | 2017-12-01
zon | [Tillgänglighetszon](../../availability-zones/az-overview.md) för den virtuella datorn | 2017-12-01
ipv4/privateIpAddress | Lokala IPv4-adressen för den virtuella datorn | 2017-04-02
ipv4/publicIpAddress | Offentliga IPv4-adressen för den virtuella datorn | 2017-04-02
subnet/address | Undernätadress för den virtuella datorn | 2017-04-02
undernätsprefix / | Undernätets prefix, exempel 24 | 2017-04-02
ipv6/ipAddress | Den lokala IPv6-adressen för den virtuella datorn | 2017-04-02
macAddress | Mac-adress för virtuell dator | 2017-04-02
scheduledevents | Se [schemalagda händelser](scheduled-events.md) | 2017-08-01
identity | Hanterade identiteter för Azure-resurser. Se [hämta en åtkomsttoken](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
godkänd av | Se [godkänd av Data](#attested-data) | 2018-10-01

## <a name="attested-data"></a>Godkänd av Data

Instance Metadata svarar på http-slutpunkt på 169.254.169.254. En del av scenariot hanteras av Instance Metadata Service är att ge garantier att data har svarat kommer från Azure. Vi loggar en del av den här informationen så marketplace-avbildningar kan vara säker på att det är sin avbildning som körs på Azure.

### <a name="example-attested-data"></a>Exempel godkänd av Data

 > [!NOTE]
> Alla API-svar är JSON-strängar. Följande exempelsvar är pretty ut för läsbarhet.

 **Förfrågan**


 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

API-versionen är ett obligatoriskt fält och vilken version som stöds för godkänd av data är 2018-10-01.
Nonce är en valfri 10 siffror sträng som tillhandahålls. Nonce kan användas för att spåra begäran och om du inte tidsstämpel returneras i-kodad svarssträng aktuella UTC.

 **Svar**
> [!NOTE]
> Svaret är en JSON-sträng. Följande exempelsvar är pretty ut för läsbarhet.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

 > Signaturen bloben är en [pkcs7](https://aka.ms/pkcs7) signerade versionen av dokumentet. Den innehåller det certifikat som används för att signera tillsammans med VM-information som vmId, nonce, tidsstämpel för skapande och dokumentet och planera information om avbildningen har upphört att gälla. Planinformationen fylls bara för Azure-marknaden plats avbildningar. Certifikatet kan extraheras från svaret och används för att verifiera att svaret är giltig och kommer från Azure.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Hämta godkänd av metadata på Windows-datorer

 **Förfrågan**

Instans metadata kan hämtas i Windows via PowerShell-verktyget `curl`:

 ```bash
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Eller via den `Invoke-RestMethod` cmdlet:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

API-versionen är ett obligatoriskt fält och vilken version som stöds för godkänd av data är 2018-10-01.
Nonce är en valfri 10 siffror sträng som tillhandahålls. Nonce kan användas för att spåra begäran och om du inte tidsstämpel returneras i-kodad svarssträng aktuella UTC.

 **Svar**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempelsvar är pretty ut för läsbarhet.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> Signaturen bloben är en [pkcs7](https://aka.ms/pkcs7) signerade versionen av dokumentet. Den innehåller det certifikat som används för att signera tillsammans med VM-information som vmId, nonce, tidsstämpel för skapande och dokumentet och planera information om avbildningen har upphört att gälla. Planinformationen fylls bara för Azure-marknaden plats avbildningar. Certifikatet kan extraheras från svaret och används för att verifiera att svaret är giltig och kommer från Azure.

## <a name="example-scenarios-for-usage"></a>Exempelscenarier för användning  

### <a name="tracking-vm-running-on-azure"></a>Spåra virtuell dator som körs i Azure

Du kan behöva att spåra hur många virtuella datorer som kör ditt program eller agenter som behöver spåra unikhet för den virtuella datorn som en tjänsteleverantör. För att kunna hämta ett unikt ID för en virtuell dator, använder de `vmId` från Instance Metadata Service.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Svar**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Placering av containrar, datapartitionsbaserad feldomän/uppdateringsdomän

För vissa scenarier, placering av olika repliker är av yttersta vikt. Till exempel [HDFS repliken placering](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) eller placering av behållare via en [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) kanske du behöver känna till den `platformFaultDomain` och `platformUpdateDomain` Virtuellt datorn körs.
Du kan också använda [Tillgänglighetszoner](../../availability-zones/az-overview.md) att fatta beslut om dessa-instanserna.
Du kan fråga data direkt via Instance Metadata Service.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Svar**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Hämta mer information om den virtuella datorn under supportbegäran

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

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Hämta Azure-miljön där den virtuella datorn körs

Azure har olika nationella moln som [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Ibland kan du behöva Azure-miljön för att fatta beslut om vissa runtime. I följande exempel visas hur du kan åstadkomma detta beteende.

**Förfrågan**
``` bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Svar**
```
AZUREPUBLICCLOUD
```

### <a name="getting-the-tags-for-the-vm"></a>Hämtar taggarna för den virtuella datorn

Du har tilldelat taggar till dina Azure virtuella datorer för att organisera dem logiskt i en taxonomi. De taggar som tilldelats till en virtuell dator kan hämtas med hjälp av begäran nedan.

**Förfrågan**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Svar**

```text
Department:IT;Environment:Test;Role:WebRole
```

> [!NOTE]
> Taggar är semikolonavgränsad. Om en parser skrivs till programmässigt extrahera taggarna, får inte taggnamn och värden innehålla semikolon för att parsern ska fungera korrekt.

### <a name="validating-that-the-vm-is-running-in-azure"></a>Verifiera att den virtuella datorn körs i Azure

 Marketplace-leverantörer vill säkerställa att deras programvaran är licensierad för att köra i Azure. Om någon kopierar den virtuella Hårddisken till en lokal, sedan de ska ha ett sätt att identifiera som. Genom att anropa den Instance Metadata Service får Marketplace leverantörer signerad data som garanterar svar från Azure.

 > [!NOTE]
> Kräver jq installeras.

 **Förfrågan**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2018-10-01 | jq -r '.["signature"]' > signature
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
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34"
}
```

Data | Beskrivning
-----|------------
nonce | Användaren har angett valfri sträng med begäran. Om ingen nonce angavs i begäran, returneras den aktuella UTC-tidsstämpeln
plan | [Planera](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) för en virtuell dator i den är en Azure Marketplace-avbildning, som innehåller namn, produkt och utgivare
timestamp/createdOn | Tidsstämpel som första signerade dokumentet skapades
timestamp/expiresOn | Tidsstämpeln då det signerade dokumentet upphör att gälla
vmId |  [Unik identifierare](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) för den virtuella datorn

#### <a name="verifying-the-signature"></a>Verifiera signaturen

När du har hämtat signaturen ovan kan du kontrollera att signaturen är från Microsoft. Du kan också kontrollera mellanliggande certifikat och certifikatkedjan.

> [!NOTE]
> Certifikatet för offentliga moln och nationellt moln är olika.

 Regioner | Certifikat
---------|-----------------
[Alla allmänt tillgängliga Azure-regioner globalt](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure China](https://www.azure.cn/)                                                           | metadata.azure.cn
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
### <a name="failover-clustering-in-windows-server"></a>Redundanskluster i Windows Server

För vissa scenarier, när du frågar efter Instance Metadata Service med Redundansklustring, det är nödvändigt att lägga till en väg i routningstabellen.

1. Öppna Kommandotolken med administratörsbehörighet.

2. Kör följande kommando och anteckna adressen för gränssnittet för målet (`0.0.0.0`) i IPv4-routningstabellen.

```bat
route print
```

> [!NOTE] 
> Följande Exempelutdata från en Windows Server VM med redundanskluster aktiverat innehåller endast IPv4 routningstabellen för enkelhetens skull.

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

1. Kör följande kommando och använda adressen för gränssnittet för målet (`0.0.0.0`) som är (`10.0.1.10`) i det här exemplet.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
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
   * Skicka dina kommentarer om https://feedback.azure.com.
7. Fungerar detta för skala ange instansen för virtuella datorer?
   * Ja är Metadata service tillgänglig för skala ange instanser.
8. Hur får jag support för tjänsten?
   * Skapa ett supportärende för att få support för tjänsten i Azure-portalen för den virtuella datorn där det inte går att hämta metadata svar efter långt återförsök.
9. Jag begäran orsakade timeout för min anrop till tjänsten?
   * Metadata-anrop måste göras från den primära IP-adress som tilldelats till nätverkskortet för den virtuella datorn, även om du har ändrat vägarna det måste vara en väg för 169.254.0.0/16 adress utanför nätverkskortet.
10. Jag har uppdaterat min taggar i virtual machine scale Sets, men de visas inte i instanser till skillnad från virtuella datorer?
    * För närvarande för ScaleSets visar taggar endast att den virtuella datorn på en omstart/reimage/eller ändrar du en disk till instansen.

    ![Stöd för instans-Metadata](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [schemalagda händelser](scheduled-events.md)
