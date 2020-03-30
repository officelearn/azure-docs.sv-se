---
title: Felsöka Azure VM-replikering i Azure Site Recovery
description: Felsöka fel vid replikerande av virtuella Azure-datorer för haveriberedskap.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: rochakm
ms.openlocfilehash: c131609a5622061e2ea49db422bc4e9da96666d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276690"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Felsöka azure-till-Azure VM-replikeringsfel

I den här artikeln beskrivs felsÃ¶kning av vanliga fel i Azure Site Recovery under replikering och återställning av virtuella Azure-datorer (VMs) från en region till en annan. Mer information om konfigurationer som stöds finns i [supportmatrisen för replikering av virtuella Azure-datorer](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a><a name="azure-resource-quota-issues-error-code-150097"></a>Problem med Azure-resurskvoter (felkod 150097)

Kontrollera att din prenumeration är aktiverad för att skapa virtuella Azure-datorer i målregionen som du planerar att använda som din katastrofåterställningsregion. Se också till att din prenumeration har tillräcklig kvot för att skapa virtuella datorer av de nödvändiga storlekarna. Som standard väljer Site Recovery en mål-VM-storlek som är samma som källan VM-storlek. Om matchningsstorleken inte är tillgänglig väljer Site Recovery automatiskt den närmaste tillgängliga storleken.

Om det inte finns någon storlek som stöder konfigurationen av käll-VM visas följande meddelande:

> "Replikering kunde inte aktiveras för den virtuella datorn *VmName*."

### <a name="possible-causes"></a>Möjliga orsaker

- Ditt prenumerations-ID är inte aktiverat för att skapa virtuella datorer på målområdets plats.
- Ditt prenumerations-ID är inte aktiverat, eller inte har tillräcklig kvot, för att skapa specifika vm-storlekar på målområdets plats.
- Det finns ingen lämplig mål-VM-storlek som matchar källdatorns nätverkskort (NIC) (2), för prenumerations-ID:t på målområdesplatsen.

### <a name="fix-the-problem"></a>Åtgärda problemet

Kontakta [Azure-faktureringssupport](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) för att göra det möjligt för din prenumeration att skapa virtuella datorer med de storlekar som krävs på målplatsen. Försök sedan igen den misslyckade åtgärden.

Om målplatsen har ett kapacitetsbegränsning inaktiverar du replikering till den. Aktivera sedan replikering till en annan plats där din prenumeration har tillräcklig kvot för att skapa virtuella datorer med de storlekar som krävs.

## <a name="trusted-root-certificates-error-code-151066"></a><a name="trusted-root-certificates-error-code-151066"></a>Betrodda rotcertifikat (felkod 151066)

Om inte alla de senaste betrodda rotcertifikaten finns på den virtuella datorn kan jobbet "Aktivera replikering" platsåterställning misslyckas. Autentisering och auktorisering av tjänstanrop för platsåterställning från den virtuella datorn misslyckas utan dessa certifikat.

Om jobbet Aktivera replikering misslyckas visas följande meddelande:

> "Konfigurationen för platsåterställning misslyckades."

### <a name="possible-cause"></a>Möjlig orsak

De betrodda rotcertifikat som krävs för auktorisering och autentisering finns inte på den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

#### <a name="windows"></a>Windows

För en virtuell dator som kör Operativsystemet Windows installerar du de senaste Windows-uppdateringarna på den virtuella datorn så att alla betrodda rotcertifikat finns på datorn. Följ den typiska processen för hantering av Windows-uppdateringshantering eller certifikatuppdatering i organisationen för att hämta de senaste rotcertifikaten och den uppdaterade listan över återkallade certifikat på de virtuella datorerna.

Om du är i en frånkopplad miljö följer du standardprocessen för Windows-uppdateringen i organisationen för att hämta certifikaten. Om de nödvändiga certifikaten inte finns på den virtuella datorn misslyckas anropen till tjänsten Site Recovery av säkerhetsskäl.

Om du vill kontrollera att problemet är löst går du till login.microsoftonline.com från en webbläsare i den virtuella datorn.

Mer information finns i [Konfigurera betrodda rötter och otillåtna certifikat](https://technet.microsoft.com/library/dn265983.aspx).

#### <a name="linux"></a>Linux

Följ vägledningen från distributören av din Linux-operativsystemversion för att få de senaste betrodda rotcertifikaten och den senaste listan över återkallade certifikat på den virtuella datorn.

Eftersom SUSE Linux använder symboliska länkar (eller *symlänkar)* för att underhålla en certifikatlista gör du så här:

1. Logga in som rotanvändare.

1. Kör det här kommandot om du vill ändra katalogen:

    **# cd /etc/ssl/certs # cd /etc/ssl/certs # cd /etc/ssl/certs # cd**

1. Kontrollera om Symantec-rotcertifikatutfärdaren finns:

    **# Ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. Om Symantec-rotcertifikatutfärdarcertifikatet inte hittas kör du följande kommando för att hämta filen. Sök efter eventuella fel och följ rekommenderade åtgärder för nätverksfel.

    **# wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. Kontrollera om Baltimore-rotcertifikatutfärdaren finns:

    **# Ls Baltimore_CyberTrust_Root.pem**

1. Om Baltimore-rotcertifikatutfärdaren inte hittas kör du det här kommandot för att hämta certifikatet:

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem**

1. Kontrollera om DigiCert_Global_Root_CA certifikat finns:

    **# Ls DigiCert_Global_Root_CA.pem**

1. Om DigiCert_Global_Root_CA inte hittas kör du följande kommandon för att hämta certifikatet:

    **# wgethttp://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# openssl x509 -i DigiCertGlobalRootCA.crt -inform der-outform pem -out DigiCert_Global_Root_CA.pem #**

1. Kör rehash-skriptet för att uppdatera certifikatämnets hashar för de nyligen nedladdade certifikaten:

    **# c_rehash**

1. Kör dessa kommandon för att kontrollera om ämnet hashar som symlänkar har skapats för certifikaten:

    - Kommandot:

        **# ls -l | grep Baltimore (på ett sätt)**

    - Resultat:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - Kommandot:

        **# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - Resultat:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - Kommandot:

        **# ls -l | grep DigiCert_Global_Root**

    - Resultat:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. Skapa en kopia av filen VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem med filnamn b204d74a.0:

    **# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0**

1. Skapa en kopia av filen Baltimore_CyberTrust_Root.pem med filnamn 653b494a.0:

    **# cp Baltimore_CyberTrust_Root.pem 653b494a.0**

1. Skapa en kopia av filen DigiCert_Global_Root_CA.pem med filnamn 3513523f.0:

    **# cp DigiCert_Global_Root_CA.pem 3513523f.0**

1. Kontrollera om filerna finns:

    - Kommandot:

        **# ls -l 653b494a.0 b204d74a.0 3513523f.0**

    - Resultat

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Utgående anslutning för url:er eller IP-intervall för webbplatsåterställning (felkod 151037 eller 151072)

För att site recovery-replikering ska fungera krävs utgående anslutning till specifika webbadresser från den virtuella datorn. Om den virtuella datorn ligger bakom en brandvägg eller använder NSG-regler (Network Security Group) för att styra utgående anslutning kan du ställas inför ett av dessa problem. Observera att även om vi fortsätter att stödja utgående åtkomst via webbadresser, stöds inte längre en lista över IP-intervall med hjälp av en tillåtslista över IP-intervall.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problem 1: Det gick inte att registrera virtuell Azure-dator med Site Recovery (151195) </br>
- **Möjlig orsak** </br>
  - Det går inte att upprätta anslutningen till slutpunkter för platsåterställning på grund av DNS-lösningsfel.
  - Detta visas oftare vid återskydd när du har redundansväxlat den virtuella datorn men DNS-servern inte kan nås från regionen för haveriberedskap.

- **Upplösning**
   - Om du använder anpassad DNS kontrollerar du att DNS-servern är tillgänglig från regionen Haveriberedskap. För att kontrollera om du har en anpassad DNS gå till VM> Disaster Recovery-nätverket> DNS-servrar. Försök att öppna DNS-servern från den virtuella datorn. Om den inte är tillgänglig gör den tillgänglig genom att antingen misslyckas över DNS-servern eller skapa platsen mellan DR-nätverket och DNS.

    ![com-fel](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Konfigurationen för återställning av platsen misslyckades (151196)
- **Möjlig orsak** </br>
  - Det går inte att upprätta anslutningen till Office 365-autentiserings- och identitets-IP4-slutpunkter.

- **Upplösning**
  - Azure Site Recovery krävs åtkomst till Office 365 IPs-intervall för autentisering.
    Om du använder NSG-regler (Azure Network Security Group) för att styra utgående nätverksanslutning på den virtuella datorn, se till att du använder [Azure Active Directory (AAD) tjänsttagbaserad](../virtual-network/security-overview.md#service-tags) NSG-regel för att tillåta åtkomst till AAD. Vi stöder inte längre IP-adressbaserade NSG-regler.


### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Konfigurationen för återställning av platsen misslyckades (151197)
- **Möjlig orsak** </br>
  - Det går inte att upprätta anslutningen till azure site recovery-tjänstslutpunkter.

- **Upplösning**
  - Om du använder NSG-regler (Azure Network Security Group) för att styra utgående nätverksanslutning på den virtuella datorn kontrollerar du att du använder tjänsttaggar. Vi har inte längre stöd för att använda en tillåtslista över IP-adresser via NSG:er för Azure Site Recovery (ASR).


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>Problem 4: A2A-replikering misslyckades när nätverkstrafiken gick igenom den lokala proxyservern (151072)
 - **Möjlig orsak** </br>
   - De anpassade proxyinställningarna är ogiltiga och ASR Mobility Service-agenten upptäckte inte proxyinställningarna automatiskt från IE


 - **Upplösning**
   1.    Mobility Service agent upptäcker proxyinställningar från IE på Windows och / etc / miljö på Linux.
   2.  Om du föredrar att ställa in proxy endast för ASR Mobility Service, kan du ange proxyinformation i ProxyInfo.conf finns på:</br>
       - ``/usr/local/InMage/config/``på ***Linux***
       - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``på ***Windows***
   3.    ProxyInfo.conf ska ha rätt proxyinställningar i följande INI-format.</br>
                   *-Jag har inte tid med det här.*</br>
                   *Adress=http://1.2.3.4*</br>
                   *Port=567*</br>
   4. ASR Mobility Service-agenten stöder endast ***icke-autentiserade proxyservrar***.


### <a name="fix-the-problem"></a>Åtgärda problemet

Om du vill lägga till [de webbadresser som krävs](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) i en tillåt-lista följer du stegen i [vägledningsdokumentet](site-recovery-azure-to-azure-networking-guidance.md)för nätverk .


### <a name="more-information"></a>Mer information

Om du vill ange [nödvändiga URL:er](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) eller [obligatoriska IP-intervall](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)följer du anvisningarna i [Om nätverk i Azure till Azure replication](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Det gick inte att hitta disken i datorn (felkod 150039)

En ny disk som är kopplad till den virtuella datorn måste initieras. Om disken inte hittas visas följande meddelande:

> "Azure data disk *DiskName* *DiskURI* med logiskt enhetsnummer *LUN* *LUNValue* mappades inte till en motsvarande disk som rapporteras inifrån den virtuella datorn som har samma LUN-värde.

### <a name="possible-causes"></a>Möjliga orsaker

- En ny datadisk kopplades till den virtuella datorn men initierades inte.
- Datadisken inuti den virtuella datorn rapporterar inte korrekt det logiska enhetsnummer (LUN) värde som disken var kopplad till den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

Kontrollera att datadiskarna initieras och försök sedan igen.

- **Windows**: [Bifoga och initiera en ny disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).

- **Linux**: [Initiera en ny datadisk i Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Om problemet kvarstår kontaktar du supporten.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>En eller flera diskar är tillgängliga för skydd (felkod 153039)

### <a name="possible-causes"></a>Möjliga orsaker

- En eller flera diskar har nyligen lagts till den virtuella datorn efter skydd.
- En eller flera diskar initierades efter skydd av den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

Om du vill göra replikeringsstatusen för den virtuella datorn felfri igen kan du välja att antingen skydda diskarna eller att stänga av varningen.

#### <a name="to-protect-the-disks"></a>Så här skyddar du diskarna

1. Gå till*VM-namndiskar* > **Disks** **för replikerade objekt** > .
1. Markera den oskyddade disken och välj sedan **Aktivera replikering:**

    ![Aktivera replikering på VM-diskar](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>Så här avfärdar du varningen

1. Gå till > *VM-namn* **för replikerade objekt**.
1. Markera varningen i avsnittet **Översikt** och välj sedan **OK**.

    ![Avvisa varning för ny disk](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Ta bort den virtuella datorn från valvet som slutförts med information (felkod 150225)

När den skyddar den virtuella datorn skapar Site Recovery några länkar på den virtuella källdatorn. När du tar bort skyddet eller inaktiverar replikering tar Site Recovery bort dessa länkar som en del av rensningsjobbet. Om den virtuella datorn har ett resurslås slutförs rensningsjobbet med informationen. Informationen säger att den virtuella datorn har tagits bort från Recovery Services-valvet, men att vissa av de inaktuella länkarna inte kunde rensas på källdatorn.

Du kan ignorera den här varningen om du aldrig tänker skydda den här virtuella datorn igen. Men om du måste skydda den här virtuella datorn senare följer du stegen under "Åtgärda problemet" för att rensa länkarna.

> [!WARNING]
> Om du inte gör rensningen:
>
> - När du aktiverar replikering med hjälp av valvet för återställningstjänster visas inte den virtuella datorn.
> - Om du försöker skydda den virtuella datorn med hjälp av**Disaster Recovery****för** >  **virtuella datorer** > misslyckas åtgärden med meddelandet "Replikering kan inte aktiveras på grund av de befintliga inaktuella resurslänkarna på den virtuella datorn".

### <a name="fix-the-problem"></a>Åtgärda problemet

> [!NOTE]
> Site Recovery tar inte bort den virtuella källdatorn eller påverkar den på något sätt medan du utför dessa steg.

1. Ta bort låset från resursgruppen för virtuella datorer eller virtuella datorer. I följande bild måste resurslåset på den virtuella datorn med namnet "MoveDemo" tas bort:

    ![Ta bort lås från virtuell dator](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Hämta skriptet för att [ta bort en inaktuell site recovery-konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Kör skriptet, som kallas Cleanup-undel-asr-config-Azure-VM.ps1. Ange prenumerations-ID, VM-resursgrupp och VM-namn som parametrar.
1. Om du blir tillfrågad om Azure-autentiseringsuppgifter anger du dem. Kontrollera sedan att skriptet körs utan några fel.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>Replikering kan inte aktiveras på grund av inaktuella resurslänkar på den virtuella datorn (felkod 150226)

### <a name="possible-cause"></a>Möjlig orsak

Den virtuella datorn har en inaktuell konfiguration från tidigare skydd för återställning av webbplats.

En inaktuell konfiguration kan uppstå på en virtuell Azure-dator om du har aktiverat replikering för Den virtuella Azure-datorn med hjälp av Site Recovery och sedan:

- Du inaktiverade replikering, men källdatorn hade ett resurslås.
- Du har tagit bort valvet för platsåterställning utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har tagit bort resursgruppen som innehåller valvet för platsåterställning utan att uttryckligen inaktivera replikering på den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

> [!NOTE]
> Site Recovery tar inte bort den virtuella källdatorn eller påverkar den på något sätt medan du utför dessa steg.

1. Ta bort låset från resursgruppen för virtuella datorer eller virtuella datorer. I följande bild måste resurslåset på den virtuella datorn med namnet "MoveDemo" tas bort:

    ![Ta bort lås från virtuell dator](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Hämta skriptet för att [ta bort en inaktuell site recovery-konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Kör skriptet, som kallas Cleanup-undel-asr-config-Azure-VM.ps1. Ange prenumerations-ID, VM-resursgrupp och VM-namn som parametrar.
1. Om du blir tillfrågad om Azure-autentiseringsuppgifter anger du dem. Kontrollera sedan att skriptet körs utan några fel.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Det gick inte att se azure-virtuella datorn eller resursgruppen för valet i jobbet Aktivera replikering

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Orsak 1: Resursgruppen och den virtuella källdatorn finns på olika platser

Site Recovery kräver för närvarande att källregionens resursgrupp och virtuella datorer finns på samma plats. Om de inte är det kan du inte hitta den virtuella datorn eller resursgruppen när du försöker tillämpa skydd.

Som en lösning kan du aktivera replikering från den virtuella datorn i stället för valvet för återställningstjänster. Gå till **Käll-VM-egenskaper** > **Properties** > **Haveriberedskap** och aktivera replikeringen.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>Orsak 2: Resursgruppen ingår inte i den valda prenumerationen

Du kanske inte kan hitta resursgruppen vid tidpunkten för skyddet om resursgruppen inte ingår i den valda prenumerationen. Kontrollera att resursgruppen tillhör prenumerationen som du använder.

### <a name="cause-3-stale-configuration"></a>Orsak 3: Inaktuell konfiguration

Du kanske inte ser den virtuella datorn som du vill aktivera för replikering om en inaktuell site recovery-konfiguration har lämnats på den virtuella Azure-datorn. Det här tillståndet kan uppstå om du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och sedan:

- Du har tagit bort valvet för platsåterställning utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har tagit bort resursgruppen som innehåller valvet för platsåterställning utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du inaktiverade replikering, men källdatorn hade ett resurslås.

### <a name="fix-the-problem"></a>Åtgärda problemet

> [!NOTE]
> Se till att uppdatera modulen "AzureRM.Resources" innan du använder skriptet som nämns i det här avsnittet.  Site Recovery tar inte bort den virtuella källdatorn eller påverkar den på något sätt medan du utför dessa steg.

1. Ta bort låset, om något, från resursgruppen för virtuella datorer eller virtuella datorer. I följande bild måste resurslåset på den virtuella datorn med namnet "MoveDemo" tas bort:

    ![Ta bort lås från virtuell dator](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Hämta skriptet för att [ta bort en inaktuell site recovery-konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Kör skriptet, som kallas Cleanup-undel-asr-config-Azure-VM.ps1. Ange prenumerations-ID, VM-resursgrupp och VM-namn som parametrar.
1. Om du blir tillfrågad om Azure-autentiseringsuppgifter anger du dem. Kontrollera sedan att skriptet körs utan några fel.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Det går inte att välja en virtuell dator för skydd

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>Orsak 1: Den virtuella datorn har ett tillägg installerat i ett feltillstånd eller inte svarar

Gå till**Tillägg** för**inställningar** >  **för virtuella datorer** > och kontrollera om det finns några tillägg i ett misslyckat tillstånd. Avinstallera ett misslyckat tillägg och försök sedan igen för att skydda den virtuella datorn.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>Orsak 2: Den virtuella datorns etableringstillstånd är ogiltigt

Se felsökningsstegen i [den virtuella datorns etableringstillstånd är ogiltigt](#the-vms-provisioning-state-is-not-valid-error-code-150019)senare i den här artikeln.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>Den virtuella datorns etableringstillstånd är ogiltigt (felkod 150019)

Om du vill aktivera replikering på den virtuella datorn måste dess **etableringstillstånd lyckades**. Så här kontrollerar du etableringstillståndet:

1. I Azure-portalen väljer du **Resursutforskaren** från **Alla tjänster**.
1. Expandera listan **Prenumerationer** och välj din prenumeration.
1. Expandera listan **ResourceGroups** och välj resursgruppen för den virtuella datorn.
1. Expandera **listan Resurser** och välj din virtuella dator.
1. Kontrollera **fältet etableringstate** i instansvyn till höger.

### <a name="fix-the-problem"></a>Åtgärda problemet

- Om **etableringstate** är **misslyckad**kontaktar du support med information att felsöka.
- Om **etableringstate** **är Uppdatering**kan ett annat tillägg distribueras. Kontrollera om det finns några pågående åtgärder på den virtuella datorn, vänta tills de är klara och försök sedan igen det misslyckade site recovery -jobbet "Aktivera replikering".

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Det går inte att välja mål-VM (fliken nätverksval är inte tillgänglig)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Orsak 1: Den virtuella datorn är ansluten till ett nätverk som redan är mappat till ett målnätverk

Om källdatorn är en del av ett virtuellt nätverk och en annan virtuell dator från samma virtuella nätverk redan är mappad med ett nätverk i målresursgruppen, är listrutan för nätverksval inte tillgänglig (visas nedtonad) som standard.

![Listan för nätverksval är inte tillgänglig](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Orsak 2: Du har tidigare skyddat den virtuella datorn med hjälp av Site Recovery och sedan inaktiverat replikeringen

Om du inaktiverar replikering av en virtuell dator tas inte nätverksmappningen bort. Mappningen måste tas bort från valvet för återställningstjänster där den virtuella datorn skyddades. Gå till *Återställningstjänster valv* > **Site Recovery Infrastructure** > **Network Mapping**.

![Ta bort nätverksmappning](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

Målnätverket som konfigurerades under inställningen för haveriberedskap kan ändras efter den första installationen, efter att den virtuella datorn har skyddats:

![Ändra nätverksmappning](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

Observera att en ändring av nätverksmappningen påverkar alla skyddade virtuella datorer som använder samma nätverksmappning.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM+ eller Tjänsten Volume Shadow Copy (felkod 151025)

NÃ¤r det hÃ¤r felet inträffar visas fÃ¶1 ã¥st:

> "Det gick inte att installera det här tillägget för återställning av platsen"

### <a name="possible-causes"></a>Möjliga orsaker

- Tjänsten COM+ System Application är inaktiverad.
- Tjänsten Volume Shadow Copy är inaktiverad.

### <a name="fix-the-problem"></a>Åtgärda problemet

Ställ in tjänsterna COM+ System application and Volume Shadow Copy till automatiskt eller manuellt startläge.

1. Öppna tjänstekonsolen i Windows.
1. Kontrollera att COM+ System application and Volume Shadow Copy-tjänsterna inte är **inaktiverade** som **starttyp**.

    ![Kontrollera starttypen för COM+ System application and Volume Shadow Copy-tjänster](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Hanterade diskstorlek som inte stöds (felkod 150172)

NÃ¤r det hÃ¤r felet inträffar visas fÃ¶1 ã¥st:

> "Skydd kunde inte aktiveras för den virtuella datorn eftersom den har *DiskName* med storlek *DiskSize*)* som är mindre än den minsta storlek som stöds 1024 MB."

### <a name="possible-cause"></a>Möjlig orsak

Disken är mindre än storleken på 1024 MB som stöds.

### <a name="fix-the-problem"></a>Åtgärda problemet

Kontrollera att diskstorleken ligger inom storleksintervallet som stöds och försök sedan igen.

## <a name="protection-was-not-enabled-because-the-grub-configuration-includes-the-device-name-instead-of-the-uuid-error-code-151126"></a><a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Skyddet aktiverades inte eftersom GRUB-konfigurationen innehåller enhetsnamnet i stället för UUID (felkod 151126)

### <a name="possible-cause"></a>Möjlig orsak

Linux GRUB-konfigurationsfilerna (/boot/grub/menu.lst", /boot/grub/grub.cfg, /boot/grub2/grub.cfg, eller /etc/default/grub) kan ange de faktiska enhetsnamnen i stället för UUID-värden för *rot-* och *återuppta parametrarna.* Site Recovery kräver UUIDs eftersom enhetsnamn kan ändras. Vid omstart kanske en virtuell dator inte kommer med samma namn vid redundans, vilket resulterar i problem.

Följande exempel är rader från GRUB-filer där enhetsnamn (visas i fetstil) visas i stället för de UUID:er som krävs:

- Fil /boot/grub2/grub.cfg

  > linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2 ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts Linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2 ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts Linux /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2** ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts Linux /

- Arkiv: /boot/grub/menu.lst

  > kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314


### <a name="fix-the-problem"></a>Åtgärda problemet

Ersätt varje enhetsnamn med motsvarande UUID:

1. Hitta enhetens UUID genom att köra kommandot **blkid-enhetsnamnet** ***device name***. Ett exempel:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Ersätt enhetsnamnet med dess UUID, i formaten **root=UUID**=*UUID* och **resume=UUID**=*UUID*. Efter byte skulle till exempel raden från /boot/grub/menu.lst (som diskuterats tidigare) se ut så här:

    > kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314

1. Försök igen.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>Aktivera skydd misslyckades eftersom enheten som nämns i GRUB-konfigurationen inte finns (felkod 151124)

### <a name="possible-cause"></a>Möjlig orsak

GRUB-konfigurationsfilerna (/boot/grub/menu.lst, /boot/grub/grub.cfg, /boot/grub2/grub.cfg, eller /etc/default/grub) kan innehålla parametrarna *rd.lvm.lv* eller *rd_LVM_LV*. Dessa parametrar identifierar LVM-enheterna (Logical Volume Manager) som ska identifieras vid start. Om dessa LVM-enheter inte finns startar inte själva det skyddade systemet och kommer att fastna i startprocessen. Samma problem kommer också att ses med redundans-VM. Här är några exempel:

- Fil: /boot/grub2/grub.cfg på RHEL7:

    > linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US. UTF-8

- Fil: /etc/default/grub på RHEL7:

    > GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"

- Arkiv: /boot/grub/menu.lst på RHEL6:

    > kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US. UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto **rd_LVM_LV=rootvg/lv_root** KEYBOARDTYPE=pc KEYTABLE=us **rd_LVM_LV=rootvg/lv_swap** rd_NO_DM rhgb quiet

I varje exempel visar delen i fetstil att GRUB måste identifiera två LVM-enheter med namnen "root" och "swap" från volymgruppen "rootvg".

### <a name="fix-the-problem"></a>Åtgärda problemet

Om LVM-enheten inte finns, antingen skapa den eller ta bort motsvarande parametrar från GRUB konfigurationsfiler. Försök sedan igen för att aktivera skydd.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>En uppdatering av mobilitetstjänsten för webbplatsåterställning har slutförts med varningar (felkod 151083)

Site Recovery Mobility Service har många komponenter, varav en kallas filterdrivrutinen. Filterdrivrutinen läses bara in i systemminnet under omstarten av systemet. När en Mobility Service-uppdatering innehåller filterdrivrutinsändringar uppdateras datorn, men du ser fortfarande en varning om att vissa korrigeringar kräver en omstart. Varningen visas eftersom filterdrivrutinskorrigeringarna endast kan börja gälla när den nya filterdrivrutinen läses in, vilket bara sker under en omstart.

> [!NOTE]
> Det här är bara en varning. Befintlig replikering fortsätter att fungera även efter den nya agentuppdateringen. Du kan välja att starta om när du vill ha fördelarna med den nya filterdrivrutinen, men den gamla filterdrivrutinen fortsätter att fungera om du inte startar om.
>
> Förutom filterdrivrutinen börjar fördelarna med andra förbättringar och korrigeringar i uppdateringen av Mobilitetstjänsten att gälla utan att kräva en omstart.

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Det gick inte att aktivera skyddet eftersom den replikhanterade disken redan finns, utan förväntade taggar, i målresursgruppen (felkod 150161)

### <a name="possible-cause"></a>Möjlig orsak

Det här problemet kan uppstå om den virtuella datorn tidigare skyddades och när replikeringen inaktiverades rensades inte replikdisken.

### <a name="fix-the-problem"></a>Åtgärda problemet

Ta bort replikdisken som identifierats i felmeddelandet och försök igen det misslyckade skyddsjobbet.

## <a name="next-steps"></a>Nästa steg

[Replikera virtuella Azure-datorer](site-recovery-replicate-azure-to-azure.md)
