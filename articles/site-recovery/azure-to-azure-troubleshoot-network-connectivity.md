---
title: Felsöka anslutning för Azure till Azure-haveriberedskap med Azure Site Recovery
description: Felsöka anslutningsproblem i azure vm-haveriberedskap
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 08/05/2019
ms.openlocfilehash: b082e1aca094dcb335a7268e4c116376d756fd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292014"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Felsöka problem med anslutning till Azure-till-Azure VM-nätverksanslutning

I den här artikeln beskrivs de vanligaste problemen med nätverksanslutning när du replikerar och återställer virtuella Azure-datorer från en region till en annan region. Mer information om nätverkskrav finns i [anslutningskraven för att replikera virtuella Azure-datorer](azure-to-azure-about-networking.md).

För att site recovery-replikering ska fungera krävs utgående anslutning till specifika webbadresser eller IP-intervall från den virtuella datorn. Om den virtuella datorn ligger bakom en brandvägg eller använder NSG-regler (Network Security Group) för att styra utgående anslutning kan du ställas inför ett av dessa problem.

**URL** | **Detaljer**  
--- | ---
*.blob.core.windows.net | Krävs så att data kan skrivas till cachelagringskontot i källregionen från den virtuella datorn. Om du känner till alla cachelagringskonton för dina virtuella datorer kan du tillåta att du listar de specifika url:erna för lagringskonto (till exempel cache1.blob.core.windows.net och cache2.blob.core.windows.net) i stället för *.blob.core.windows.net
login.microsoftonline.com | Krävs för auktorisering och autentisering till url:erna för tjänsten Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Krävs så att tjänsten Site Recovery-tjänsten kan ske från den virtuella datorn. Du kan använda motsvarande "Site Recovery IP" om din brandvägg proxy stöder IP-adresser.
*.servicebus.windows.net | Krävs så att data för övervakning och diagnostik av platsåterställning kan skrivas från den virtuella datorn. Du kan använda motsvarande IP för övervakning av platsåterställning om brandväggsproxyen stöder IP-adresser.

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Utgående anslutning för url:er eller IP-intervall för webbplatsåterställning (felkod 151037 eller 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problem 1: Det gick inte att registrera virtuell Azure-dator med Site Recovery (151195) </br>
- **Möjlig orsak** </br>
  - Det går inte att upprätta anslutningen till slutpunkter för platsåterställning på grund av DNS-lösningsfel.
  - Detta visas oftare vid återskydd när du har redundansväxlat den virtuella datorn men DNS-servern inte kan nås från regionen för haveriberedskap.

- **Upplösning**
   - Om du använder anpassad DNS kontrollerar du att DNS-servern är tillgänglig från regionen Haveriberedskap. För att kontrollera om du har en anpassad DNS gå till VM> Disaster Recovery-nätverket> DNS-servrar. Försök att öppna DNS-servern från den virtuella datorn. Om den inte är tillgänglig gör du den tillgänglig genom att antingen misslyckas över DNS-servern eller skapa platsen mellan DR-nätverket och DNS.

    ![com-fel](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Konfigurationen för återställning av platsen misslyckades (151196)

> [!NOTE]
> Om de virtuella datorerna ligger bakom **Standard** intern belastningsutjämnare, skulle det inte ha tillgång till O365 IP-adresser (det vill login.microsoftonline.com) som standard. Ändra den antingen till **grundläggande** intern belastningsutjämnad typ eller skapa utgående åtkomst som nämns i [artikeln](https://aka.ms/lboutboundrulescli).

- **Möjlig orsak** </br>
  - Det går inte att upprätta anslutningen till Office 365-autentiserings- och identitets-IP4-slutpunkter.

- **Upplösning**
  - Azure Site Recovery krävs åtkomst till Office 365 IPs-intervall för autentisering.
    Om du använder NSG-regler (Azure Network Security Group) för att styra utgående nätverksanslutning på den virtuella datorn, se till att du tillåter kommunikation till O365 IPranges. Skapa en [Azure Active Directory-tjänsttaggbaserad](../virtual-network/security-overview.md#service-tags) NSG-regel (Azure Active Directory) för att tillåta åtkomst till alla IP-adresser som motsvarar Azure AD
      - Om nya adresser läggs till i Azure AD i framtiden måste du skapa nya NSG-regler.

### <a name="example-nsg-configuration"></a>Exempel på NSG-konfiguration

Det här exemplet visar hur du konfigurerar NSG-regler för en virtuell dator som ska replikeras.

- Om du använder NSG-regler för att styra utgående anslutning använder du regler för "Tillåt utgående HTTPS" till port:443 för alla nödvändiga IP-adressintervall.
- Exemplet förutsätter att vm-källplatsen är "Östra USA" och målplatsen är "Central US".

### <a name="nsg-rules---east-us"></a>NSG regler - Östra USA

1. Skapa en utgående HTTPS -säkerhetsregel (443) för "Storage.EastUS" på NSG som visas i skärmbilden nedan.

      ![lagring-tagg](./media/azure-to-azure-about-networking/storage-tag.png)

2. Skapa en utgående HTTPS-säkerhetsregel (443) för "AzureActiveDirectory" på NSG som visas i skärmbilden nedan.

      ![aad-tagg](./media/azure-to-azure-about-networking/aad-tag.png)

3. Skapa utgående HTTPS-regler (443) för ip-adresser för platsåterställning som motsvarar målplatsen:

   **Location** | **IP-adress för återställning av webbplats** |  **IP-adress för övervakning av webbplatsåterställning**
    --- | --- | ---
   USA, centrala | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG regler - Centrala USA

Dessa regler krävs så att replikering kan aktiveras från målområdet till källregionen efter redundansen:

1. Skapa en utgående HTTPS-säkerhetsregel (443) för "Storage.CentralUS" på NSG.

2. Skapa en utgående HTTPS-säkerhet (443) för "AzureActiveDirectory" på NSG.

3. Skapa utgående HTTPS-regler (443) för de IPs för platsåterställning som motsvarar källplatsen:

   **Location** | **IP-adress för återställning av webbplats** |  **IP-adress för övervakning av webbplatsåterställning**
    --- | --- | ---
   USA, centrala | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Konfigurationen för återställning av platsen misslyckades (151197)
- **Möjlig orsak** </br>
  - Det går inte att upprätta anslutningen till azure site recovery-tjänstslutpunkter.

- **Upplösning**
  - Azure Site Recovery krävde åtkomst till [IP-intervall för Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-using-service-tags) beroende på region. Kontrollera att de nödvändiga IP-intervallen är tillgängliga från den virtuella datorn.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problem 4: A2A-replikering misslyckades när nätverkstrafiken gick igenom den lokala proxyservern (151072)
- **Möjlig orsak** </br>
  - De anpassade proxyinställningarna är ogiltiga och Azure Site Recovery Mobility Service-agenten upptäckte inte proxyinställningarna automatiskt från IE


- **Upplösning**
  1. Mobility Service agent upptäcker proxyinställningar från IE på Windows och / etc / miljö på Linux.
  2. Om du föredrar att ange proxy endast för Azure Site Recovery Mobility Service kan du ange proxyinformation i ProxyInfo.conf som finns på:</br>
     - ``/usr/local/InMage/config/``på ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``på ***Windows***
  3. ProxyInfo.conf ska ha rätt proxyinställningar i följande INI-format.</br>
                *-Jag har inte tid med det här.*</br>
                *Adress=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Azure Site Recovery Mobility Service-agenten stöder endast ***icke-autentiserade proxyservrar***.

### <a name="fix-the-problem"></a>Åtgärda problemet
Om du vill tillåta [nödvändiga webbadresser](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) eller [de IP-intervall som krävs](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)följer du stegen i [vägledningsdokumentet](site-recovery-azure-to-azure-networking-guidance.md)för nätverk .


## <a name="next-steps"></a>Nästa steg
[Replikera virtuella Azure-datorer](site-recovery-replicate-azure-to-azure.md)
