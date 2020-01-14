---
title: Felsök anslutning för haveri beredskap i Azure till Azure med Azure Site Recovery
description: Felsök fel och problem när du replikerar virtuella Azure-datorer för haveri beredskap
services: site-recovery
author: carmonmills
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/05/2019
ms.author: carmonm
ms.openlocfilehash: 41b5203c328243c9ef7cc74e6b9771c677a54c7c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933413"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Felsök problem med Azure-till-Azure VM-nätverksanslutningar

I den här artikeln beskrivs vanliga problem som rör nätverks anslutning när du replikerar och återställer virtuella Azure-datorer från en region till en annan region. Mer information om nätverks krav finns i [anslutnings kraven för replikering av virtuella Azure-datorer](azure-to-azure-about-networking.md).

För Site Recovery-replikering till arbete, utgående anslutning till specifika URL: er eller IP-intervall krävs från den virtuella datorn. Om den virtuella datorn finns bakom en brandvägg eller använder regler för nätverkssäkerhetsgrupper (NSG) för att styra utgående anslutningar, kan du står inför ett av de här problemen.

**URL** | **Detaljer**  
--- | ---
*.blob.core.windows.net | Krävs så att data kan skrivas till cache-lagrings kontot i käll regionen från den virtuella datorn. Om du känner till alla cache-lagrings konton för dina virtuella datorer kan du tillåta-lista de angivna lagrings konto webb adresserna (till exempel cache1.blob.core.windows.net och cache2.blob.core.windows.net) i stället för *. blob.core.windows.net
login.microsoftonline.com | Krävs för auktorisering och autentisering till Site Recovery tjänst-URL: er.
*.hypervrecoverymanager.windowsazure.com | Krävs så att kommunikationen mellan Site Recoverys tjänsten kan ske från den virtuella datorn. Du kan använda motsvarande "Site Recovery IP" om brand Väggs-proxyn stöder IP-adresser.
*.servicebus.windows.net | Krävs så att Site Recovery övervakning och diagnostikdata kan skrivas från den virtuella datorn. Du kan använda motsvarande "Site Recovery övervaknings-IP" om brand Väggs-proxyn har stöd för IP-adresser.

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Utgående anslutning för Site Recovery-webbadresser eller IP-intervall (felkod: 151037 eller 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problem 1: det gick inte att registrera den virtuella Azure-datorn med Site Recovery (151195) </br>
- **Möjlig orsak** </br>
  - Det går inte att upprätta en anslutning till Site Recovery slut punkter på grund av ett DNS-matchningsfel.
  - Detta visas oftare vid återskydd när du har redundansväxlat den virtuella datorn men DNS-servern inte kan nås från regionen för haveriberedskap.

- **Lösning**
   - Om du använder anpassad DNS måste du kontrol lera att DNS-servern är tillgänglig från Disaster Recovery-regionen. Kontrollera om du har en anpassad DNS går du till den virtuella datorn > Disaster Recovery-nätverket > DNS-servrar. Försök att öppna DNS-servern från den virtuella datorn. Om den inte är tillgänglig kan du göra den tillgänglig genom att antingen redundansväxla DNS-servern eller skapa en plats mellan DR-nätverk och DNS.

    ![COM-fel](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Site Recovery-konfigurationen misslyckades (151196)

> [!NOTE]
> Om de virtuella datorerna ligger bakom den interna **standard** belastnings Utjämnings tjänsten har den inte åtkomst till O365-IP: er (det vill säga login.microsoftonline.com) som standard. Ändra den till en **grundläggande** typ av belastningsutjämnare eller skapa utgående åtkomst som anges i [artikeln](https://aka.ms/lboutboundrulescli).

- **Möjlig orsak** </br>
  - Att går inte ansluta till Office 365-autentisering och identitet IP4-slutpunkter.

- **Lösning**
  - Azure Site Recovery krävs åtkomst till Office 365-IP-intervall för autentisering.
    Om du använder Azure Network security group (NSG) regler/brandväggsproxy för att styra utgående nätverksanslutning på den virtuella datorn, kontrollerar du att du tillåter kommunikation till IP-intervall för O365. Skapa en [Azure Active Directory (Azure AD) service tag-](../virtual-network/security-overview.md#service-tags) NSG regel för att tillåta åtkomst till alla IP-adresser som motsvarar Azure AD
      - Om nya adresser läggs till i Azure AD i framtiden måste du skapa nya NSG-regler.

### <a name="example-nsg-configuration"></a>Exempel på NSG-konfiguration

Det här exemplet visar hur du konfigurerar NSG-regler för en virtuell dator att replikera.

- Om du använder NSG-regler för att kontrol lera utgående anslutningar använder du reglerna "Tillåt HTTPS utgående" till port: 443 för alla nödvändiga IP-adressintervall.
- Exemplet förutsätter att käll platsen för den virtuella datorn är "USA, östra" och att mål platsen är "Central US".

### <a name="nsg-rules---east-us"></a>NSG-regler – USA, östra

1. Skapa en utgående HTTPS-säkerhetsregel (443) för "Storage. öst" på NSG som visas på skärm bilden nedan.

      ![lagrings tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Skapa en utgående HTTPS (443) säkerhets regel för "AzureActiveDirectory" på NSG som visas på skärm bilden nedan.

      ![AAD-tagg](./media/azure-to-azure-about-networking/aad-tag.png)

3. Skapa regler för utgående HTTPS (443) för de Site Recovery IP-adresser som motsvarar mål platsen:

   **Plats** | **Site Recovery IP-adress** |  **Site Recovery övervakning av IP-adress**
    --- | --- | ---
   USA, centrala | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG-regler – centrala USA

De här reglerna krävs för att replikeringen ska kunna aktive ras från mål regionen till käll regionen efter redundans:

1. Skapa en utgående HTTPS-säkerhets regel (443) för "Storage. Central" på NSG.

2. Skapa en utgående HTTPS (443) säkerhets regel för "AzureActiveDirectory" på NSG.

3. Skapa regler för utgående HTTPS (443) för de Site Recovery IP-adresser som motsvarar käll platsen:

   **Plats** | **Site Recovery IP-adress** |  **Site Recovery övervakning av IP-adress**
    --- | --- | ---
   USA, centrala | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Site Recovery-konfigurationen misslyckades (151197)
- **Möjlig orsak** </br>
  - Att går inte ansluta till Azure Site Recovery-Tjänsteslutpunkter.

- **Lösning**
  - Azure Site Recovery krävde åtkomst till [IP-intervall för Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) beroende på region. Kontrollera att de nödvändiga IP-intervallen är tillgängliga från den virtuella datorn.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problem 4: A2A-replikeringen misslyckades när nätverks trafiken går via en lokal proxyserver (151072)
- **Möjlig orsak** </br>
  - De anpassade proxyinställningarna är ogiltiga och Azure Site Recovery mobilitets tjänst agenten automatiskt identifierade proxyinställningarna från IE


- **Lösning**
  1. Mobilitetstjänstagenten identifierar proxyinställningar från Internet Explorer på Windows och /etc/environment i Linux.
  2. Om du föredrar att bara ange proxy för Azure Site Recovery mobilitets tjänst kan du ange proxyinformation i ProxyInfo. conf på:</br>
     - ``/usr/local/InMage/config/`` på ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` på ***Windows***
  3. ProxyInfo.conf ska ha rätt proxyinställningar i följande INI-format.</br>
                *[proxy]*</br>
                *Adress =http://1.2.3.4*</br>
                *Port = 567*</br>
  4. Azure Site Recovery Mobility Service Agent stöder bara ***oautentiserade proxyservrar***.

### <a name="fix-the-problem"></a>Åtgärda problemet
Om du vill tillåta [nödvändiga URL: er](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) eller de [IP-adressintervall som krävs](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)följer du stegen i [dokumentet om nätverks vägledning](site-recovery-azure-to-azure-networking-guidance.md).


## <a name="next-steps"></a>Nästa steg
[Replikera virtuella Azure-datorer](site-recovery-replicate-azure-to-azure.md)
