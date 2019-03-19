---
title: Azure Stack Development Kit (ASDK) distributionskraven | Microsoft Docs
description: Granska kraven för Azure Stack Development Kit (ASDK) miljön och maskinvarukrav.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: f3fdfcc6a2fbb527582d4bf242c039a778b84ed1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877997"
---
# <a name="azure-stack-deployment-planning-considerations"></a>Saker att tänka på för Azure Stack-distribution
Innan du distribuerar Azure Stack Development Kit (ASDK) måste du kontrollera att värddatorn development kit uppfyller kraven som beskrivs i den här artikeln.


## <a name="hardware"></a>Maskinvara
| Komponent | Minimum | Rekommenderas |
| --- | --- | --- |
| Diskenheter: Operativsystem |1 operativsystemdisk med minst 200 GB tillgängligt för systempartitionering (SSD och HDD) |1 OS-disk med minst 200 GB tillgängligt för systempartitionering (SSD och HDD) |
| Diskenheter: Allmän development kit data<sup>*</sup>  |4 diskar. Varje disk innehåller minst 240 GB kapacitet (SSD och HDD). Alla tillgängliga diskar används. |4 diskar. Varje disk innehåller minst 400 GB kapacitet (SSD och HDD). Alla tillgängliga diskar används. |
| Beräkna: Processor |Dual-Socket: 16 fysiska kärnor (totalt) |Dual-Socket: 20 fysiska kärnor (totalt) |
| Beräkna: Minne |192 GB RAM |256 GB RAM-MINNE |
| Beräkna: BIOS |Hyper-V aktiverat (med stöd för SLAT) |Hyper-V aktiverat (med stöd för SLAT) |
| Nätverk: Nätverkskort |Windows Server 2012 R2 Certification. Inga specialfunktioner krävs |Windows Server 2012 R2 Certification. Inga specialfunktioner krävs |
| HW-logotypcertifiering |[Certifierat för Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certifierade för Windows Server 2016](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

<sup>*</sup> Du behöver fler än det rekommenderade kapacitet om du planerar att lägga till många av de [marketplace-objekt](asdk-marketplace-item.md) från Azure.

**Konfiguration av datadiskenhet:** Alla dataenheter måste vara av samma typ (alla SAS, SATA eller alla NVMe) och kapacitet. Om SAS-hårddiskar används måste diskenheter kopplas via en enskild sökväg (inga MPIO, stöd för flera sökvägar, tillhandahålls).

**HBA-konfigurationsalternativ**

* (Rekommenderas) Enkel HBA
* RAID HBA – Adaptern måste konfigureras i genomströmningsläge (”pass through”)
* RAID HBA – Diskar ska konfigureras som enskild disk, RAID-0

**Skriv kombinationer stöds bus och media**

* SATA-hårddisk
* SAS-hårddisk
* RAID-hårddisk
* RAID-SSD (om medietypen är Ospecificerad/okänd<sup>*</sup>)
* SATA SSD + SATA-hårddisk
* SAS SSD + SAS-hårddisk
* NVMe

<sup>*</sup> RAID-styrenheter utan genomströmningskapacitet kan inte identifiera medietyp. Sådana styrenheter markerar både HDD och SSD som ospecificerade. I så fall används SSD som beständig lagring i stället för cachelagringsenheter. Därför kan du distribuera development kit på dessa SSD-enheter.

**Exempel på HBA**: LSI 9207-8i, LSI-9300-8i eller LSI-9265-8i i genomströmningsläge

Det finns tillgängliga exempel på OEM-konfigurationer.

## <a name="operating-system"></a>Operativsystem
|  | **Krav** |
| --- | --- |
| **OS-Version** |Windows Server 2016 eller senare. Versionen av operativsystemet inte är det viktigaste innan distributionen startar när du ska starta värddatorn i den virtuella Hårddisken som ingår i Azure Stack-installationen. Operativsystemet och alla uppdateringar som krävs är redan integrerade i avbildningen. Använd inte några nycklar för att aktivera alla Windows Server-instanser som används i development kit. |

> [!TIP]
> När du har installerat operativsystemet, du kan använda den [distribution av förutsättningar för Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) att bekräfta att maskinvaran uppfyller alla krav.

## <a name="account-requirements"></a>-Kontokrav
Normalt distribuerar du i development kit internet-anslutning, där du kan ansluta till Microsoft Azure. I så fall måste du konfigurera ett Azure Active Directory (AD Azure). Om du vill distribuera i development kit.

Om din miljö inte är ansluten till internet, eller om du inte vill använda Azure AD, kan du distribuera Azure Stack med hjälp av Active Directory Federation Services (AD FS). I development kit innehåller sin egen AD FS och Active Directory Domain Services-instanser. Om du distribuerar med hjälp av det här alternativet kan behöver du inte konfigurera konton förbereds i förväg.

> [!NOTE]
> Om du distribuerar med hjälp av AD FS-alternativet måste du distribuera om Azure Stack för att växla till Azure AD.

### <a name="azure-active-directory-accounts"></a>Azure Active Directory-konton
För att distribuera Azure Stack med hjälp av en Azure AD-konto, måste du förbereda en Azure AD-konto innan du kör PowerShell-skript för distribution. Detta konto blir den globala administratören för Azure AD-klient. Den används för att etablera och delegera program och tjänstens huvudnamn för alla Azure Stack-tjänster som interagerar med Azure Active Directory och Graph API. Den används också som ägare till standard-providerprenumeration (som du kan ändra senare). Du kan logga in till administratörsportalen för Azure Stack-system med hjälp av det här kontot.

1. Skapa ett Azure AD-konto som är katalogadministratör för minst en Azure AD. Om du redan har ett konto kan använda du det. Annars kan du skapa en kostnadsfritt på [ https://azure.microsoft.com/free/ ](https://azure.microsoft.com/free/) (i Kina, besök <https://go.microsoft.com/fwlink/?LinkID=717821> i stället). Om du planerar att senare [registrera Azure Stack med Azure](asdk-register.md), du måste också ha en prenumeration för den nya konto.
   
    Spara autentiseringsuppgifterna för användning som tjänstadministratör. Det här kontot kan konfigurera och hantera resursmoln, användarkonton, klientplaner för innehavare, kvoter och priser. I portalen kan de skapa webbplatsmoln, privata moln för virtuella datorer, skapa planer och hantera användarprenumerationer.
1. Skapa minst ett test-användarkonto i din Azure AD så att du kan logga in i development Kit som en klient.
   
   | **Azure Active Directory-konto** | **Stöd för?** |
   | --- | --- |
   | Arbets- eller skolkonto konto med giltig prenumeration på offentlig Azure |Ja |
   | Microsoft-konto med giltig prenumeration på offentlig Azure |Ja |
   | Arbets- eller skolkonto konto med giltig prenumeration på China Azure |Ja |
   | Arbets- eller skolkonto konto med giltig US Government Azure-prenumeration |Ja |

Efter distributionen krävs inte behörighet för Azure Active Directory global administratör. Vissa åtgärder kan dock kräva autentiseringsuppgifter för global administratör. Till exempel ett resource provider installer skript eller en ny funktion som kräver en behörighet som ska beviljas. Du kan tillfälligt återställa kontots behörigheter som global administratör eller använda ett separat globalt administratörskonto som äger den *standard providerprenumeration*.

## <a name="network"></a>Nätverk
### <a name="switch"></a>Växel
En tillgänglig port för en växel för kit utvecklingsdator.  

Development kit datorn stöder anslutning till en växelåtkomstport eller segmentport. Inga särskilda funktioner krävs på växeln. Om du använder en segmentport eller om du behöver konfigurera ett VLAN-ID måste du ange VLAN-ID som en parameter för distribution.

### <a name="subnet"></a>Undernät
Anslut inte kit utvecklingsdator till följande undernät:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

De här undernäten är reserverade för interna nätverk i utvecklingsmiljön kit.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Endast IPv4 stöds. Du kan inte skapa IPv6-nätverk.

### <a name="dhcp"></a>DHCP
Kontrollera att det finns en DHCP-server i nätverket som nätverkskortet ansluts till. Om DHCP inte är tillgängligt måste du förbereda ytterligare ett statiskt IPv4-nätverk än det som används av värden. Du måste ange IP-adress och gateway som distributionsparameter.

### <a name="internet-access"></a>Internet-åtkomst
Azure Stack kräver åtkomst till Internet, antingen direkt eller via en transparent proxy. Azure Stack har inte stöd för konfigurationen av en webbproxy för att aktivera åtkomst till Internet. Både värddatorns IP-adress och den nya IP-Adressen som tilldelats AzS-BGPNAT01 (via DCHP eller statisk IP-adress) måste kunna få åtkomst till Internet. Portarna 80 och 443 används under domänerna graph.windows.net och login.microsoftonline.com domäner.


## <a name="next-steps"></a>Nästa steg
[Hämta ASDK distributionspaketet](asdk-download.md)
