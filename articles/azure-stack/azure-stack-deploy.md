<properties
    pageTitle="Innan du distribuerar Azure Stack POC | Microsoft Azure"
    description="Visa miljön och maskinvarukrav för Azure Stack POC (tjänstadministratör)."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/29/2016"
    ms.author="erikje"/>


# Distributionskrav för Azure Stack

Innan du distribuerar Azure Stack POC ([Proof of Concept](azure-stack-poc.md)) ska du kontrollera att datorn uppfyller följande krav.
Dessa krav gäller enbart för Azure Stack POC endast och kan ändras för framtida versioner.

Det kan även vara praktiskt att se den här självstudievideon om distribution:

[AZURE.VIDEO microsoft-azure-stack-tp1-poc-deployment-tutorial]

## Maskinvara

| Komponent | Minimum  | Rekommenderas |
|---|---|---|
| Diskenheter: operativsystem | 1 OS-disk med minst 200 GB tillgängligt för systempartitionering (SSD och HDD) | 1 OS-disk med minst 200 GB tillgängligt för systempartitionering (SSD och HDD) |
| Diskenheter: allmänna Azure Stack POC-data | 4 diskar. Varje disk innehåller minst 140 GB kapacitet (SSD och HDD). Alla tillgängliga diskar används. | 4 diskar. Varje disk innehåller minst 250 GB kapacitet (SSD eller HDD). Alla tillgängliga diskar används.|
| Beräkna: CPU | Dubbla uttag: 12 fysiska kärnor (totalt)  | Dubbla uttag: 16 fysiska kärnor (totalt) |
| Beräkna: minne | 96 GB RAM-minne  | 128 GB RAM-minne |
| Beräkna: BIOS | Hyper-V aktiverat (med stöd för SLAT)  | Hyper-V aktiverat (med stöd för SLAT) |
| Nätverk: NIC | Windows Server 2012 R2-certifiering krävs för NIC, inga specialfunktioner krävs | Windows Server 2012 R2-certifiering krävs för NIC, inga specialfunktioner krävs |
| HW-logotypcertifiering | [Certifierat för Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certifierat för Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0)|

