---
title: "Kraven för distribution av Azure-stacken Development Kit | Microsoft Docs"
description: "Visa kraven på miljö och maskinvara för Azure-stacken Development Kit (moln operator)."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 32a21d9b-ee42-417d-8e54-98a7f90f7311
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2017
ms.author: jeffgilb
ms.openlocfilehash: 8a0d23e14ef50034d5f9595cf154c3513a09c464
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="azure-stack-deployment-prerequisites"></a>Distributionskrav för Azure Stack

*Gäller för: Azure stacken Development Kit*

Innan du distribuerar [Azure Stack Development Kit](azure-stack-poc.md), kontrollera att datorn uppfyller följande krav:


## <a name="hardware"></a>Maskinvara
| Komponent | Minimum | Rekommenderas |
| --- | --- | --- |
| Diskenheter: operativsystem |1 OS-disk med minst 200 GB tillgängligt för systempartitionering (SSD och HDD) |1 OS-disk med minst 200 GB tillgängligt för systempartitionering (SSD och HDD) |
| Diskenheter: Allmänt development kit data * |4 diskar. Varje disk innehåller minst 140 GB kapacitet (SSD och HDD). Alla tillgängliga diskar används. |4 diskar. Varje disk innehåller minst 250 GB kapacitet (SSD eller HDD). Alla tillgängliga diskar används. |
| Beräkna: CPU |Dubbla uttag: 12 fysiska kärnor (totalt) |Dubbla uttag: 16 fysiska kärnor (totalt) |
| Beräkna: minne |96 GB RAM-minne |128 GB RAM-minne (det är minst som stöd för PaaS-resursprovidrar.)|
| Beräkna: BIOS |Hyper-V aktiverat (med stöd för SLAT) |Hyper-V aktiverat (med stöd för SLAT) |
| Nätverk: NIC |Windows Server 2012 R2-certifiering krävs för NIC, inga specialfunktioner krävs |Windows Server 2012 R2-certifiering krävs för NIC, inga specialfunktioner krävs |
| HW-logotypcertifiering |[Certifierade för Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certifierade för Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

\*Du behöver fler än det rekommenderade kapacitet om du planerar att lägga till många av de [marketplace-objekt](azure-stack-download-azure-marketplace-item.md) från Azure.

**Konfiguration av enhet:** alla dataenheter måste vara av samma typ (alla SAS, alla SATA eller alla NVMe) och kapacitet. Om SAS-hårddiskar används måste diskenheter kopplas via en enskild sökväg (inga MPIO, stöd för flera sökvägar, tillhandahålls).

**HBA-konfigurationsalternativ**

* (Rekommenderas) Enkel HBA
* RAID HBA – Adaptern måste konfigureras i genomströmningsläge (”pass through”)
* RAID HBA – Diskar ska konfigureras som enskild disk, RAID-0

**Skriv kombinationer stöds bus och media**

* SATA-hårddisk
* SAS-hårddisk
* RAID-hårddisk
* RAID-SSD (om medietypen är ospecificerad/okänd\*)
* SATA SSD + SATA-hårddisk
* SAS SSD + SAS-hårddisk
* NVMe

\*RAID-styrenheter utan direkt funktionen kan inte identifiera medietyp. Sådana styrenheter markerar både HDD och SSD som ospecificerade. I så fall ska SSD användas som permanent lagring i stället för cachelagringsenheter. Därför kan du distribuera development kit på dessa SSD.

**Exempel på HBA**: LSI 9207-8i, LSI-9300-8i eller LSI-9265-8i i genomströmningsläge (pass-through)

Det finns tillgängliga exempel på OEM-konfigurationer.

## <a name="operating-system"></a>Operativsystem
|  | **Krav** |
| --- | --- |
| **OS-Version** |Windows Server 2012 R2 eller senare. Operativsystemets version inte är driftskritisk innan distributionen startar, som du ska starta värddatorn till den virtuella Hårddisken som ingår i Azure Stack-installationen. Operativsystem och alla uppdateringar som krävs är redan integrerade i avbildningen. Använd inte en för att aktivera alla Windows Server-instanser som används i development kit. |

## <a name="deployment-requirements-check-tool"></a>Krav för distribution Kontrollera verktyget
När du har installerat operativsystemet, kan du använda den [distribution av förutsättningar för Azure-stacken](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) att bekräfta att maskinvaran uppfyller alla krav.

## <a name="account-requirements"></a>Kontokrav
Normalt distribuerar du development kit med anslutning till internet, där du kan ansluta till Microsoft Azure. I det här fallet måste du konfigurera ett konto för Azure Active Directory (Azure AD) för att distribuera development kit.

Om din miljö inte är ansluten till internet, eller om du inte vill använda Azure AD, kan du distribuera Azure-stacken genom att använda Active Directory Federation Services (AD FS). I development kit ingår instanser AD FS och Active Directory Domain Services. Om du distribuerar med hjälp av det här alternativet om behöver du inte konfigurera konton i förväg.

>[!NOTE]
Om du distribuerar med hjälp av AD FS-alternativet måste du omdistribuera Azure stackutrymme för att växla till Azure AD.

### <a name="azure-active-directory-accounts"></a>Azure Active Directory-konton
För att distribuera Azure-stacken genom att använda en Azure AD-konto, måste du förbereda en Azure AD-kontot innan du kör PowerShell-skript för distribution. Det här kontot blir Global administratör för Azure AD-klient. Den används för att etablera och delegera program och tjänstens huvudnamn för alla Azure Stack-tjänster som samverkar med Azure Active Directory och Graph API. Det används också som ägare till prenumerationen för standard-providern (som du kan ändra senare). Du kan logga in till Azure-stacken systemets administratörsportal genom att använda det här kontot.

1. Skapa ett Azure AD-konto som är directory-administratör för minst en Azure AD. Om du redan har ett konto kan använda du det. Annars kan du skapa ett kostnadsfritt på [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (i Kina, besök <http://go.microsoft.com/fwlink/?LinkID=717821> i stället). Om du planerar att senare [registrera Azure stacken med Azure](azure-stack-register.md), du måste ha en prenumeration i den nya konto.
   
    Spara autentiseringsuppgifterna för användning i steg 6 i [distribuera development kit](azure-stack-run-powershell-script.md#deploy-the-development-kit). Med det här *tjänstadministratörkontot* kan du konfigurera och hantera resursmoln, användarkonton, klientplaner för innehavare, kvoter och priser. I portalen kan de skapa webbplatsmoln, privata moln för virtuella datorer, skapa planer och hantera användarprenumerationer.
2. [Skapa](azure-stack-add-new-user-aad.md) minst ett konto så att du kan logga in i development Kit som en klient.
   
   | **Azure Active Directory-konto** | **Stöds?** |
   | --- | --- |
   | Arbets- eller skolkonto konto med giltig offentlig Azure-prenumeration |Ja |
   | Microsoft-konto med giltig prenumeration på offentlig Azure |Ja |
   | Arbets- eller skolkonto konto med giltig Kina Azure-prenumeration |Ja |
   | Arbets- eller skolkonto konto med giltig US Government Azure-prenumeration |Ja |

## <a name="network"></a>Nätverk
### <a name="switch"></a>Växel
En tillgänglig port på en växel för den development kit datorn.  

Development kit datorn stöder anslutning till en åtkomst-växelport eller trunk port. Inga särskilda funktioner krävs på växeln. Om du använder en segmentport eller om du behöver konfigurera ett VLAN-ID måste du ange VLAN-ID som en parameter för distribution. Du kan se exemplen i den [lista över distributionsparametrarna](azure-stack-run-powershell-script.md).

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
Kontrollera att det finns en DHCP-server i nätverket som nätverkskortet ansluts till. Om DHCP inte är tillgängligt måste du förbereda ytterligare ett statiskt IPv4-nätverk än det som används av värden. Du måste ange IP-adress och gateway som distributionsparameter. Du kan se exemplen i den [lista över distributionsparametrarna](azure-stack-run-powershell-script.md).

### <a name="internet-access"></a>Internet-åtkomst
Azure-stacken kräver åtkomst till Internet, antingen direkt eller via en transparent proxy. Azure-stacken stöder inte konfigurationen av en webbproxy för Internet-åtkomst. Både värd-IP och nya IP-Adressen som tilldelats MAS-BGPNAT01 (med DHCP eller statiska IP-) måste kunna få åtkomst till Internet. Portarna 80 och 443 används under graph.windows.net och login.microsoftonline.com domäner.

## <a name="telemetry"></a>Telemetri

Telemetri hjälper oss att formar framtida versioner av Azure-stacken. Det gör oss snabbt svara på feedback, innehåller nya funktioner och förbättra kvaliteten. Microsoft Azure-stacken innehåller Windows Server 2016 och SQL Server 2014. Ingen av dessa produkter har ändrats från standardinställningarna och båda beskrivs av sekretesspolicyn för Microsoft Enterprise. Azure stacken innehåller också programvara med öppen källkod som inte har ändrats för att skicka telemetri till Microsoft. Här följer några exempel på Azure-stacken telemetridata:

- information om distribution
- När en avisering öppnas och stängs
- antalet nätverksresurser

För att stödja telemetri dataflöde, öppnas port 443 (HTTPS) i nätverket. Klientslutpunkten är https://vortex-win.data.microsoft.com.

Om du inte vill ange telemetri för Azure-stacken kan inaktivera du den på development kit värden och infrastruktur för virtuella datorer som beskrivs nedan.

### <a name="turn-off-telemetry-on-the-development-kit-host-optional"></a>Inaktivera telemetri för development kit värden (valfritt)

>[!NOTE]
Om du vill inaktivera telemetri för development kit värden måste du göra det innan du kör skriptet för distribution.

Innan du [kör skriptet asdk installer.ps1]() för att distribuera development kit värden, starta i CloudBuilder.vhdx och kör följande skript i ett upphöjt PowerShell-fönster:
```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host 
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0'  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

Ange **AllowTelemetry** till 0 inaktiveras telemetri för både Windows- och Azure Stack-distribution. Endast kritiska säkerhetshändelser från operativsystemet skickas. Inställningen styr Windows telemetri över alla värdar och infrastruktur för virtuella datorer och används igen till nya noder/virtuella datorer när skalbar åtgärder vidtas.


### <a name="turn-off-telemetry-on-the-infrastructure-virtual-machines-optional"></a>Inaktivera telemetri för infrastruktur för virtuella datorer (valfritt)

När distributionen är klar kör du följande skript i ett upphöjt PowerShell-fönster (som AzureStack\AzureStackAdmin användare) på värden development kit:

```powershell
$AzSVMs= get-vm |  where {$_.Name -like "AzS-*"}
### Show current AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
### Set & Get updated AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {Set-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name "AllowTelemetry" -Value '0'}
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
```

Om du vill konfigurera SQL Server-telemetri finns [hur du konfigurerar SQL Server 2016](https://support.microsoft.com/en-us/help/3153756/how-to-configure-sql-server-2016-to-send-feedback-to-microsoft).

### <a name="usage-reporting"></a>Användningsrapportering

Via registrering, Azure Stack även är konfigurerat med vanlig användningsinformation till Azure. Användningsrapport styrs oberoende från telemetri. Du kan inaktivera användning reporting när [registrera](azure-stack-register.md) med hjälp av skript på Github. Anger den **$reportUsage** parameter till **$false**.

Användningsdata är formaterad enligt anvisningarna i den [rapporten Azure Stack användningsdata till Azure](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-usage-reporting). Azure-stacken Development Kit användare är faktiskt inte debiteras. Den här funktionen ingår i development kit så att du kan testa hur användningsrapportering fungerar. 


## <a name="next-steps"></a>Nästa steg
[Hämta Azure Stack development kit distributionspaketet](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Distribuera Azure-stacken development kit](azure-stack-run-powershell-script.md)

