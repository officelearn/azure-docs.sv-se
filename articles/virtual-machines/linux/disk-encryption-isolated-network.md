---
title: Azure Disk-kryptering i ett isolerat nätverk
description: Den här artikeln innehåller felsökningstips för virtuella virtuella linux-datorer med Microsoft Azure Disk Encryption for Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: f2b84427b9aad2d18368d808fc618f3bfbe774ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460128"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Azure Disk-kryptering i ett isolerat nätverk

När anslutningen begränsas av en brandvägg, proxykrav eller NSG-inställningar (Network Security Group) kan möjligheten för tillägget att utföra nödvändiga uppgifter störas. Den här störningen kan resultera i statusmeddelanden som "Tilläggsstatus är inte tillgänglig på den virtuella datorn".

## <a name="package-management"></a>Pakethantering

Azure Disk Encryption beror på ett antal komponenter, som vanligtvis installeras som en del av ADE-aktivering om det inte redan finns. När du är bakom en brandvägg eller på annat sätt isolerad från Internet måste dessa paket vara förinstallerade eller tillgängliga lokalt.

Här är de paket som behövs för varje distribution. En fullständig lista över distributioner och volymtyper som stöds finns i [virtuella datorer och operativsystem som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-six, procps
- **CentOS 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, pyparted, procps-ng, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-reencrypt, pyparted, python-six
- **RedHat 7,2 - 7,7**: lsscsi, psmisc, lvm2, uuid, på, patch, cryptsetup, cryptsetup-reencrypt, procps-ng, util-linux
- **RedHat 6,8**: lsscsi, psmisc, lvm2, uuid, på, patch, cryptsetup-reencrypt
- **openSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

På Red Hat måste du se till att prenumerationshanteraren och yum är korrekt konfigurerade på Red Hat. Mer information finns i [Felsöka prenumerationshanterare och yum-problem](https://access.redhat.com/solutions/189533).  

När paket installeras manuellt måste de också uppgraderas manuellt när nya versioner släpps.

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
Alla nätverkssäkerhetsgruppinställningar som tillämpas måste fortfarande göra det möjligt för slutpunkten att uppfylla de dokumenterade nätverkskonfigurationsförutsättningarna för diskkryptering.  Se [Azure Disk Encryption: Nätverkskrav](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure DiskKryptering med Azure AD (tidigare version)

Om du använder [Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-overview-aad.md)måste Azure Active [Directory-biblioteket](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) installeras manuellt för alla distributioner (utöver de paket som är lämpliga för distributionerna, enligt [beskrivningen ovan](#package-management)).

När kryptering aktiveras med [Azure AD-autentiseringsuppgifter](disk-encryption-linux-aad.md)måste måldatorn tillåta anslutning till både Azure Active Directory-slutpunkter och Key Vault-slutpunkter. Aktuella slutpunkter för Azure Active Directory-autentisering finns i avsnitten 56 och 59 i dokumentationen för [Office 365-URL:er och IP-adressintervall.](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) Key Vault-instruktioner finns i dokumentationen om hur du [kommer åt Azure Key Vault bakom en brandvägg](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Azure-instansmetadatatjänst 

Den virtuella datorn måste kunna komma åt [azure instance metadata-tjänstslutpunkten,](instance-metadata-service.md) som använder`169.254.169.254`en välkänd icke-dirigerbar IP-adress ( ) som endast kan nås från den virtuella datorn.  Proxykonfigurationer som ändrar lokal HTTP-trafik till den här adressen (till exempel att lägga till ett X-Forwarded-For-huvud) stöds inte.

## <a name="next-steps"></a>Nästa steg

- Se fler steg för [felsökning av Azure-diskkryptering](disk-encryption-troubleshooting.md)
- [Azure-datakryptering i vila](../../security/fundamentals/encryption-atrest.md)