Du kan använda [Deployment Checker for Azure Stack Technical Preview 1](https://gallery.technet.microsoft.com/Deployment-Checker-for-76d824e1) (Distributionskontrollen för Azure Stack Technical Preview 1)för att bekräfta dina krav.

**Konfiguration av datadiskenhet:** Alla dataenheter måste vara av samma typ (alla ska vara SAS eller SATA) och ha samma kapacitet. Om SAS-hårddiskar används måste diskenheter kopplas via en enskild sökväg (inga MPIO, stöd för flera sökvägar, tillhandahålls).

**HBA-konfigurationsalternativ**
 
- (Rekommenderas) Enkel HBA
- RAID HBA – Adaptern måste konfigureras i genomströmningsläge (”pass through”)
- RAID HBA – Diskar ska konfigureras som enskild disk, RAID-0

**Kombinationer av buss- och medietyper som stöds**

-   SATA-hårddisk

-   SAS-hårddisk

-   RAID-hårddisk

-   RAID-SSD (om medietypen är ospecificerad/okänd\*)

-   SATA SSD + SATA-hårddisk

-   SAS SSD + SAS-hårddisk

\* RAID-styrenheter utan genomströmningskapacitet kan inte identifiera medietyp. Sådana styrenheter markerar både HDD och SSD som ospecificerade. I så fall ska SSD användas som permanent lagring i stället för cachelagringsenheter. Därför kan du distribuera Microsoft Azure Stack POC på dessa SSD-enheter.

**Exempel på HBA**: LSI 9207-8i, LSI-9300-8i eller LSI-9265-8i i genomströmningsläge (pass-through)

Det finns tillgängliga exempel på OEM-konfigurationer.




## Operativsystem

| | **Krav**  |
|---|---|
| **OS-version** | Windows Server 2016 Datacenter Edition **Technical Preview 4** med de senaste uppdateringarna installerade. En WindowsServer2016Datacenter.vhdx ingår i paketet som hämtas. Du kan starta i denna VHDX och sedan använda den som grundläggande operativsystem för distribution av Azure Stack POC.|
| **Installationsmetod** | Ren installation. Du kan använda WindowsServer2016Datacenter.vhdx i distributionspaketet för att snabbt installera operativsystemet på datorn med Azure Stack POC. |
| **Har domänen anslutits?** | Nej. |


## Microsoft Azure Active Directory-konton

1. Skapa ett Azure AD-konto som är katalogadministratör för minst en Azure Active Directory. Om du redan har ett konto kan använda du det. Annars kan du skapa ett konto utan extra kostnad på  [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (i Kina går du till <http://go.microsoft.com/fwlink/?LinkID=717821> istället.)

    Spara autentiseringsuppgifterna så att du kan använda dem i steg 6 i [Kör PowerShell-skriptet för distribution](azure-stack-run-powershell-script.md#run-the-powershell-deployment-script). Med det här *tjänstadministratörkontot* kan du konfigurera och hantera resursmoln, användarkonton, klientplaner för innehavare, kvoter och priser. I portalen kan de skapa webbplatsmoln, privata moln för virtuella datorer, skapa planer och hantera användarprenumerationer.

2. [Skapa](azure-stack-add-new-user-aad.md) minst ett konto så att du kan logga in på Azure Stack POC som klient.

  	| **Azure Active Directory-konto**  | **Stöds?** |
  	|---|---| 
  	| Organisations-ID med giltig prenumeration på offentlig Azure  | Ja |
  	| Microsoft-konto med giltig prenumeration på offentlig Azure  | Ja |
  	| Organisations-ID med giltig prenumeration på China Azure  | Ja |
  	| Organisations-ID med giltig prenumeration på US Government Azure  | Nej |

>[AZURE.NOTE] Azure Stack POC stöder endast Azure Active Directory-autentisering.


## Nätverk

### Växel

En tillgänglig port för en växel för POC-datorn.  

Azure Stack POC-datorn stöder anslutning till en växelåtkomstport eller segmentport. Inga särskilda funktioner krävs på växeln. Om du använder en segmentport eller om du behöver konfigurera ett VLAN-ID måste du ange VLAN-ID som en parameter för distribution. Några exempel:

    DeployAzureStack.ps1 –Verbose –PublicVLan 305

När du anger den här parametern anges VLAN-ID endast för värden och NATVM.

### Undernät

Anslut inte POC-datorn till undernäten 192.168.200.0/24, 192.168.100.0/24 eller 192.168.133.0/24. Dessa är reserverade för interna nätverk i Microsoft Azure Stack POC-miljön.

### IPv4/IPv6

Endast IPv4 stöds. Du kan inte skapa IPv6-nätverk.

### DHCP

Kontrollera att det finns en DHCP-server i nätverket som nätverkskortet ansluts till. Om DHCP inte är tillgängligt måste du förbereda ytterligare ett statiskt IPv4-nätverk än det som används av värden. Du måste ange IP-adress och gateway som distributionsparameter. Några exempel:

    DeployAzureStack.ps1 -Verbose -NATVMStaticIP 10.10.10.10/24 -NATVMStaticGateway 10.10.10.1

### Internet-åtkomst

Kontrollera att nätverkskortet kan ansluta till Internet. Både värddatorns IP-adress och den nya IP-adressen som är tilldelad till NATVM (via DCHP eller statisk IP) måste ha åtkomst till Internet. Portarna 80 och 443 används under domänerna graph.windows.net och login.windows.net.

### Proxy

Om en proxyserver krävs i din miljö ska du ange proxyserveradressen och porten som distributionsparameter. Några exempel:

    DeployAzureStack.ps1 -Verbose -ProxyServer 172.11.1.1:8080

Azure Stack POC stöder inte proxyautentisering. 

### Telemetri

Port 443 (HTTPS) måste vara öppet för nätverket. Klientslutpunkten är https://vortex-win.data.microsoft.com.


## Nästa steg

[Hämta Azure Stack POC-distributionspaketet](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Distribuera Azure Stack POC](azure-stack-run-powershell-script.md)



<!--HONumber=Sep16_HO3-->


