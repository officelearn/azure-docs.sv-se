---
title: Felsöka anslutning för Azure till Azure-haveriberedskap med Azure Site Recovery
description: Felsöka anslutningsproblem i azure vm-haveriberedskap
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: d2cc4133e52e7cab812413d23948da6ac2660e77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884876"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Felsöka problem med anslutning till Azure-till-Azure VM-nätverksanslutning

I den här artikeln beskrivs de vanligaste problemen med nätverksanslutning när du replikerar och återställer virtuella Azure-datorer (VM) från en region till en annan region. Mer information om nätverkskrav finns i [anslutningskraven för att replikera virtuella Azure-datorer](azure-to-azure-about-networking.md).

För att site recovery-replikering ska fungera krävs utgående anslutning till specifika webbadresser eller IP-intervall från den virtuella datorn. Om den virtuella datorn ligger bakom en brandvägg eller använder NSG-regler (Network Security Group) för att styra utgående anslutning kan du ställas inför ett av dessa problem.

| URL | Information |
|---|---|
| `*.blob.core.windows.net` | Krävs så att data kan skrivas till cachelagringskontot i källregionen från den virtuella datorn. Om du känner till alla cachelagringskonton för dina virtuella datorer kan du använda en tillåtlista för de specifika url:erna för lagringskonto. Till `cache1.blob.core.windows.net` exempel, `cache2.blob.core.windows.net` och `*.blob.core.windows.net`i stället för . |
| `login.microsoftonline.com` | Krävs för auktorisering och autentisering till url:erna för tjänsten Site Recovery. |
| `*.hypervrecoverymanager.windowsazure.com` | Krävs så att tjänsten Site Recovery-tjänsten kan ske från den virtuella datorn. Du kan använda motsvarande _IP för återställning_ av webbplats om brandväggsproxyn stöder IP-adresser. |
| `*.servicebus.windows.net` | Krävs så att data för övervakning och diagnostik av platsåterställning kan skrivas från den virtuella datorn. Du kan använda motsvarande IP för övervakning av _platsåterställning_ om brandväggsproxyen stöder IP-adresser. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Utgående anslutning för url:er eller IP-intervall för webbplatsåterställning (felkod 151037 eller 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Problem 1: Det gick inte att registrera virtuell Azure-dator med Site Recovery (151195)

#### <a name="possible-cause"></a>Möjlig orsak

Det går inte att upprätta en anslutning till slutpunkter för platsåterställning på grund av ett DNS-lösningsfel (Domain Name System). Det här problemet är vanligare under återskydd när du har misslyckats över den virtuella datorn, men DNS-servern kan inte nås från regionen för haveriberedskap (DR).

#### <a name="resolution"></a>Lösning

Om du använder anpassad DNS kontrollerar du att DNS-servern är tillgänglig från katastrofåterställningsregionen.

Så här kontrollerar du om den virtuella datorn använder en anpassad DNS-inställning:

1. Öppna **virtuella datorer** och välj den virtuella datorn.
1. Navigera till de virtuella **datorernas inställningar** och välj **Nätverk**.
1. I **Virtuellt nätverk/undernät**väljer du länken för att öppna det virtuella nätverkets resurssida.
1. Gå till **Inställningar** och välj **DNS-servrar**.

Försök att komma åt DNS-servern från den virtuella datorn. Om DNS-servern inte är tillgänglig gör du den tillgänglig genom att antingen gå över DNS-servern eller skapa platsen mellan DR-nätverket och DNS.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-fel":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Konfigurationen för återställning av platsen misslyckades (151196)

> [!NOTE]
> Om de virtuella datorerna ligger bakom en **intern** standardbelastningsutjämnare, som standard, skulle `login.microsoftonline.com`den inte ha tillgång till Office 365-IPs, till exempel . Ändra den antingen till **grundläggande** intern belastningsutjämnad typ eller skapa utgående åtkomst som nämns i artikeln [Konfigurera belastningsutjämning och utgående regler i Standard belastningsutjämnare med Azure CLI](/azure/load-balancer/configure-load-balancer-outbound-cli).

#### <a name="possible-cause"></a>Möjlig orsak

Det går inte att upprätta en anslutning till OFFICE 365-autentisering och identitets-IP4-slutpunkter.

#### <a name="resolution"></a>Lösning

- Azure Site Recovery kräver åtkomst till Office 365 IP-intervall för autentisering.
- Om du använder NSG-regler (Azure Network Security Group) för att styra utgående nätverksanslutning på den virtuella datorn, se till att du tillåter kommunikation till Office 365 IP-intervall. Skapa en [Azure Active Directory -tjänsttaggbaserad](/azure/virtual-network/security-overview#service-tags) NSG-regel (Azure Active Directory) som ger åtkomst till alla IP-adresser som motsvarar Azure AD.
- Om nya adresser läggs till i Azure AD i framtiden måste du skapa nya NSG-regler.

### <a name="example-nsg-configuration"></a>Exempel på NSG-konfiguration

Det här exemplet visar hur du konfigurerar NSG-regler för en virtuell dator som ska replikeras.

- Om du använder NSG-regler för att styra utgående anslutning använder du **Tillåt utgående HTTPS-regler** till port 443 för alla nödvändiga IP-adressintervall.
- Exemplet förutsätter att vm-källplatsen är **östra USA** och målplatsen är **central USA**.

#### <a name="nsg-rules---east-us"></a>NSG regler - Östra USA

1. Skapa en HTTPS-utgående säkerhetsregel för NSG som visas i följande skärmbild. I det här exemplet används **tjänstmärket Destination**: _Storage.EastUS_ och **Målportintervall:** _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="lagring-tagg":::

1. Skapa en HTTPS-utgående säkerhetsregel för NSG som visas i följande skärmbild. I det här exemplet används **måltjänsttaggen** _: AzureActiveDirectory-_ och **Målportintervall:** _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="aad-tagg":::

1. Skapa utgående HTTPS-port 443-regler för ip-adresser för platsåterställning som motsvarar målplatsen:

   | Location | IP-adress för återställning av webbplats | IP-adress för övervakning av webbplatsåterställning |
   | --- | --- | --- |
   | USA, centrala | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>NSG regler - Centrala USA

I det här exemplet krävs dessa NSG-regler så att replikering kan aktiveras från målområdet till källregionen efter redundansen:

1. Skapa en https-utgående säkerhetsregel för _Storage.CentralUS:_

   - **Mål service tag**: _Storage.CentralUS_
   - **Destinationshamnsområden:** _443_

1. Skapa en HTTPS-utgående säkerhetsregel för _AzureActiveDirectory_.

   - **Måltjänsttagg:** _AzureActiveDirectory_
   - **Destinationshamnsområden:** _443_

1. Skapa utgående HTTPS-port 443-regler för IPs för platsåterställning som motsvarar källplatsen:

   | Location | IP-adress för återställning av webbplats | IP-adress för övervakning av webbplatsåterställning |
   | --- | --- | --- |
   | USA, östra | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Konfigurationen för återställning av platsen misslyckades (151197)

#### <a name="possible-cause"></a>Möjlig orsak

Det går inte att upprätta en anslutning till slutpunkter för Azure Site Recovery-tjänsten.

#### <a name="resolution"></a>Lösning

Azure Site Recovery krävde åtkomst till [IP-intervall för Site Recovery](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) beroende på region. Kontrollera att nödvändiga IP-intervall är tillgängliga från den virtuella datorn.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problem 4: Azure-to-Azure-replikering misslyckades när nätverkstrafiken går igenom lokal proxyserver (151072)

#### <a name="possible-cause"></a>Möjlig orsak

De anpassade proxyinställningarna är ogiltiga och azure Site Recovery Mobility-serviceagenten har inte automatiskt de proxyinställningarna från Internet Explorer (IE).

#### <a name="resolution"></a>Lösning

1. Mobility-serviceagenten identifierar proxyinställningarna från IE på Windows och `/etc/environment` på Linux.
1. Om du föredrar att ange proxy endast för Azure Site Recovery Mobility-tjänsten kan du ange proxyinformation i _ProxyInfo.conf_ som finns på:

   - **Linux**:`/usr/local/InMage/config/`
   - **Windows**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo.conf_ bör ha proxyinställningarna i följande _INI-format:_

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure Site Recovery Mobility-tjänstagenten stöder endast **oautentiserade proxyservrar**.

### <a name="fix-the-problem"></a>Åtgärda problemet

Om du vill tillåta [nödvändiga webbadresser](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) eller [de IP-intervall som krävs](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)följer du stegen i [vägledningsdokumentet](site-recovery-azure-to-azure-networking-guidance.md)för nätverk .

## <a name="next-steps"></a>Nästa steg

[Replikera virtuella Azure-datorer till en annan Azure-region](azure-to-azure-how-to-enable-replication.md)
