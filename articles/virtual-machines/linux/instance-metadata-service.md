---
title: Azure Instance Metadata Service
description: Lär dig mer om Azure-Instance Metadata Service och hur den innehåller information om de virtuella dator instanser som körs.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: 79a583575acc6e3b3b2551046d57355bbf74a663
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966810"
---
# <a name="azure-instance-metadata-service-imds"></a>Azure Instance Metadata Service (IMDS)

Azure-Instance Metadata Service (IMDS) innehåller information om de virtuella dator instanser som körs och kan användas för att hantera och konfigurera dina virtuella datorer.
Den här informationen omfattar SKU, lagring, nätverkskonfigurationer och kommande underhålls händelser. En fullständig lista över tillgängliga data finns i [API: er för metadata](#metadata-apis).
Instance Metadata Service tillgänglig för körning av virtuella datorer och virtuella datorers skalnings uppsättnings instanser. Alla API: er stöder virtuella datorer som skapats/hanteras med [Azure Resource Manager](/rest/api/resources/). Endast de attesterings-och nätverks slut punkter som har stöd för klassiska virtuella datorer (ej ARM) och attesterade gör det bara till en begränsad omfattning.

Azures IMDS är en REST-slutpunkt som är tillgänglig på en välkänd icke-flyttbar IP-adress ( `169.254.169.254` ), men den kan bara nås från den virtuella datorn. Kommunikationen mellan VM-och IMDS lämnar aldrig värden.
Vi rekommenderar att du använder dina HTTP-klienter för att kringgå Webbproxyservrar i den virtuella datorn när du frågar IMDS och behandlar `169.254.169.254` samma som [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="security"></a>Säkerhet

Instance Metadata Service slut punkten kan bara nås från den virtuella dator instansen som körs på en IP-adress som inte är dirigerbart. Dessutom avvisas alla begär Anden med en `X-Forwarded-For` rubrik av tjänsten.
Begär Anden måste också innehålla en `Metadata: true` rubrik för att säkerställa att den faktiska begäran var direkt avsedd och inte en del av oavsiktlig omdirigering.

> [!IMPORTANT]
> Instance Metadata Service är inte en kanal för känsliga data. Slut punkten är öppen för alla processer på den virtuella datorn. Information som exponeras genom den här tjänsten bör betraktas som delad information till alla program som körs i den virtuella datorn.

## <a name="usage"></a>Användning

### <a name="accessing-azure-instance-metadata-service"></a>Åtkomst till Azure-Instance Metadata Service

För att komma åt Instance Metadata Service skapar du en virtuell dator från [Azure Resource Manager](/rest/api/resources/) eller [Azure Portal](https://portal.azure.com)och följer sedan exemplen nedan.
Fler exempel på hur du kan fråga IMDS finns på [Azure instance metadata-exempel](https://github.com/microsoft/azureimds).

Nedan visas exempel koden för att hämta alla metadata för en instans, för att få åtkomst till en speciell data källa, se avsnittet [metadata-API](#metadata-apis) . 

**Förfrågan**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01"
```

**Response**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempel svar är ganska utskrivet för läsbarhet.

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
        "osType": "linux",
        "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
        "plan": {
            "name": "planName",
            "product": "planProduct",
            "publisher": "planPublisher"
        },
        "platformFaultDomain": "36",
        "platformUpdateDomain": "42",
        "publicKeys": [{
                "keyData": "ssh-rsa 0",
                "path": "/home/user/.ssh/authorized_keys0"
            },
            {
                "keyData": "ssh-rsa 1",
                "path": "/home/user/.ssh/authorized_keys1"
            }
        ],
        "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
        "resourceGroupName": "macikgo-test-may-23",
        "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
        "securityProfile": {
            "secureBootEnabled": "true",
            "virtualTpmEnabled": "false"
        },
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }],
            "imageReference": {
                "id": "",
                "offer": "UbuntuServer",
                "publisher": "Canonical",
                "sku": "16.04.0-LTS",
                "version": "latest"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "createOption": "FromImage",
                "diskSizeGB": "30",
                "diffDiskSettings": {
                    "option": "Local"
                },
                "encryptionSettings": {
                    "enabled": "false"
                },
                "image": {
                    "uri": ""
                },
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampleosdiskname",
                "osType": "Linux",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }
        },
        "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    },
    "network": {
        "interface": [{
            "ipv4": {
               "ipAddress": [{
                    "privateIpAddress": "10.144.133.132",
                    "publicIpAddress": ""
                }],
                "subnet": [{
                    "address": "10.144.133.128",
                    "prefix": "26"
                }]
            },
            "ipv6": {
                "ipAddress": [
                 ]
            },
            "macAddress": "0011AAFFBB22"
        }]
    }
}
```

### <a name="data-output"></a>Data utdata

Som standard returnerar Instance Metadata Service data i JSON-format ( `Content-Type: application/json` ). Vissa API: er kan dock returnera data i olika format om det behövs.
Följande tabell är en referens till andra API: er för data format som kan ha stöd för.

API | Standard data format | Andra format
--------|---------------------|--------------
/attested | json | inget
/identity | json | inget
/instance | json | text
/scheduledevents | json | inget

Om du vill komma åt ett svar som inte är standardformat anger du det begärda formatet som en frågesträngparametern i begäran. Exempel:

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> För löv-noder i/metadata/instance `format=json` fungerar inte. För dessa frågor `format=text` måste anges explicit eftersom standardformatet är JSON.

### <a name="versioning"></a>Versionshantering

Instance Metadata Service har versions hantering och angett att API-versionen i HTTP-begäran är obligatorisk.

De API-versioner som stöds är: 
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01

> [!NOTE]
> Version 2020-10-01 är för närvarande distribuerad och är kanske ännu inte tillgänglig i varje region.

När nya versioner läggs till kan äldre versioner fortfarande nås för kompatibilitet om skripten har beroenden för vissa data format.

Om ingen version anges returneras ett fel med en lista över de senaste versionerna som stöds.

> [!NOTE]
> Svaret är en JSON-sträng. I följande exempel visas fel tillståndet när ingen version anges, svaret är ganska utskrivet för läsbarhet.

**Förfrågan**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance"
```

**Response**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

## <a name="metadata-apis"></a>API: er för metadata

Metadata Service innehåller flera API: er som representerar olika data källor.

API | Beskrivning | Version introducerad
----|-------------|-----------------------
/attested | Se [attesterade data](#attested-data) | 2018-10-01
/identity | Se [Hämta en åtkomsttoken](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | Se [instans-API](#instance-api) | 2017-04-02
/scheduledevents | Se [schemalagda händelser](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>Instans-API

Instans-API: t visar viktiga metadata för VM-instanser, inklusive VM, nätverk och lagring. Följande kategorier kan nås via instans/Compute:

Data | Beskrivning | Version introducerad
-----|-------------|-----------------------
azEnvironment | Azure-miljö där den virtuella datorn körs i | 2018-10-01
customData | Den här funktionen är för närvarande inaktive rad. Vi kommer att uppdatera den här dokumentationen när den blir tillgänglig | 2019-02-01
isHostCompatibilityLayerVm | Identifierar om den virtuella datorn körs på värdens kompatibilitetsnivå | 2020-06-01
licenseType | Typ av licens för [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit). Observera att detta endast är tillgängligt för AHB-aktiverade virtuella datorer | 2020-09-01
location | Azure-regionen som den virtuella datorn körs i | 2017-04-02
name | Namn på den virtuella datorn | 2017-04-02
offer | Erbjudande information för den virtuella dator avbildningen och finns bara för avbildningar som distribuerats från Azures avbildnings Galleri | 2017-04-02
osProfile.adminUsername | Anger namnet på administratörs kontot | 2020-07-15
osProfile. computerName | Anger namnet på datorn | 2020-07-15
osProfile. disablePasswordAuthentication | Anger om lösenordsautentisering är inaktiverat. Observera att detta bara är tillgängligt för virtuella Linux-datorer | 2020-10-01
osType | Linux eller Windows | 2017-04-02
placementGroupId | [Placerings grupp](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) för den virtuella datorns skalnings uppsättning | 2017-08-01
planera | [Planera](/rest/api/compute/virtualmachines/createorupdate#plan) som innehåller namn, produkt och utgivare för en virtuell dator om det är en Azure Marketplace-avbildning | 2018-04-02
platformUpdateDomain |  [Uppdatera den domän](../manage-availability.md) som den virtuella datorn körs i | 2017-04-02
platformFaultDomain | [Feldomän](../manage-availability.md) som den virtuella datorn körs i | 2017-04-02
CSP | Provider för den virtuella datorn | 2018-10-01
publicKeys | [Samling offentliga nycklar](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) som har tilldelats den virtuella datorn och sökvägar | 2018-04-02
utgivare | Utgivare av VM-avbildningen | 2017-04-02
resourceGroupName | [Resurs grupp](../../azure-resource-manager/management/overview.md) för den virtuella datorn | 2017-08-01
resourceId | Resursens [fullständigt kvalificerade](/rest/api/resources/resources/getbyid) ID | 2019-03-11
sku | En speciell SKU för VM-avbildningen | 2017-04-02
securityProfile. secureBootEnabled | Identifierar om UEFI säker start är aktiverat på den virtuella datorn | 2020-06-01
securityProfile.virtualTpmEnabled | Identifierar om den virtuella Trusted Platform Module (TPM) är aktive rad på den virtuella datorn | 2020-06-01
storageProfile | Se [lagrings profil](#storage-metadata) | 2019-06-01
subscriptionId | Azure-prenumeration för den virtuella datorn | 2017-08-01
tags | [Taggar](../../azure-resource-manager/management/tag-resources.md) för den virtuella datorn  | 2017-08-01
tagsList | Taggar formaterade som en JSON-matris för enklare programmerings parsning  | 2019-06-04
version | Version av VM-avbildningen | 2017-04-02
vmId | [Unikt ID](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) för den virtuella datorn | 2017-04-02
vmScaleSetName | [Skal uppsättnings namn för virtuell](../../virtual-machine-scale-sets/overview.md) dator för den virtuella datorns skalnings uppsättning | 2017-12-01
vmSize | [VM-storlek](../sizes.md) | 2017-04-02
zon | [Tillgänglighets zon](../../availability-zones/az-overview.md) för den virtuella datorn | 2017-12-01

### <a name="sample-1-tracking-vm-running-on-azure"></a>Exempel 1: spåra virtuell dator som körs på Azure

Som tjänst leverantör kan du behöva spåra antalet virtuella datorer som kör program varan eller ha agenter som behöver spåra unika virtuella datorer. Om du vill kunna hämta ett unikt ID för en virtuell dator använder du `vmId` fältet från instance metadata service.

**Förfrågan**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Response**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-containers-data-partitions-based-faultupdate-domain"></a>Exempel 2: placering av behållare, data-partitioner som är baserade fel/uppdaterings domän

För vissa scenarier är placeringen av olika data repliker av primär betydelse. Till exempel, [HDFS-replik placering](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) eller container placering via en [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) kan du behöva känna till `platformFaultDomain` och `platformUpdateDomain` den virtuella datorn körs på.
Du kan också använda [Tillgänglighetszoner](../../availability-zones/az-overview.md) för instanserna för att fatta beslut.
Du kan fråga dessa data direkt via Instance Metadata Service.

**Förfrågan**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Response**

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>Exempel 3: Hämta mer information om den virtuella datorn under support ärendet

Som tjänst leverantör kan du få ett support samtal där du vill ha mer information om den virtuella datorn. Att be kunden att dela dina beräknings-metadata kan ge grundläggande information om support teknikern för att veta om vilken typ av virtuell dator som finns i Azure.

**Förfrågan**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

**Response**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempel svar är ganska utskrivet för läsbarhet.

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "Windows-Server-2012-R2-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

### <a name="sample-4-getting-azure-environment-where-the-vm-is-running"></a>Exempel 4: få Azure-miljö där den virtuella datorn körs

Azure har olika suveräna moln som [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Ibland behöver du Azure-miljön för att kunna göra vissa körnings beslut. I följande exempel visas hur du kan få det här beteendet.

**Förfrågan**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Response**

```text
AzurePublicCloud
```

Molnet och värdena i Azure-miljön visas nedan.

 Moln   | Azure-miljö
---------|-----------------
[Alla allmänt tillgängliga globala Azure-regioner](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure Kina 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Metadata för nätverk 

Metadata för nätverket är en del av instans-API: et. Följande nätverks kategorier är tillgängliga via instansen/nätverks slut punkten.

Data | Beskrivning | Version introducerad
-----|-------------|-----------------------
IPv4/privateIpAddress | Lokal IPv4-adress för den virtuella datorn | 2017-04-02
IPv4/publicIpAddress | Offentlig IPv4-adress för den virtuella datorn | 2017-04-02
undernät/adress | Den virtuella datorns under näts adress | 2017-04-02
undernät/prefix | Undernätsprefixet, exempel 24 | 2017-04-02
IPv6/ipAddress | Lokal IPv6-adress för den virtuella datorn | 2017-04-02
macAddress | Mac-adress för virtuell dator | 2017-04-02

> [!NOTE]
> Alla API-svar är JSON-strängar. Alla följande exempel svar är ganska utskrivna för läsbarhet.

#### <a name="sample-1-retrieving-network-information"></a>Exempel 1: hämtar nätverks information

**Förfrågan**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Response**

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

#### <a name="sample-2-retrieving-public-ip-address"></a>Exempel 2: hämtar offentlig IP-adress

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Metadata för lagring

Lagrings-metadata ingår i instans-API: t under instans/Compute/storageProfile-slutpunkt.
Den innehåller information om de lagrings diskar som är kopplade till den virtuella datorn. 

Lagrings profilen för en virtuell dator är uppdelad i tre kategorier: avbildnings referens, OS-disk och data diskar.

Objektet bild referens innehåller följande information om operativ system avbildningen:

Data    | Beskrivning
--------|-----------------
id      | Resurs-ID
offer   | Erbjudande för plattformen eller Marketplace-avbildningen
utgivare | Avbildnings utgivare
sku     | Bild-SKU
version | Version av plattformen eller Marketplace-avbildningen

Objektet OS-disk innehåller följande information om den OS-disk som används av den virtuella datorn:

Data    | Beskrivning
--------|-----------------
cachelagring | Krav för cachelagring
createOption | Information om hur den virtuella datorn skapades
diffDiskSettings | Inställningar för tillfälliga diskar
diskSizeGB | Disk storlek i GB
image   | Virtuell hård disk för käll användar avbildning
enheten     | Diskens logiska enhets nummer
managedDisk | Parametrar för hanterade diskar
name    | Disknamn
disken     | Virtuell hårddisk
writeAcceleratorEnabled | Huruvida writeAccelerator har Aktiver ATS på disken

Data disks-matrisen innehåller en lista över data diskar som är anslutna till den virtuella datorn. Varje data disk objekt innehåller följande information:

Data    | Beskrivning
--------|-----------------
cachelagring | Krav för cachelagring
createOption | Information om hur den virtuella datorn skapades
diffDiskSettings | Inställningar för tillfälliga diskar
diskSizeGB | Disk storlek i GB
encryptionSettings | Krypterings inställningar för disken
image   | Virtuell hård disk för käll användar avbildning
managedDisk | Parametrar för hanterade diskar
name    | Disknamn
osType  | Typ av operativ system som ingår i disken
disken     | Virtuell hårddisk
writeAcceleratorEnabled | Huruvida writeAccelerator har Aktiver ATS på disken

I följande exempel visas hur du frågar den virtuella datorns lagrings information.

**Förfrågan**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Response**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempel svar är ganska utskrivet för läsbarhet.

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>VM-Taggar

VM-Taggar ingår i instans-API: t under slut punkt för instans/beräkning/taggar.
Taggar kan ha tillämpats på den virtuella Azure-datorn för att logiskt organisera dem i en taxonomi. Taggarna som tilldelas till en virtuell dator kan hämtas med hjälp av begäran nedan.

**Förfrågan**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Response**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags`Fältet är en sträng med taggarna avgränsade med semikolon. Dessa utdata kan vara ett problem om semikolon används i själva taggarna. Om en parser skrivs för att program mässigt extrahera taggarna bör du förlita dig på `tagsList` fältet. `tagsList`Fältet är en JSON-matris utan avgränsare och därmed lättare att parsa.

**Förfrågan**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04"
```

**Response**

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

## <a name="attested-data"></a>Bestyrkade data

En del av scenariot som hanteras av Instance Metadata Service är att tillhandahålla garantier att de data som tillhandahålls kommer från Azure. Vi loggar en del av den här informationen så att Marketplace-avbildningar kan vara säker på att de är en image som körs på Azure.

### <a name="sample-1-getting-attested-data"></a>Exempel 1: Hämta attestering av data

> [!NOTE]
> Alla API-svar är JSON-strängar. Följande exempel svar är ganska utskrivna för läsbarhet.

**Förfrågan**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

API-version är ett obligatoriskt fält. Se [användnings avsnittet](#usage) för API-versioner som stöds.
Nonce är en valfri sträng med 10 siffror. Om den inte anges, returnerar IMDS den aktuella UTC-tidsstämpeln i sitt ställe.

> [!NOTE]
> På grund av IMDS-mekanismen kan ett tidigare cachelagrat nonce-värde returneras.

**Response**

> [!NOTE]
> Svaret är en JSON-sträng. Följande exempel svar är ganska utskrivet för läsbarhet.

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Signatur-bloben är en [PKCS7](https://aka.ms/pkcs7) -signerad version av dokumentet. Den innehåller certifikatet som används för signering tillsammans med viss VM-specifik information. För virtuella ARM-datorer omfattar detta vmId, SKU, nonce, subscriptionId, tidstämpel för att skapa och upphör ande av dokumentet och plan informationen om avbildningen. Plan informationen är bara ifylld för Azure Marketplace-avbildningar. För klassiska virtuella datorer (inte ARM) är det bara vmId som är garanterat ifyllt. Certifikatet kan extraheras från svaret och används för att verifiera att svaret är giltigt och kommer från Azure.
Dokumentet innehåller följande fält:

Data | Beskrivning | Version introducerad
-----|-------------|-----------------------
licenseType | Typ av licens för [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit). Observera att detta endast är tillgängligt för AHB-aktiverade virtuella datorer | 2020-09-01
Nnär | En sträng som kan anges med begäran. Om inget nonce angavs används den aktuella UTC-tidsstämpeln | 2018-10-01
planera | [Avbildnings planen för Azure Marketplace](/rest/api/compute/virtualmachines/createorupdate#plan). Innehåller plan-ID (namn), produkt avbildning eller erbjudande (produkt) och utgivar-ID (utgivare). | 2018-10-01
Timestamp/createdOn | UTC-tidsstämpeln för när det signerade dokumentet skapades | 2018-20-01
tidsstämpel/expiresOn | UTC-tidsstämpeln för när det signerade dokumentet upphör att gälla | 2018-10-01
vmId |  [Unikt ID](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) för den virtuella datorn | 2018-10-01
subscriptionId | Azure-prenumeration för den virtuella datorn | 2019-04-30
sku | En speciell SKU för VM-avbildningen | 2019-11-01

> [!NOTE]
> För klassiska virtuella datorer (inte ARM) är det bara vmId som är garanterat ifyllt.

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>Exempel 2: verifierar att den virtuella datorn körs i Azure

Marketplace-leverantörer vill se till att deras program vara licensieras att köras endast i Azure. Om någon kopierar den virtuella hård disken till en lokal plats, ska de ha möjlighet att identifiera den. Genom att ringa till Instance Metadata Service kan Marketplace-leverantörer Hämta signerade data som endast garanterar svar från Azure.

> [!NOTE]
> Kräver att JQ installeras.

**Förfrågan**

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Response**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Kontrol lera att signaturen kommer från Microsoft Azure och kontrol lera om det finns fel i certifikat kedjan.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

> [!NOTE]
> På grund av IMDS-mekanismen kan ett tidigare cachelagrat nonce-värde returneras.

Nonce i det signerade dokumentet kan jämföras om du angav en nonce-parameter i den första begäran.

> [!NOTE]
> Certifikatet för det offentliga molnet och det suveräna molnet är annorlunda.

Moln | Certifikat
------|------------
[Alla allmänt tillgängliga globala Azure-regioner](https://azure.microsoft.com/regions/) | *. metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *. metadata.azure.us
[Azure Kina 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *. metadata.azure.cn
[Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/)                | *. metadata.microsoftazure.de

> [!NOTE]
> Det finns ett känt problem runt certifikatet som används för signering. Certifikaten kanske inte har en exakt matchning av `metadata.azure.com` för det offentliga molnet. Certifierings valideringen bör därför tillåta ett eget namn från valfri `.metadata.azure.com` under domän.

I de fall där det mellanliggande certifikatet inte kan laddas ned på grund av nätverks begränsningar under verifieringen, kan det mellanliggande certifikatet fästas. Azure kommer dock att återställas över certifikaten enligt PKI-praxis enligt standard. De fästa certifikaten måste uppdateras när förnyelsen sker. När en ändring av uppdateringen av mellanliggande certifikat planeras uppdateras Azure-bloggen och Azure-kunder meddelas. Du hittar de mellanliggande certifikaten [här](https://www.microsoft.com/pki/mscorp/cps/default.htm). De mellanliggande certifikaten för varje region kan vara olika.

> [!NOTE]
> Mellanliggande certifikat för Azure Kina 21Vianet kommer från DigiCert globala rot certifikat utfärdare i stället för Baltimore.
Om du har fäst mellanliggande certifikat för Azure Kina som en del av ändringen av rot kedjan måste de mellanliggande certifikaten uppdateras.

## <a name="managed-identity-via-metadata-service"></a>Hanterad identitet via Metadata Service

En systemtilldelad hanterad identitet kan aktive ras på den virtuella datorn eller en eller flera användare som tilldelats hanterade identiteter kan tilldelas den virtuella datorn.
Token för hanterade identiteter kan sedan begäras från Instance Metadata Service. Dessa token kan användas för att autentisera med andra Azure-tjänster som Azure Key Vault.

Detaljerade anvisningar om hur du aktiverar den här funktionen finns i [Hämta en](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)åtkomsttoken.

## <a name="scheduled-events-via-metadata-service"></a>Schemalagda händelser via Metadata Service
Du kan hämta status för schemalagda händelser via metadatatjänst och sedan kan användaren ange en uppsättning åtgärder som ska utföras vid dessa händelser.  Se [schemalagda händelser](scheduled-events.md) för mer information. 

## <a name="regional-availability"></a>Regional tillgänglighet

Tjänsten är **allmänt tillgänglig** i alla Azure-moln.

## <a name="sample-code-in-different-languages"></a>Exempel kod på olika språk

Exempel på hur du anropar metadata service med olika språk i den virtuella datorn:

Språk      | Exempel
--------------|----------------
Bash          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="error-and-debugging"></a>Fel och fel sökning

Om det inte går att hitta ett data element eller en felaktig begäran, returnerar Instance Metadata Service vanliga HTTP-fel. Exempel:

HTTP-statuskod | Orsak
----------------|-------
200 OK |
400 – Felaktig begäran | Saknad `Metadata: true` rubrik eller saknad parameter `format=json` vid fråga till en lövnod
404 – Hittades inte | Det begärda elementet finns inte
metoden 405 tillåts inte | Endast `GET` begär Anden stöds
410 borta | Försök igen om en stund i högst 70 sekunder
429 För många förfrågningar | API: et stöder för närvarande högst 5 frågor per sekund
500-tjänst fel     | Försök igen om en stund

### <a name="known-issues-and-faq"></a>Kända problem och vanliga frågor och svar

1. Jag får felet `400 Bad Request, Required metadata header not specified` . Vad betyder detta?
   * Den Instance Metadata Service kräver att rubriken `Metadata: true` skickas i begäran. Om du skickar den här rubriken i REST-anropet får du till gång till Instance Metadata Service.
1. Varför får jag inte beräknings information för min virtuella dator?
   * För närvarande stöder Instance Metadata Service endast instanser som skapats med Azure Resource Manager.
1. Jag har skapat min virtuella dator genom att Azure Resource Manager en och tillbaka. Varför visas inte information om att beräkna metadata?
   * För virtuella datorer som skapats efter sep 2016 lägger du till en [tagg](../../azure-resource-manager/management/tag-resources.md) för att börja se Compute metadata. För äldre virtuella datorer (som skapats före sep 2016) lägger du till/tar bort tillägg eller data diskar till den eller de virtuella dator instanserna för att uppdatera metadata.
1. Jag ser inte alla data som är ifyllda för nya versioner
   * För virtuella datorer som skapats efter sep 2016 lägger du till en [tagg](../../azure-resource-manager/management/tag-resources.md) för att börja se Compute metadata. För äldre virtuella datorer (som skapats före sep 2016) lägger du till/tar bort tillägg eller data diskar till den eller de virtuella dator instanserna för att uppdatera metadata.
1. Varför får jag fel meddelandet `500 Internal Server Error` `410 Resource Gone` ?
   * Gör om din begäran baserat på exponentiellt system eller andra metoder som beskrivs i [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults). Om problemet kvarstår skapar du ett support ärende i Azure Portal för den virtuella datorn.
1. Kommer detta att fungera för instanser av skalnings uppsättningar för virtuella datorer?
   * Tjänsten Yes metadata är tillgänglig för instanser av skalnings uppsättningar.
1. Jag uppdaterade mina taggar i Virtual Machine Scale Sets, men de visas inte i instanserna på samma sätt som virtuella datorer med en instans?
   * För närvarande används taggar för skalnings uppsättningar bara på den virtuella datorn vid omstart, avbildning eller disk ändring till instansen.
1. Jag får en timeout för mitt samtal till tjänsten?
   * Metadata-anrop måste göras från den primära IP-adress som tilldelats till det primära nätverkskortet på den virtuella datorn. Om du har ändrat dina vägar måste du dessutom ha en väg för 169.254.169.254-/32-adressen i den virtuella datorns lokala routningstabell.
   * <details>
        <summary>Verifierar routningstabellen</summary>

        1. Dumpa din lokala routningstabell med ett kommando som `netstat -r` och leta efter posten IMDS (t. ex.):
            ```console
            ~$ netstat -r
            Kernel IP routing table
            Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
            default         _gateway        0.0.0.0         UG        0 0          0 eth0
            168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
            169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
            172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
            ```
        1. Kontrol lera att det finns en väg för `169.254.169.254` och notera motsvarande nätverks gränssnitt (t. ex. `eth0` ).
        1. Dumpa gränssnitts konfigurationen för motsvarande gränssnitt i routningstabellen (Observera att det exakta namnet på konfigurations filen kan variera)
            ```console
            ~$ cat /etc/netplan/50-cloud-init.yaml
            network:
            ethernets:
                eth0:
                    dhcp4: true
                    dhcp4-overrides:
                        route-metric: 100
                    dhcp6: false
                    match:
                        macaddress: 00:0d:3a:e4:c7:2e
                    set-name: eth0
            version: 2
            ```
        1. Om du använder en dynamisk IP-adress noterar du MAC-adressen. Om du använder en statisk IP-adress kan du anteckna IP-adresser och/eller MAC-adressen i listan.
        1. Bekräfta att gränssnittet motsvarar det primära NÄTVERKSKORTet för den virtuella datorn och den primära IP-adressen. Du kan hitta det primära NÄTVERKSKORTet/IP-adressen genom att titta på nätverks konfigurationen i Azure Portal eller genom att titta på den [med Azure CLI](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-show). Observera offentliga och privata IP-adresser (och MAC-adressen om du använder CLI). PowerShell CLI-exempel:
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: ipexample606 True 00-0D-3A-E4-C7-2E
            ```
        1. Om de inte matchar uppdaterar du routningstabellen så att det primära NÄTVERKSKORTet/IP-adressen är mål.
    </details>

## <a name="support-and-feedback"></a>Support och feedback

Skicka in feedback och kommentarer om https://feedback.azure.com .

Om du vill få support för tjänsten skapar du ett support ärende i Azure Portal för den virtuella datorn där du inte kan få svar på metadata efter långa återförsök.
Använd problem typen `Management` och välj `Instance Metadata Service` som kategori.

![Stöd för instansen metadata](./media/instance-metadata-service/InstanceMetadata-support.png "Skärm bild: öppna ett support ärende när du har problem med Instance Metadata Service")

## <a name="next-steps"></a>Nästa steg

Läs mer om:
1. [Hämta en åtkomsttoken för den virtuella datorn](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).
1. [Schemalagda händelser](scheduled-events.md)