---
title: Felsök problem med Azure NFS-filresursen – Azure Files
description: Felsök problem med Azure NFS-filresursen.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 661cfd5bb410a714bc42e0cd9676ac2ec08f8a45
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90709100"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Felsöka fil resurser i Azure NFS

Den här artikeln innehåller några vanliga problem som rör Azure NFS-filresurser. Den ger potentiella orsaker och lösningar när dessa problem uppstår.

## <a name="unable-to-create-an-nfs-share"></a>Det gick inte att skapa en NFS-resurs

### <a name="cause-1-subscription-is-not-enabled"></a>Orsak 1: prenumerationen är inte aktive rad

Din prenumeration har kanske inte registrerats för för hands versionen av Azure Files NFS. Du måste köra ytterligare en cmdletarna från antingen Cloud Shell eller en lokal Terminal för att aktivera funktionen.

> [!NOTE]
> Du kan behöva vänta upp till 30 minuter innan registreringen är klar.


#### <a name="solution"></a>Lösning

Använd följande skript för att registrera funktionen och resurs leverantören, Ersätt `<yourSubscriptionIDHere>` innan du kör skriptet:

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares - ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>Orsak 2: inställningar för lagrings konto som inte stöds

NFS är endast tillgängligt på lagrings konton med följande konfiguration:

- Nivå – Premium
- Konto Natura-FileStorage
- Redundans – LRS
- Regionerna – östra USA, östra USA 2, Storbritannien, södra, sydöstra Asien

#### <a name="solution"></a>Lösning

Följ anvisningarna i vår artikel: [så här skapar du en NFS-resurs](storage-files-how-to-create-nfs-shares.md).

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>Orsak 3: lagrings kontot skapades innan registreringen slutfördes

För att ett lagrings konto ska kunna använda funktionen måste det skapas när prenumerationen har slutfört registreringen för NFS. Det kan ta upp till 30 minuter innan registreringen är klar.

#### <a name="solution"></a>Lösning

När registreringen är klar följer du anvisningarna i vår artikel: [så här skapar du en NFS-resurs](storage-files-how-to-create-nfs-shares.md).

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Det går inte att ansluta till eller montera en fil resurs i Azure NFS

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>Orsak 1: begäran kommer från en klient i ett ej betrott nätverk/ej betrodda IP-adresser

Till skillnad från SMB har NFS inte användar-baserad autentisering. Autentiseringen för en resurs baseras på nätverks säkerhets regelns konfiguration. På grund av detta måste du antingen använda tjänstens slut punkt eller privata slut punkter för att säkerställa att endast säkra anslutningar upprättas till din NFS-resurs. För att få åtkomst till resurser lokalt, förutom privata slut punkter, måste du konfigurera VPN eller ExpressRoute. IP-adresser som läggs till i lagrings kontots lista över tillåtna för brand väggen ignoreras. Du måste använda någon av följande metoder för att konfigurera åtkomst till en NFS-resurs:


- [Tjänst slut punkt](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - Används av den offentliga slut punkten
    - Endast tillgängligt i samma region.
    - VNet-peering ger inte åtkomst till din resurs.
    - Varje virtuellt nätverk eller undernät måste läggas till separat i listan över tillåtna.
    - För lokal åtkomst kan tjänst slut punkter användas med ExpressRoute-, punkt-till-plats-och plats-till-plats-VPN, men vi rekommenderar att du använder privat slut punkt eftersom det är säkrare.

Följande diagram visar anslutningar med hjälp av offentliga slut punkter.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="Diagram över anslutning till offentliga slut punkter." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Privat slut punkt](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - Åtkomst är säkrare än tjänstens slut punkt.
    - Åtkomst till NFS-resurs via privat länk är tillgänglig i och utanför lagrings kontots Azure-region (över flera regioner lokalt)
    - Virtuell nätverks-peering med virtuella nätverk som finns i den privata slut punkten ger NFS-resurs åtkomst till klienter i peer-kopplade virtuella nätverk.
    - Privata slut punkter kan användas med ExpressRoute-, punkt-till-plats-och plats-till-plats-VPN.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Diagram över anslutning till privat slut punkt." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Orsak 2: säker överföring krävs har Aktiver ATS

Double Encryption stöds inte för NFS-resurser än. Azure tillhandahåller ett krypterings lager för alla data som överförs mellan Azure-datacenter med hjälp av MACSec. NFS-resurser kan bara nås från betrodda virtuella nätverk och via VPN-tunnlar. Ingen ytterligare kryptering av transport skikt är tillgängligt på NFS-resurser.

#### <a name="solution"></a>Lösning

Inaktivera säker överföring som krävs i ditt lagrings kontos konfigurations blad.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Skärm bild som visar bladet lagrings konto konfiguration och inaktiverar säker överföring krävs.":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>Orsak 3: NFS-vanligt paket har inte installerats
Innan du kör monterings kommandot installerar du paketet genom att köra det distribution kommandot nedan.

Kontrol lera om NFS-paketet är installerat genom att köra: `rpm qa | grep nfs-utils`

#### <a name="solution"></a>Lösning

Om paketet inte är installerat installerar du paketet på din distribution.

##### <a name="ubuntu-or-debian"></a>Ubuntu eller Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8 +, CentOS 8 +

Använd DNF Package Manager: `sudo dnf install nfs-common` .

Äldre versioner av Red Hat Enterprise Linux och CentOS använder yum Package Manager: `sudo yum install nfs-common` .

##### <a name="opensuse"></a>openSUSE

Använd zypper Package Manager: `sudo zypper install-nfscommon` .

### <a name="cause-4-firewall-blocking-port-2049"></a>Orsak 4: brand vägg blockerar port 2049

NFS-protokollet kommunicerar med servern via port 2049, kontrol lera att porten är öppen för lagrings kontot (NFS-servern).

#### <a name="solution"></a>Lösning

Kontrol lera att port 2049 är öppen på klienten genom att köra följande kommando: `telnet <storageaccountnamehere>.file.core.windows.net 2049` . Om porten inte är öppen öppnar du den.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att lösa problemet snabbt.