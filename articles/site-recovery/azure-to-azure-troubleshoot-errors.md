---
title: Felsöka Azure VM-replikering i Azure Site Recovery
description: Felsöka fel vid replikerande av virtuella Azure-datorer för haveriberedskap.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 0882eaa8b54966c7a804cf78a3928771b238e056
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885012"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Felsöka azure-till-Azure VM-replikeringsfel

I den här artikeln beskrivs felsÃ¶kning av vanliga fel i Azure Site Recovery under replikering och återställning av virtuella Azure-datorer (VMs) från en region till en annan. Mer information om konfigurationer som stöds finns i [supportmatrisen för replikering av virtuella Azure-datorer](azure-to-azure-support-matrix.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problem med Azure-resurskvoter (felkod 150097)

Kontrollera att din prenumeration är aktiverad för att skapa virtuella Azure-datorer i målregionen som du planerar att använda som din REGION för haveriberedskap (DR). Din prenumeration behöver tillräcklig kvot för att skapa virtuella datorer av de nödvändiga storlekarna. Som standard väljer Site Recovery en mål-VM-storlek som är samma som källan VM-storlek. Om matchningsstorleken inte är tillgänglig väljer Site Recovery automatiskt den närmaste tillgängliga storleken.

Om det inte finns någon storlek som stöder konfigurationen av käll-VM visas följande meddelande:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Möjliga orsaker

- Ditt prenumerations-ID är inte aktiverat för att skapa virtuella datorer på målområdets plats.
- Ditt prenumerations-ID är inte aktiverat, eller inte har tillräcklig kvot, för att skapa specifika vm-storlekar på målområdets plats.
- Det finns ingen lämplig mål-VM-storlek som matchar källdatorns nätverkskort (NIC) (2), för prenumerations-ID:t på målområdesplatsen.

### <a name="fix-the-problem"></a>Åtgärda problemet

Kontakta [Azure-faktureringssupport](/azure/azure-portal/supportability/resource-manager-core-quotas-request) för att göra det möjligt för din prenumeration att skapa virtuella datorer med de storlekar som krävs på målplatsen. Försök sedan igen den misslyckade åtgärden.

Om målplatsen har ett kapacitetsbegränsning inaktiverar du replikering till den platsen. Aktivera sedan replikering till en annan plats där din prenumeration har tillräcklig kvot för att skapa virtuella datorer med de storlekar som krävs.

## <a name="trusted-root-certificates-error-code-151066"></a>Betrodda rotcertifikat (felkod 151066)

Om inte alla de senaste betrodda rotcertifikaten finns på den virtuella datorn kan det hända att replikeringen för platsåterställning misslyckas. Autentisering och auktorisering av tjänstanrop för platsåterställning från den virtuella datorn misslyckas utan dessa certifikat.

Om aktivera replikeringsjobbet misslyckas visas följande meddelande:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Möjlig orsak

De betrodda rotcertifikat som krävs för auktorisering och autentisering finns inte på den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

#### <a name="windows"></a>Windows

För en virtuell dator som kör Operativsystemet Windows installerar du de senaste Windows-uppdateringarna så att alla betrodda rotcertifikat finns på den virtuella datorn. Följ den typiska hanterings- eller certifikatuppdateringsprocessen för Windows update i organisationen för att hämta de senaste rotcertifikaten och den uppdaterade listan över återkallade certifikat på de virtuella datorerna.

- Om du är i en frånkopplad miljö följer du standardprocessen för Windows-uppdateringen i organisationen för att hämta certifikaten.
- Om de nödvändiga certifikaten inte finns på den virtuella datorn misslyckas anropen till tjänsten Site Recovery av säkerhetsskäl.

Om du vill kontrollera att `login.microsoftonline.com` problemet är löst går du till från en webbläsare i den virtuella datorn.

Mer information finns i [Konfigurera betrodda rötter och otillåtna certifikat](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11)).

#### <a name="linux"></a>Linux

Följ vägledningen från distributören av din Linux-operativsystemversion för att få de senaste betrodda rotcertifikaten och den senaste listan över återkallade certifikat på den virtuella datorn.

Eftersom SUSE Linux använder symboliska länkar, eller symlänkar, för att underhålla en certifikatlista gör du så här:

1. Logga in som **rotanvändare.** Hash-symbolen`#`( ) är standardkommandot.

1. Om du vill ändra katalogen kör du det här kommandot:

   `cd /etc/ssl/certs`

