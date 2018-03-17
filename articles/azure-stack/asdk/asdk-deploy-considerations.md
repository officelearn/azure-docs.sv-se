---
title: "Krav för Azure-stacken Development Kit distribution (ASDK) | Microsoft Docs"
description: "Granska kraven för Azure Stack Development Kit (ASDK)-miljön och maskinvara."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 24a766ada1cdd1cb854b6869d571acd73e376327
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="azure-stack-deployment-planning-considerations"></a>Överväganden vid planering för Azure Stack-distribution
Innan du distribuerar Azure Stack Development Kit (ASDK) kontrollera att värddatorn development kit uppfyller kraven som beskrivs i den här artikeln.


## <a name="hardware"></a>Maskinvara
| Komponent | Minimum | Rekommenderas |
| --- | --- | --- |
| Diskenheter: operativsystem |1 OS-disk med minst 200 GB tillgängligt för systempartitionering (SSD och HDD) |1 OS-disk med minst 200 GB tillgängligt för systempartitionering (SSD och HDD) |
| Diskenheter: Allmänt development kit data<sup>*</sup>  |4 diskar. Varje disk innehåller minst 140 GB kapacitet (SSD och HDD). Alla tillgängliga diskar som används. |4 diskar. Varje disk innehåller minst 250 GB kapacitet (SSD eller HDD). Alla tillgängliga diskar som används. |
| Beräkna: CPU |Dubbla uttag: 12 fysiska kärnor (totalt) |Dubbla uttag: 16 fysiska kärnor (totalt) |
| Beräkna: minne |96 GB RAM-minne |128 GB RAM-minne (det är minst som stöd för PaaS-resursprovidrar.)|
| Beräkna: BIOS |Hyper-V aktiverat (med stöd för SLAT) |Hyper-V aktiverat (med stöd för SLAT) |
| Nätverk: NIC |Windows Server 2012 R2-certifiering krävs för NIC, inga specialfunktioner krävs |Windows Server 2012 R2-certifiering krävs för NIC, inga specialfunktioner krävs |
| HW-logotypcertifiering |[Certifierade för Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certifierade för Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

<sup>*</sup> Du behöver fler än det rekommenderade kapacitet om du planerar att lägga till många av de [marketplace-objekt](asdk-marketplace-item.md) från Azure.

**Konfiguration av enhet:** alla dataenheter måste vara av samma typ (alla SAS, alla SATA eller alla NVMe) och kapacitet. Om SAS-hårddiskar används måste diskenheter kopplas via en enskild sökväg (inga MPIO, stöd för flera sökvägar, tillhandahålls).

**HBA-konfigurationsalternativ**

* (Rekommenderas) Enkel HBA
* RAID HBA – Adaptern måste konfigureras i genomströmningsläge (”pass through”)
* RAID HBA – Diskar ska konfigureras som enskild disk, RAID-0

**Skriv kombinationer stöds bus och media**

* SATA-hårddisk
* SAS-hårddisk
* RAID-hårddisk
* RAID SSD (om medietypen är ospecificerade/okänt<sup>*</sup>)
* SATA SSD + SATA-hårddisk
* SAS SSD + SAS-hårddisk
* NVMe

<sup>*</sup> RAID-styrenheter utan direkt funktionen kan inte identifiera medietyp. Dessa domänkontrollanter Markera både hårddiskar och SSD som Ospecificerad. I så fall används SSD som beständig lagring i stället för cachelagring av enheter. Därför kan du distribuera development kit på dessa SSD.

**Exempel på HBA**: LSI 9207-8i, LSI-9300-8i eller LSI-9265-8i i genomströmningsläge (pass-through)

Det finns tillgängliga exempel på OEM-konfigurationer.

## <a name="operating-system"></a>Operativsystem
|  | **Krav** |
| --- | --- |
| **OS-Version** |Windows Server 2012 R2 eller senare. Operativsystemets version inte är driftskritisk innan distributionen startar, som du ska starta värddatorn till den virtuella Hårddisken som ingår i Azure Stack-installationen. Operativsystem och alla uppdateringar som krävs är redan integrerade i avbildningen. Använd inte en för att aktivera alla Windows Server-instanser som används i development kit. |

> [!TIP]
> När du har installerat operativsystemet, kan du använda den [distribution av förutsättningar för Azure-stacken](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) att bekräfta att maskinvaran uppfyller alla krav.

## <a name="account-requirements"></a>Kontokrav
Normalt distribuerar du development kit med anslutning till internet, där du kan ansluta till Microsoft Azure. I det här fallet måste du konfigurera ett konto för Azure Active Directory (Azure AD) för att distribuera development kit.

Om din miljö inte är ansluten till internet, eller om du inte vill använda Azure AD, kan du distribuera Azure-stacken genom att använda Active Directory Federation Services (AD FS). I development kit ingår instanser AD FS och Active Directory Domain Services. Om du distribuerar med hjälp av det här alternativet om behöver du inte konfigurera konton i förväg.

>[!NOTE]
Om du distribuerar med hjälp av AD FS-alternativet måste du omdistribuera Azure stackutrymme för att växla till Azure AD.

### <a name="azure-active-directory-accounts"></a>Azure Active Directory-konton
För att distribuera Azure-stacken genom att använda en Azure AD-konto, måste du förbereda en Azure AD-kontot innan du kör PowerShell-skript för distribution. Det här kontot blir Global administratör för Azure AD-klient. Den används för att etablera och delegera program och tjänstens huvudnamn för alla Azure Stack-tjänster som samverkar med Azure Active Directory och Graph API. Det används också som ägare till prenumerationen för standard-providern (som du kan ändra senare). Du kan logga in till Azure-stacken systemets administratörsportal genom att använda det här kontot.

1. Skapa ett Azure AD-konto som är directory-administratör för minst en Azure AD. Om du redan har ett konto kan använda du det. Annars kan du skapa ett kostnadsfritt på [ https://azure.microsoft.com/free/ ](http://azure.microsoft.com/pricing/free/) (i Kina, besök <http://go.microsoft.com/fwlink/?LinkID=717821> i stället). Om du planerar att senare [registrera Azure stacken med Azure](asdk-register.md), du måste ha en prenumeration i den nya konto.
   
    Spara autentiseringsuppgifterna för användning som administratören. Det här kontot kan konfigurera och hantera resurs moln, användarkonton, planer för innehavare, kvoter och priser. I portalen kan de skapa webbplatsmoln, privata moln för virtuella datorer, skapa planer och hantera användarprenumerationer.
1. Skapa minst ett test-användarkonto i din Azure AD så att du kan logga in i development Kit som en klient.
   
   | **Azure Active Directory-konto** | **Stöds?** |
   | --- | --- |
   | Arbets- eller skolkonto konto med giltig offentlig Azure-prenumeration |Ja |
   | Microsoft-konto med giltig prenumeration på offentlig Azure |Ja |
   | Arbets- eller skolkonto konto med giltig Kina Azure-prenumeration |Ja |
   | Arbets- eller skolkonto konto med giltig US Government Azure-prenumeration |Ja |

## <a name="network"></a>Nätverk
### <a name="switch"></a>Växel
En tillgänglig port på en växel för den development kit datorn.  

Development kit datorn stöder anslutning till en åtkomst-växelport eller trunk port. Inga särskilda funktioner krävs på växeln. Om du använder en segmentport eller om du behöver konfigurera ett VLAN-ID måste du ange VLAN-ID som en parameter för distribution.

### <a name="subnet"></a>Undernät
Anslut inte development kit datorn till följande undernät:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Dessa undernät är reserverade för interna nätverk i miljön development kit.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Endast IPv4 stöds. Du kan inte skapa IPv6-nätverk.

### <a name="dhcp"></a>DHCP
Kontrollera att det finns en DHCP-server i nätverket som nätverkskortet ansluts till. Om DHCP inte är tillgängligt måste du förbereda ytterligare ett statiskt IPv4-nätverk än det som används av värden. Du måste ange IP-adress och gateway som distributionsparameter.

### <a name="internet-access"></a>Internet-åtkomst
Azure-stacken kräver åtkomst till Internet, antingen direkt eller via en transparent proxy. Azure-stacken stöder inte konfigurationen av en webbproxy för Internet-åtkomst. Både värd-IP och nya IP-Adressen som tilldelats MAS-BGPNAT01 (med DHCP eller statiska IP-) måste kunna få åtkomst till Internet. Portarna 80 och 443 används under graph.windows.net och login.microsoftonline.com domäner.


## <a name="next-steps"></a>Nästa steg
[Hämta distributionspaketet ASDK](asdk-download.md)