1. Kontrollera om Symantec-rotcertifikatutfärdaren finns:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Om Symantec-rotcertifikatutfärdaren inte hittas kör du följande kommando för att hämta filen. Sök efter eventuella fel och följ rekommenderade åtgärder för nätverksfel.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Kontrollera om Baltimore-rotcertifikatutfärdaren finns:

   `ls Baltimore_CyberTrust_Root.pem`

   - Om Baltimore-rotcertifikatutfärdaren inte hittas kör du det här kommandot för att hämta certifikatet:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Kontrollera om DigiCert_Global_Root_CA certifikat finns:

   `ls DigiCert_Global_Root_CA.pem`

    - Om DigiCert_Global_Root_CA inte hittas kör du följande kommandon för att hämta certifikatet:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Om du vill uppdatera certifikatämnets hashar för de nyligen nedladdade certifikaten kör du skriptet för återhash:

   `c_rehash`

1. Om du vill kontrollera om ämnet hashar som symlänkar har skapats för certifikaten kör du följande kommandon:

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. Skapa en kopia av filen _VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem_ med filnamn _b204d74a.0:_

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Skapa en kopia av filen _Baltimore_CyberTrust_Root.pem_ med filnamn _653b494a.0:_

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Skapa en kopia av filen _DigiCert_Global_Root_CA.pem_ med filnamn _3513523f.0:_

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. Kontrollera att filerna finns:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Utgående anslutning för url:er eller IP-intervall för webbplatsåterställning (felkod 151037 eller 151072)

För att site recovery-replikering ska fungera krävs utgående anslutning till specifika webbadresser från den virtuella datorn. Om den virtuella datorn ligger bakom en brandvägg eller använder NSG-regler (Network Security Group) för att styra utgående anslutning kan du ställas inför ett av dessa problem. Vi fortsätter att stödja utgående åtkomst via webbadresser, men med hjälp av en tillåtslista över IP-intervall stöds inte längre.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Problem 1: Det gick inte att registrera virtuell Azure-dator med Site Recovery (151195)

#### <a name="possible-causes"></a>Möjliga orsaker

- Det går inte att upprätta en anslutning till slutpunkter för platsåterställning på grund av ett DNS-lösningsfel (Domain Name System).
- Det här problemet är vanligare vid återskydd när du har misslyckats över den virtuella datorn, men DNS-servern kan inte nås från regionen för haveriberedskap (DR).

#### <a name="fix-the-problem"></a>Åtgärda problemet

Om du använder anpassad DNS kontrollerar du att DNS-servern är tillgänglig från katastrofåterställningsregionen.

Så här kontrollerar du om den virtuella datorn använder en anpassad DNS-inställning:

1. Öppna **virtuella datorer** och välj den virtuella datorn.
1. Navigera till de virtuella **datorernas inställningar** och välj **Nätverk**.
1. I **Virtuellt nätverk/undernät**väljer du länken för att öppna det virtuella nätverkets resurssida.
1. Gå till **Inställningar** och välj **DNS-servrar**.

Försök att komma åt DNS-servern från den virtuella datorn. Om DNS-servern inte är tillgänglig gör du den tillgänglig genom att antingen gå över DNS-servern eller skapa platsen mellan DR-nätverket och DNS.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-error.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Konfigurationen för återställning av platsen misslyckades (151196)

#### <a name="possible-cause"></a>Möjlig orsak

Det går inte att upprätta en anslutning till OFFICE 365-autentisering och identitets-IP4-slutpunkter.

#### <a name="fix-the-problem"></a>Åtgärda problemet

Azure Site Recovery krävs åtkomst till Office 365 IP-intervall för autentisering.
Om du använder NSG-regler (Azure Network Security Group) för att styra utgående nätverksanslutning på den virtuella datorn, kontrollerar du att du använder [Azure Active Directory (AAD) tjänsttagbaserad](/azure/virtual-network/security-overview#service-tags) NSG-regel för att tillåta åtkomst till AAD. Vi stöder inte längre IP-adressbaserade NSG-regler.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Konfigurationen för återställning av platsen misslyckades (151197)

#### <a name="possible-cause"></a>Möjlig orsak

Det går inte att upprätta en anslutning till slutpunkter för Azure Site Recovery-tjänsten.

#### <a name="fix-the-problem"></a>Åtgärda problemet

Om du använder NSG-regler (Azure Network Security Group) för att styra utgående nätverksanslutning på den virtuella datorn kontrollerar du att du använder tjänsttaggar. Vi har inte längre stöd för att använda en tillåtslista över IP-adresser via NSG:er för Azure Site Recovery.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problem 4: Azure till Azure-replikering misslyckades när nätverkstrafiken går igenom lokal proxyserver (151072)

#### <a name="possible-cause"></a>Möjlig orsak

De anpassade proxyinställningarna är ogiltiga och mobilitetstjänsten agenten har inte automatiskt de proxyinställningarna från Internet Explorer (IE).

#### <a name="fix-the-problem"></a>Åtgärda problemet

1. Mobility-serviceagenten identifierar proxyinställningarna från IE på Windows och `/etc/environment` på Linux.
1. Om du föredrar att ställa in proxy endast för mobilitetstjänsten kan du ange proxyinformation i _ProxyInfo.conf_ som finns på:

   - **Linux**:`/usr/local/InMage/config/`
   - **Windows**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo.conf_ bör ha proxyinställningarna i följande _INI-format._

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Tjänsten Mobilitet stöder endast **oautentiserade proxyservrar.**

### <a name="more-information"></a>Mer information

Om du vill ange [nödvändiga URL:er](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) eller de [IP-intervall som krävs](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)följer du anvisningarna i Om nätverk i Azure till Azure [replication](azure-to-azure-about-networking.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Det gick inte att hitta disken i datorn (felkod 150039)

En ny disk som är kopplad till den virtuella datorn måste initieras. Om disken inte hittas visas följande meddelande:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Möjliga orsaker

- En ny datadisk kopplades till den virtuella datorn men initierades inte.
- Datadisken i den virtuella datorn rapporterar inte korrekt det logiska enhetsnummer (LUN) värde som disken var kopplad till den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

Kontrollera att datadiskarna initieras och försök sedan igen.

- **Windows**: [Bifoga och initiera en ny disk](/azure/virtual-machines/windows/attach-managed-disk-portal).
- **Linux**: [Initiera en ny datadisk i Linux](/azure/virtual-machines/linux/add-disk).

Om problemet kvarstår kontaktar du supporten.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>En eller flera diskar är tillgängliga för skydd (felkod 153039)

### <a name="possible-causes"></a>Möjliga orsaker

- En eller flera diskar har nyligen lagts till den virtuella datorn efter skydd.
- En eller flera diskar initierades efter skydd av den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

Om du vill göra replikeringsstatusen för den virtuella datorn felfri igen kan du välja att antingen skydda diskarna eller att stänga av varningen.

#### <a name="to-protect-the-disks"></a>Så här skyddar du diskarna

1. Gå till_VM-namndiskar_ > **Disks** **för replikerade objekt** > .
1. Markera den oskyddade disken och välj sedan **Aktivera replikering:**

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Aktivera replikering på VM-diskar.":::

#### <a name="to-dismiss-the-warning"></a>Så här avfärdar du varningen

1. Gå till > _VM-namn_ **för replikerade objekt**.
1. Markera varningen i avsnittet **Översikt** och välj sedan **OK**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Stäng av en varning för ny disk.":::

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Ta bort den virtuella datorn från valvet som slutförts med information (felkod 150225)

När Site Recovery skyddar den virtuella datorn skapas länkar på den virtuella datorn för källan. När du tar bort skyddet eller inaktiverar replikering tar Site Recovery bort dessa länkar som en del av rensningsjobbet. Om den virtuella datorn har ett resurslås slutförs rensningsjobbet med informationen. Informationen säger att den virtuella datorn har tagits bort från Recovery Services-valvet, men att vissa av de inaktuella länkarna inte kunde rensas på källdatorn.

Du kan ignorera den här varningen om du aldrig tänker skydda den här virtuella datorn igen. Men om du måste skydda den här virtuella datorn senare följer du stegen i det här avsnittet för att rensa länkarna.

> [!WARNING]
> Om du inte gör rensningen:
>
> - När du aktiverar replikering med hjälp av valvet för återställningstjänster visas inte den virtuella datorn.
> - Om du försöker skydda den **Virtual machine** > virtuella datorn med hjälp av**Haveriberedskap för** > **virtuella**datorer misslyckas åtgärden med meddelandet **Replikeringen kan inte aktiveras på grund av de befintliga inaktuella resurslänkarna på den virtuella datorn**.

### <a name="fix-the-problem"></a>Åtgärda problemet

> [!NOTE]
> Site Recovery tar inte bort den virtuella källdatorn eller påverkar den på något sätt medan du utför dessa steg.

1. Ta bort låset från resursgruppen för virtuella datorer eller virtuella datorer. I följande bild måste resurslåset på den `MoveDemo` virtuella datorn som heter tas bort:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Ta bort lås från den virtuella datorn.":::

1. Hämta skriptet för att [ta bort en inaktuell site recovery-konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Kör skriptet, _Cleanup-unakt-asr-config-Azure-VM.ps1_. Ange **prenumerations-ID,** **VM-resursgrupp**och **VM-namn** som parametrar.
1. Om du uppmanas att ange Azure-autentiseringsuppgifter anger du dem. Kontrollera sedan att skriptet körs utan några fel.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>Replikering kan inte aktiveras på grund av inaktuella resurslänkar på den virtuella datorn (felkod 150226)

### <a name="possible-causes"></a>Möjliga orsaker

Den virtuella datorn har en inaktuell konfiguration från tidigare skydd för återställning av webbplats.

En inaktuell konfiguration kan uppstå på en virtuell Azure-dator om du har aktiverat replikering för Den virtuella Azure-datorn med hjälp av Site Recovery och sedan:

- Du inaktiverade replikering, men källdatorn hade ett resurslås.
- Du har tagit bort valvet för platsåterställning utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har tagit bort resursgruppen som innehåller valvet för platsåterställning utan att uttryckligen inaktivera replikering på den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

> [!NOTE]
> Site Recovery tar inte bort den virtuella källdatorn eller påverkar den på något sätt medan du utför dessa steg.

1. Ta bort låset från resursgruppen för virtuella datorer eller virtuella datorer. I följande bild måste resurslåset på den `MoveDemo` virtuella datorn som heter tas bort:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Ta bort lås från den virtuella datorn.":::

1. Hämta skriptet för att [ta bort en inaktuell site recovery-konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Kör skriptet, _Cleanup-unakt-asr-config-Azure-VM.ps1_. Ange **prenumerations-ID,** **VM-resursgrupp**och **VM-namn** som parametrar.
1. Om du uppmanas att ange Azure-autentiseringsuppgifter anger du dem. Kontrollera sedan att skriptet körs utan några fel.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Det gick inte att se den virtuella azure-datorn eller resursgruppen för valet i aktivera replikeringsjobbet

### <a name="issue-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Problem 1: Resursgruppen och den virtuella källdatorn finns på olika platser

Site Recovery kräver för närvarande att källregionens resursgrupp och virtuella datorer finns på samma plats. Om de inte är det kan du inte hitta den virtuella datorn eller resursgruppen när du försöker tillämpa skydd.

Som en lösning kan du aktivera replikering från den virtuella datorn i stället för valvet för återställningstjänster. Gå till **Käll-VM-egenskaper** > **Properties** > **Haveriberedskap** och aktivera replikeringen.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Problem 2: Resursgruppen ingår inte i den valda prenumerationen

Du kanske inte kan hitta resursgruppen vid tidpunkten för skyddet om resursgruppen inte ingår i den valda prenumerationen. Kontrollera att resursgruppen tillhör prenumerationen som du använder.

### <a name="issue-3-stale-configuration"></a>Problem 3: Inaktuell konfiguration

Du kanske inte ser den virtuella datorn som du vill aktivera för replikering om det finns en inaktuell site recovery-konfiguration på Den virtuella Azure-datorn. Det här tillståndet kan uppstå om du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och sedan:

- Du har tagit bort valvet för platsåterställning utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har tagit bort resursgruppen som innehåller valvet för platsåterställning utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du inaktiverade replikering, men källdatorn hade ett resurslås.

### <a name="fix-the-problem"></a>Åtgärda problemet

> [!NOTE]
> Se till att `AzureRM.Resources` uppdatera modulen innan du använder skriptet som nämns i det här avsnittet. Site Recovery tar inte bort den virtuella källdatorn eller påverkar den på något sätt medan du utför dessa steg.

1. Ta bort låset, om något, från resursgruppen för virtuella datorer eller virtuella datorer. I följande bild måste resurslåset på den `MoveDemo` virtuella datorn som heter tas bort:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Ta bort lås från den virtuella datorn.":::

1. Hämta skriptet för att [ta bort en inaktuell site recovery-konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Kör skriptet, _Cleanup-unakt-asr-config-Azure-VM.ps1_. Ange **prenumerations-ID,** **VM-resursgrupp**och **VM-namn** som parametrar.
1. Om du uppmanas att ange Azure-autentiseringsuppgifter anger du dem. Kontrollera sedan att skriptet körs utan några fel.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Det går inte att välja en virtuell dator för skydd

### <a name="possible-cause"></a>Möjlig orsak

Den virtuella datorn har ett tillägg installerat i ett feltillstånd eller inte svarar

### <a name="fix-the-problem"></a>Åtgärda problemet

Gå till**Tillägg** för**inställningar** >  **för virtuella datorer** > och kontrollera om det finns några tillägg i ett misslyckat tillstånd. Avinstallera ett misslyckat tillägg och försök sedan igen för att skydda den virtuella datorn.

## <a name="the-vms-provisioning-state-isnt-valid-error-code-150019"></a>Den virtuella datorns etableringstillstånd är ogiltigt (felkod 150019)

Om du vill aktivera replikering på den virtuella datorn måste dess **etableringstillstånd lyckades**. Så här kontrollerar du etableringstillståndet:

1. I Azure-portalen väljer du **Resursutforskaren** från **Alla tjänster**.
1. Expandera listan **Prenumerationer** och välj din prenumeration.
1. Expandera listan **ResourceGroups** och välj resursgruppen för den virtuella datorn.
1. Expandera **listan Resurser** och välj din virtuella dator.
1. Kontrollera **fältet etableringstate** i instansvyn till höger.

### <a name="fix-the-problem"></a>Åtgärda problemet

- Om **etableringstaten** **har misslyckats**kontaktar du supporten med information som du kan felsöka.
- Om **etableringstaten** **uppdateras**kan ett annat tillägg distribueras. Kontrollera om det finns några pågående åtgärder på den virtuella datorn, vänta tills de är klara och försök sedan igen det misslyckade site recovery-jobbet för att aktivera replikering.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Det går inte att välja mål-VM (fliken nätverksval är inte tillgänglig)

### <a name="issue-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Problem 1: Den virtuella datorn är ansluten till ett nätverk som redan är mappat till ett målnätverk

Om källdatorn är en del av ett virtuellt nätverk och en annan virtuell dator från samma virtuella nätverk redan är mappad med ett nätverk i målgruppen för nätverksval, är listrutan för nätverksval inte tillgänglig (visas nedtonad) som standard.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="Listan för nätverksval är inte tillgänglig.":::

### <a name="issue-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Problem 2: Du har tidigare skyddat den virtuella datorn med hjälp av Site Recovery och sedan inaktiverat replikeringen

Om du inaktiverar replikering av en virtuell dator tas inte nätverksmappningen bort. Mappningen måste tas bort från valvet för återställningstjänster där den virtuella datorn skyddades. Gå till **Återställningstjänster valv** > **Site Recovery Infrastructure** > **Network Mapping**.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Ta bort nätverksmappning.":::

Målnätverket som konfigurerades under inställningarna för haveriberedskap kan ändras efter den första installationen och efter att den virtuella datorn har skyddats:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Ändra nätverksmappning.":::

Om du ändrar nätverksmappning påverkas alla skyddade virtuella datorer som använder samma nätverksmappning.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM+ eller Volym shadow copy-tjänsten (felkod 151025)

När det här felet uppstår visas följande meddelande:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Möjliga orsaker

- Tjänsten COM+ System Application är inaktiverad.
- Tjänsten Volume Shadow Copy är inaktiverad.

### <a name="fix-the-problem"></a>Åtgärda problemet

Ställ in tjänsten COM+ System application and Volume Shadow Copy till automatiskt eller manuellt startläge.

1. Öppna tjänstekonsolen i Windows.
1. Kontrollera att TJÄNSTEN COM+ System Application and Volume Shadow Copy inte är **inaktiverade** som **starttyp**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Kontrollera starttypen com plus systemprogram och volymskuggkopieringstjänst.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Hanterade diskstorlek som inte stöds (felkod 150172)

När det här felet uppstår visas följande meddelande:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Möjlig orsak

Disken är mindre än storleken på 1024 MB som stöds.

### <a name="fix-the-problem"></a>Åtgärda problemet

Kontrollera att diskstorleken ligger inom storleksintervallet som stöds och försök sedan igen.

## <a name="protection-wasnt-enabled-because-the-grub-configuration-includes-the-device-name-instead-of-the-uuid-error-code-151126"></a>Skyddet aktiverades inte eftersom GRUB-konfigurationen innehåller enhetsnamnet i stället för UUID (felkod 151126)

### <a name="possible-causes"></a>Möjliga orsaker

Konfigurationsfilerna för Linux Grand Unified Bootloader (GRUB)_(/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_, eller _/etc/default/grub_) kan ange `root` `resume` de faktiska enhetsnamnen i stället för universellt unika identifierare (UUID) värden för och parametrar. Site Recovery kräver UUIDs eftersom enhetsnamn kan ändras. Vid omstart kanske en virtuell dator inte kommer med samma namn vid redundans, vilket resulterar i problem.

Följande exempel är rader från GRUB-filer där enhetsnamn visas i stället för de UUID:er som krävs:

- Fil _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Arkiv: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Åtgärda problemet

Ersätt varje enhetsnamn med motsvarande UUID:

1. Hitta enhetens UUID genom att `blkid <device name>`köra kommandot . Ett exempel:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Ersätt enhetsnamnet `root=UUID=<UUID>` med dess UUID, `resume=UUID=<UUID>`i formaten och . Efter byte, skulle till exempel raden från _/boot/grub/menu.lst_ se ut så här:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Försök igen.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>Aktivera skydd misslyckades eftersom enheten som nämns i GRUB-konfigurationen inte finns (felkod 151124)

### <a name="possible-cause"></a>Möjlig orsak

GRUB-konfigurationsfilerna (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_, or _/etc/default/grub_) kan innehålla parametrarna `rd.lvm.lv` eller `rd_LVM_LV`. Dessa parametrar identifierar LVM-enheterna (Logical Volume Manager) som ska identifieras vid start. Om dessa LVM-enheter inte finns startar inte själva det skyddade systemet och kommer att fastna i startprocessen. Samma problem kommer också att ses med redundans-VM. Här är några exempel:

- Fil: _/boot/grub2/grub.cfg_ på RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Fil: _/etc/default/grub_ på RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Arkiv: _/boot/grub/menu.lst_ på RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

I varje exempel måste GRUB identifiera två LVM-enheter med namnen `root` och `swap` från volymgruppen `rootvg`.

### <a name="fix-the-problem"></a>Åtgärda problemet

Om LVM-enheten inte finns, antingen skapa den eller ta bort motsvarande parametrar från GRUB konfigurationsfiler. Försök sedan igen för att aktivera skydd.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>En uppdatering av tjänsten Site Recovery Mobility har avslutats med varningar (felkod 151083)

Tjänsten Site Recovery Mobility har många komponenter, varav en kallas filterdrivrutinen. Filterdrivrutinen läses bara in i systemminnet under omstarten av systemet. När en uppdatering av mobilitetstjänsten innehåller filterdrivrutinsändringar uppdateras datorn, men du ser fortfarande en varning om att vissa korrigeringar kräver en omstart. Varningen visas eftersom filterdrivrutinskorrigeringarna endast kan börja gälla när den nya filterdrivrutinen läses in, vilket bara sker under en omstart.

> [!NOTE]
> Det här är bara en varning. Den befintliga replikeringen fortsätter att fungera även efter den nya agentuppdateringen. Du kan välja att starta om när du vill ha fördelarna med den nya filterdrivrutinen, men den gamla filterdrivrutinen fortsätter att fungera om du inte startar om.
>
> Förutom filterdrivrutinen börjar fördelarna med andra förbättringar och korrigeringar i uppdateringen av mobilitetstjänsten att gälla utan att kräva en omstart.

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Det gick inte att aktivera skyddet eftersom den replikhanterade disken redan finns, utan förväntade taggar, i målresursgruppen (felkod 150161)

### <a name="possible-cause"></a>Möjlig orsak

Det här problemet kan uppstå om den virtuella datorn tidigare skyddades och när replikeringen inaktiverades togs inte replikdisken bort.

### <a name="fix-the-problem"></a>Åtgärda problemet

Ta bort replikdisken som identifierats i felmeddelandet och försök igen det misslyckade skyddsjobbet.

## <a name="next-steps"></a>Nästa steg

[Replikera virtuella Azure-datorer till en annan Azure-region](azure-to-azure-how-to-enable-replication.md)
