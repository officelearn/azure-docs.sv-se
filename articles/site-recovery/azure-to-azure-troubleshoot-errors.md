---
title: Felsöka Azure VM-replikering i Azure Site Recovery
description: Felsök fel vid replikering av virtuella Azure-datorer för haveri beredskap.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: rochakm
ms.openlocfilehash: 32d826f3c27cea3d0993c47e8562360315b7bd2e
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78256051"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Felsöka fel med Azure-till-Azure VM-replikering

Den här artikeln beskriver hur du felsöker vanliga fel i Azure Site Recovery vid replikering och återställning av virtuella Azure-datorer (VM) från en region till en annan. Mer information om konfigurationer som stöds finns i [support mat ris för replikering av virtuella Azure-datorer](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problem med resurs kvoter i Azure (felkod 150097)

Se till att din prenumeration är aktive rad för att skapa virtuella Azure-datorer i mål regionen som du planerar att använda som din katastrof återställnings region. Kontrol lera också att prenumerationen har tillräcklig kvot för att skapa virtuella datorer av de nödvändiga storlekarna. Som standard väljer Site Recovery en storlek för virtuell måldator som är samma som storleken på den virtuella käll datorn. Om den matchande storleken inte är tillgänglig väljer Site Recovery automatiskt den närmast tillgängliga storleken.

Om det inte finns någon storlek som stöder den virtuella käll konfigurationen visas följande meddelande:

> "Det gick inte att aktivera replikering för den virtuella datorns *VmName*."

### <a name="possible-causes"></a>Möjliga orsaker

- Ditt prenumerations-ID har inte Aktiver ATS för att skapa virtuella datorer i mål regionens plats.
- Ditt prenumerations-ID är inte aktiverat eller har inte tillräcklig kvot för att skapa specifika VM-storlekar på platsen för mål regionen.
- Ingen lämplig storlek på virtuell måldator hittades för den virtuella käll datorns nätverkskort (NIC), för prenumerations-ID: t i mål regionens plats.

### <a name="fix-the-problem"></a>Åtgärda problemet

Kontakta [Azures fakturerings support](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) för att aktivera din prenumeration för att skapa virtuella datorer av de nödvändiga storlekarna på mål platsen. Försök sedan utföra åtgärden igen.

Om mål platsen har en kapacitets begränsning inaktiverar du replikering till den. Aktivera sedan replikering till en annan plats där prenumerationen har tillräcklig kvot för att skapa virtuella datorer av de nödvändiga storlekarna.

## <a name="trusted-root-certificates-error-code-151066"></a>Betrodda rot certifikat (felkod 151066)

Om inte alla de senaste betrodda rot certifikaten finns på den virtuella datorn kan det hända att Site Recovery jobbet "Aktivera replikering". Autentisering och auktorisering av Site Recovery tjänst anrop från den virtuella datorn fungerar inte utan dessa certifikat. 

Om jobbet "Aktivera replikering" Miss lyckas visas följande meddelande:

> "Site Recovery konfigurationen misslyckades."

### <a name="possible-cause"></a>Möjlig orsak

De betrodda rot certifikat som krävs för auktorisering och autentisering finns inte på den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

#### <a name="windows"></a>Windows

För en virtuell dator som kör operativ systemet Windows installerar du de senaste Windows-uppdateringarna på den virtuella datorn så att alla betrodda rot certifikat finns på datorn. Följ den vanliga hanterings processen för Windows Update-hantering eller certifikat uppdatering i din organisation för att hämta de senaste rot certifikaten och den uppdaterade listan över återkallade certifikat på de virtuella datorerna.

Om du arbetar i en frånkopplad miljö, följer du Windows update standardprocessen i din organisation för att hämta certifikaten. Om certifikat som krävs inte finns på den virtuella datorn kan misslyckas anrop till Site Recovery-tjänsten av säkerhetsskäl.

Kontrollera att problemet är löst genom att gå till login.microsoftonline.com från en webbläsare i den virtuella datorn.

Mer information finns i [Konfigurera betrodda rötter och otillåtna certifikat](https://technet.microsoft.com/library/dn265983.aspx).

#### <a name="linux"></a>Linux

Följ de rikt linjer som tillhandahålls av distributören av Linux-operativsystemet för att hämta de senaste betrodda rot certifikaten och den senaste listan över återkallade certifikat på den virtuella datorn.

Eftersom SUSE Linux använder symboliska länkar (eller *symlinks*) för att underhålla en certifikat lista, följer du dessa steg:

1. Logga in som en rotanvändare.

1. Kör det här kommandot för att ändra katalogen:

    **antal CD-/etc/ssl/certs**

1. Kontrol lera om Symantec-rotcertifikatet för rot certifikat finns:

    **# LS VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Om Symantecs CA-certifikat inte hittas kör du följande kommando för att ladda ned filen. Kontrol lera om det finns några fel och följ rekommenderade åtgärder för nätverks fel.

    **# wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem-O VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem**

1. Kontrol lera om Baltimore rot certifikat finns:

    **# LS Baltimore_CyberTrust_Root. pem**

1. Om Baltimore inte hittas kör du det här kommandot för att ladda ned certifikatet:

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem-O Baltimore_CyberTrust_Root. pem**

1. Kontrol lera om DigiCert_Global_Root_CA certifikatet finns:

    **# LS DigiCert_Global_Root_CA. pem**

1. Om DigiCert_Global_Root_CA inte hittas kör du följande kommandon för att ladda ned certifikatet:

    **antal wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# openssl x509-in DigiCertGlobalRootCA. CRT-informera der-PEM DigiCert_Global_Root_CA. pem**

1. Kör omhash-skriptet för att uppdatera certifikatets ämnes-hashar för de nyligen hämtade certifikaten:

    **# c_rehash**

1. Kör dessa kommandon för att kontrol lera om ämnes-hashar som symlinks har skapats för certifikaten:

    - Kommandoprompt

        **# ls-l | grep-Baltimore**

    - Resultat:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - Kommandoprompt

        **# ls-l | grep-VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - Resultat:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - Kommandoprompt

        **# ls-l | grep-DigiCert_Global_Root**

    - Resultat:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. Skapa en kopia av filen VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem med fil namnet b204d74a. 0:

    **# CP VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem b204d74a. 0**

1. Skapa en kopia av filen Baltimore_CyberTrust_Root. pem med fil namnet 653b494a. 0:

    **# CP Baltimore_CyberTrust_Root. pem 653b494a. 0**

1. Skapa en kopia av filen DigiCert_Global_Root_CA. pem med fil namnet 3513523f. 0:

    **# CP DigiCert_Global_Root_CA. pem 3513523f. 0**

1. Kontrol lera om filerna finns:

    - Kommandoprompt

        **# ls-l 653b494a. 0 b204d74a. 0 3513523f. 0**

    - Resultat

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Utgående anslutning för Site Recovery-webbadresser eller IP-intervall (felkod: 151037 eller 151072)

För att Site Recovery replikering ska fungera krävs utgående anslutning från den virtuella datorn till vissa URL-adresser eller IP-intervall. Om den virtuella datorn finns bakom en brandvägg eller använder regler för nätverkssäkerhetsgrupper (NSG) för att styra utgående anslutningar, kan du står inför ett av de här problemen.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problem 1: det gick inte att registrera den virtuella Azure-datorn med Site Recovery (felkod 151195)

#### <a name="possible-cause"></a>Möjlig orsak 

Det går inte att upprätta anslutningen till Site Recovery slut punkter på grund av ett DNS-matchnings problem.

Det här problemet uppstår ofta när du har växlat över den virtuella datorn men det går inte att komma åt DNS-servern från en katastrof återställnings region (DR).

#### <a name="fix-the-problem"></a>Åtgärda problemet

Om du använder en anpassad DNS måste du kontrol lera att DNS-servern är tillgänglig från området för katastrof återställning. Om du vill ta reda på om du har en anpassad DNS på den virtuella datorn går du till *Disaster Recovery network* > **DNS-servrar**.

![Lista med anpassade DNS-servrar](./media/azure-to-azure-troubleshoot-errors/custom_dns.PNG)

Försök att öppna DNS-servern från den virtuella datorn. Om servern inte är tillgänglig kan du göra den tillgänglig antingen genom att redundansväxla DNS-servern eller genom att skapa en webbplats som är mellan DR-nätverket och DNS.

### <a name="issue-2-site-recovery-configuration-failed-error-code-151196"></a>Problem 2: Site Recovery konfigurationen misslyckades (felkod 151196)

#### <a name="possible-cause"></a>Möjlig orsak

Det går inte att upprätta en anslutning till Office 365-autentisering och identitet IP4-slutpunkter.

#### <a name="fix-the-problem"></a>Åtgärda problemet

Site Recovery kräver åtkomst till Office 365 IP-intervall för autentisering.
Om du använder Azure NSG-regler eller brand Väggs-proxy för att kontrol lera utgående nätverks anslutning på den virtuella datorn, se till att du tillåter kommunikation till Office 365 IP-intervall. Skapa en NSG-regel baserat på en [Azure Active Directory (Azure AD)-service tag](../virtual-network/security-overview.md#service-tags)som ger åtkomst till alla IP-adresser som motsvarar Azure AD. Om nya adresser läggs till i Azure AD i framtiden måste du skapa nya NSG-regler.

> [!NOTE]
> Om de virtuella datorerna ligger bakom en intern belastningsutjämnare som är *standard* har belastningsutjämnaren som standard inte åtkomst till IP-intervall för Office 365 (det vill säga login.microsoftonline.com). Ändra antingen den interna belastnings Utjämnings typen till *grundläggande* eller skapa utgående åtkomst enligt beskrivningen i artikeln [Konfigurera belastnings utjämning och utgående regler](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-error-code-151197"></a>Problem 3: Site Recovery konfiguration misslyckades (felkod 151197)

#### <a name="possible-cause"></a>Möjlig orsak

Det går inte att upprätta anslutningen till Site Recovery tjänst slut punkter.

#### <a name="fix-the-problem"></a>Åtgärda problemet

Site Recovery kräver åtkomst till [Site Recovery IP-intervall](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges), beroende på region. Kontrol lera att de IP-adressintervall som krävs är tillgängliga från den virtuella datorn.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-an-on-premises-proxy-server-error-code-151072"></a>Problem 4: Azure-till-Azure-replikeringen misslyckades när nätverks trafiken går via en lokal proxyserver (felkod 151072)

#### <a name="possible-cause"></a>Möjlig orsak

Inställningarna för den anpassade proxyn är ogiltiga och Site Recovery mobilitets tjänst agenten kunde inte automatiskt identifiera proxyinställningarna från Internet Explorer.

#### <a name="fix-the-problem"></a>Åtgärda problemet

Mobilitets tjänst agenten identifierar proxyinställningarna från Internet Explorer på Windows och från/etc/Environment i Linux.

Om du föredrar att bara ange proxyn för mobilitets tjänsten kan du ange proxyinformation i filen ProxyInfo. conf på följande platser:

- **Linux**:/usr/local/InMage/config/
- **Windows**: C:\Programdata\Microsoft Azure Site Recovery\Config

I ProxyInfo. conf anger du proxyinställningarna i följande initierings fil format:

> [*proxy*]

> Adress = *http://1.2.3.4*

> Port =*567*


> [!NOTE]
> Site Recovery Mobility Service Agent stöder bara *oautentiserade proxyservrar*.

### <a name="more-information"></a>Mer information

Följ anvisningarna i [om nätverk i Azure till Azure-replikering](site-recovery-azure-to-azure-networking-guidance.md)för att ange [obligatoriska URL: er](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) eller [obligatoriska IP-intervall](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Hittades inte på datorn (felkod 150039)

En ny disk som är ansluten till den virtuella datorn måste initieras. Om disken inte hittas visas följande meddelande:

> "Azure data disk *DiskName* *DiskURI* med Logical Unit Number *LUN* - *LUNValue* mappades inte till en motsvarande disk som RAPPORTERades från den virtuella datorn med samma LUN-värde.

### <a name="possible-causes"></a>Möjliga orsaker

- En ny datadisk har kopplats till den virtuella datorn men initierades inte.
- Datadisken i den virtuella datorn rapporterar inte korrekt Logical Unit Number (LUN)-värdet som disken var kopplad till den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

Kontrol lera att data diskarna har initierats och försök sedan igen.

- **Windows**: [Anslut och initiera en ny disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).

- **Linux**: [initiera en ny datadisk i Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Kontakta supporten om problemet kvarstår.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>En eller flera diskar är tillgängliga för skydd (felkod 153039)

### <a name="possible-causes"></a>Möjliga orsaker

- En eller flera diskar har nyligen lagts till på den virtuella datorn efter skyddet.
- En eller flera diskar initierades efter skyddet av den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

Om du vill göra replikeringsstatus för den virtuella datorn felfritt igen kan du välja att skydda diskarna eller stänga av varningen.

#### <a name="to-protect-the-disks"></a>Skydda diskarna

1. Gå till **replikerade objekt** > *VM-namn* > **diskar**.
1. Välj den oskyddade disken och välj sedan **Aktivera replikering**:

    ![Aktivera replikering på VM-diskar](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>Ignorera varningen

1. Gå till **replikerade objekt** > *VM-namn*.
1. Välj varningen i avsnittet **Översikt** och välj sedan **OK**.

    ![Ignorera varning om ny disk](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>Ta bort den virtuella datorn från valvet som slutfördes med information (felkod 150225)

När den virtuella datorn skyddas, skapar Site Recovery några länkar på den virtuella käll datorn. När du tar bort skyddet eller inaktiverar replikering tar Site Recovery bort dessa länkar som en del av rensnings jobbet. Om den virtuella datorn har ett resurs lås slutförs rensnings jobbet med informationen. Informationen anger att den virtuella datorn har tagits bort från Recovery Services valvet, men att vissa av de inaktuella länkarna inte kunde rensas på käll datorn.

Du kan ignorera den här varningen om du aldrig planerar att skydda den här virtuella datorn igen. Men om du vill skydda den här virtuella datorn senare följer du stegen under "åtgärda problemet" för att rensa länkarna.

> [!WARNING]
> Om du inte gör rensningen:
>
> - När du aktiverar replikering med hjälp av Recovery Services valvet visas inte den virtuella datorn.
> - Om du försöker skydda den virtuella datorn med hjälp av den **virtuella datorns** > **Inställningar** > **haveri beredskap**, Miss Miss fungerar åtgärden med meddelandet "det går inte att aktivera replikering på grund av befintliga INAKTUELLA resurs länkar på den virtuella datorn".

### <a name="fix-the-problem"></a>Åtgärda problemet

> [!NOTE]
> Site Recovery inte tar bort den virtuella käll datorn eller påverkar den på något sätt när du utför de här stegen.

1. Ta bort låset från den virtuella datorn eller resurs gruppen för den virtuella datorn. I följande bild måste till exempel resurs låset på den virtuella datorn med namnet "MoveDemo" tas bort:

    ![Ta bort lås från virtuell dator](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Hämta skriptet för att [ta bort en föråldrad Site Recovery-konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Kör skriptet, som kallas Cleanup-stale-asr-config-Azure-VM. ps1. Ange prenumerations-ID, resurs grupp för virtuell dator och VM-namn som parametrar.
1. Om du uppmanas att ange Azure-autentiseringsuppgifter anger du dem. Kontrol lera sedan att skriptet körs utan problem.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>Det går inte att aktivera replikering på grund av inaktuella resurs länkar på den virtuella datorn (felkod 150226)

### <a name="possible-cause"></a>Möjlig orsak

Den virtuella datorn har en inaktuell konfiguration från föregående Site Recovery skydd.

En föråldrad konfiguration kan ske på en virtuell Azure-dator om du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och sedan:

- Du har inaktiverat replikering, men den virtuella käll datorn hade ett resurs lås.
- Du har tagit bort Site Recovery valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har tagit bort resurs gruppen som innehåller Site Recovery valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

> [!NOTE]
> Site Recovery inte tar bort den virtuella käll datorn eller påverkar den på något sätt när du utför de här stegen.

1. Ta bort låset från den virtuella datorn eller resurs gruppen för den virtuella datorn. I följande bild måste till exempel resurs låset på den virtuella datorn med namnet "MoveDemo" tas bort:

    ![Ta bort lås från virtuell dator](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Hämta skriptet för att [ta bort en föråldrad Site Recovery-konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Kör skriptet, som kallas Cleanup-stale-asr-config-Azure-VM. ps1. Ange prenumerations-ID, resurs grupp för virtuell dator och VM-namn som parametrar.
1. Om du uppmanas att ange Azure-autentiseringsuppgifter anger du dem. Kontrol lera sedan att skriptet körs utan problem.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>Det går inte att visa den virtuella Azure-datorn eller resurs gruppen för valet i jobbet "Aktivera replikering"

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>Orsak 1: resurs gruppen och den virtuella käll datorn finns på olika platser

Site Recovery kräver för närvarande resurs gruppen för käll regionen och de virtuella datorerna på samma plats. Om de inte är det kan du inte hitta den virtuella datorn eller resurs gruppen när du försöker tillämpa skyddet.

Som en lösning kan du aktivera replikering från den virtuella datorn i stället för Recovery Services valvet. Gå till **käll-VM** > **Egenskaper** > **haveri beredskap** och aktivera replikeringen.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>Orsak 2: resurs gruppen tillhör inte den valda prenumerationen

Du kanske inte kan hitta resurs gruppen vid tidpunkten för skyddet om resurs gruppen inte ingår i den valda prenumerationen. Kontrol lera att resurs gruppen tillhör den prenumeration som du använder.

### <a name="cause-3-stale-configuration"></a>Orsak 3: inaktuell konfiguration

Du kanske inte ser den virtuella datorn som du vill aktivera för replikering om en inaktuell Site Recovery-konfiguration har lämnats på den virtuella Azure-datorn. Det här tillståndet kan inträffa om du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och sedan:

- Du har tagit bort Site Recovery valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har tagit bort resurs gruppen som innehåller Site Recovery valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har inaktiverat replikering, men den virtuella käll datorn hade ett resurs lås.

### <a name="fix-the-problem"></a>Åtgärda problemet

> [!NOTE]
> Se till att uppdatera modulen "AzureRM. Resources" innan du använder skriptet som nämns i det här avsnittet.  Site Recovery inte tar bort den virtuella käll datorn eller påverkar den på något sätt när du utför de här stegen.

1. Ta bort låset, om det finns, från den virtuella datorn eller resurs gruppen för den virtuella datorn. I följande bild måste till exempel resurs låset på den virtuella datorn med namnet "MoveDemo" tas bort:

    ![Ta bort lås från virtuell dator](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. Hämta skriptet för att [ta bort en föråldrad Site Recovery-konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Kör skriptet, som kallas Cleanup-stale-asr-config-Azure-VM. ps1. Ange prenumerations-ID, resurs grupp för virtuell dator och VM-namn som parametrar.
1. Om du uppmanas att ange Azure-autentiseringsuppgifter anger du dem. Kontrol lera sedan att skriptet körs utan problem.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>Det går inte att välja en virtuell dator för skydd

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>Orsak 1: den virtuella datorn har ett tillägg installerat i ett tillstånd där det inte gick att ange eller inte svarar

Gå till **virtual machines** > **Inställningar** > **tillägg** och Sök efter eventuella tillägg i ett felaktigt tillstånd. Avinstallera eventuella misslyckade tillägg och försök sedan igen för att skydda den virtuella datorn.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>Orsak 2: etablerings statusen för den virtuella datorn är inte giltig

Se fel söknings stegen i [etablerings statusen för den virtuella datorn är inte giltig](#the-vms-provisioning-state-is-not-valid-error-code-150019)längre fram i den här artikeln.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>Etablerings statusen för den virtuella datorn är inte giltig (felkod 150019)

Om du vill aktivera replikering på den virtuella datorn måste etablerings statusen vara **klar**. Följ de här stegen för att kontrol lera etablerings statusen:

1. I Azure Portal väljer du **Resursläsaren** från **alla tjänster**.
1. Expandera listan **prenumerationer** och välj din prenumeration.
1. Expandera listan **ResourceGroups** och välj den virtuella datorns resurs grupp.
1. Expandera listan **resurser** och välj den virtuella datorn.
1. Kontrol lera fältet **provisioningState** i instans visning på höger sida.

### <a name="fix-the-problem"></a>Åtgärda problemet

- Om **ProvisioningState** **Miss lyckas**kan du kontakta supporten med information om fel sökning.
- Om **ProvisioningState** **uppdateras**kan ett annat tillägg distribueras. Kontrol lera om det finns pågående åtgärder på den virtuella datorn, vänta tills de har slutförts och försök sedan att aktivera replikering för det Site Recovery misslyckade jobbet.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>Det går inte att välja den virtuella mål datorn (fliken nätverks val är inte tillgänglig)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Orsak 1: din virtuella dator är ansluten till ett nätverk som redan har mappats till ett mål nätverk

Om den virtuella käll datorn ingår i ett virtuellt nätverk och en annan virtuell dator från samma virtuella nätverk redan har mappats till ett nätverk i mål resurs gruppen, är List rutan nätverks val inte tillgänglig (visas nedtonad) som standard.

![Listan över nätverks val är inte tillgänglig](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>Orsak 2: du har tidigare skyddat den virtuella datorn med hjälp av Site Recovery och sedan inaktiverade replikeringen

Om du inaktiverar replikering av en virtuell dator tas inte nätverks mappningen bort. Mappningen måste tas bort från det Recovery Services valv där den virtuella datorn skyddades. Gå till *Recovery Services valv* > **Site Recovery infrastruktur** > **nätverks mappning**.

![Ta bort nätverks mappning](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

Mål nätverket som konfigurerades under haveri beredskap kan ändras efter den första installationen, efter att den virtuella datorn har skyddats:

![Ändra nätverks mappning](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

Observera att ändringar av nätverks mappning påverkar alla skyddade virtuella datorer som använder samma nätverks mappning.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>Fel i tjänsten COM+ eller tjänsten Volume Shadow Copy (felkod 151025)

När det här felet uppstår visas följande meddelande:

> "Det gick inte att installera Site Recovery tillägget"

### <a name="possible-causes"></a>Möjliga orsaker

- COM+-system tillämpnings tjänsten är inaktive rad.
- Tjänsten Volume Shadow Copy är inaktive rad.

### <a name="fix-the-problem"></a>Åtgärda problemet

Ange COM+-systemprogram och Volume Shadow Copy-tjänster till automatiskt eller manuellt start läge.

1. Öppna konsolen tjänster i Windows.
1. Kontrol lera att COM+-system programmet och Volume Shadow Copy-tjänsterna inte är inställda på att **inaktive ras** som **Start metod**.

    ![Kontrol lera starttyp för COM+-system program och Volume Shadow Copy-tjänster](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Hanterad disk storlek stöds inte (felkod 150172)

När det här felet uppstår visas följande meddelande:

> "Det gick inte att aktivera skydd för den virtuella datorn eftersom den har *DiskName* med storleken *DiskSize*) * som är mindre än den minsta storlek som stöds 1024 MB."

### <a name="possible-cause"></a>Möjlig orsak

Disken är mindre än den storlek som stöds på 1024 MB.

### <a name="fix-the-problem"></a>Åtgärda problemet

Kontrol lera att disk storleken ligger inom det storleks intervall som stöds och försök sedan igen.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Skyddet aktiverades inte eftersom GRUB-konfigurationen innehåller enhets namnet i stället för UUID (felkod 151126)

### <a name="possible-cause"></a>Möjlig orsak

Konfigurations filerna för Linux-GRUB (/boot/grub/menu.lst ",/boot/grub/grub.cfg,/Boot/grub2/grub.cfg eller/etc/default/grub) kan ange de faktiska enhets namnen i stället för UUID-värden för *rot* -och *återställnings* parametrarna. Site Recovery kräver UUID: er eftersom enhets namn kan ändras. Vid omstart kanske det inte finns någon virtuell dator med samma namn vid redundansväxling, vilket leder till problem.

Följande exempel är rader från GRUB-filer där enhets namn (visas i fetstil) visas i stället för de nödvändiga UUID:

- Fil/Boot/grub2/grub.cfg

  > Linux/Boot/vmlinuz-3.12.49-11-default **root =/dev/sda2** $ {extra_cmdline} **Resume =/dev/sda1** Starter = tyst tyst showopts

- Fil:/boot/grub/menu.lst

  > kernel/Boot/vmlinuz-3.0.101-63-default **root =/dev/sda2** **Resume =/dev/sda1** Starter = tyst crashkernel = 256M-: 128M showopts VGA = 0x314


### <a name="fix-the-problem"></a>Åtgärda problemet

Ersätt varje enhets namn med motsvarande UUID:

1. Hitta enhetens UUID genom att köra kommandot **blkid** ***enhets namn***. Exempel:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Ersätt enhets namnet med dess UUID i formaten **root = UUID**=*UUID* och **Resume = UUID**=*UUID*. Till exempel ser raden från/boot/grub/menu.lst (diskuterad tidigare) ut så här:

    > kernel/Boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **Resume = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** Starter = tyst crashkernel = 256M-: 128M showopts VGA = 0x314

1. Gör om skyddet.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>Det gick inte att aktivera skydd eftersom den enhet som anges i GRUB-konfigurationen inte finns (felkod 151124)

### <a name="possible-cause"></a>Möjlig orsak

Konfigurationsfilerna för GRUB (/boot/grub/menu.lst,/boot/grub/grub.cfg,/Boot/grub2/grub.cfg eller/etc/default/grub) kan innehålla parametrarna *Rd.LVM.lv* eller *rd_LVM_LV*. Dessa parametrar identifierar de LVM-enheter (Logical Volume Manager) som ska identifieras vid start. Om dessa LVM-enheter inte finns startar inte det skyddade systemet och kommer att fastna i Start processen. Samma problem visas också med den virtuella redundansväxlingen. Här följer några exempel:

- Fil:/boot/grub2/grub.cfg på RHEL7:

    > linux16/vmlinuz-3.10.0-957.el7. x86_64 root =/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel = 128M\@64M **Rd. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/byte** RHGB quiet LANG = en_US. UTF-8

- Fil:/etc/default/grub på RHEL7:

    > GRUB_CMDLINE_LINUX = "crashkernel = Auto **Rd. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/växling** rhgb quiet"

- Fil:/boot/grub/menu.lst på RHEL6:

    > kernel/vmlinuz-2.6.32-754.EL6. x86_64 ro root = UUID = 36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US. UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = Auto **rd_LVM_LV = rootvg/lv_root** KEYBOARDTYPE = PC-= US **rd_LVM_LV = rootvg/lv_swap** rd_NO_DM rhgb quiet

I varje exempel visar delen i fetstil att GRUB måste identifiera två LVM-enheter med namnen "rot" och "Växla" från volym gruppen "rootvg".

### <a name="fix-the-problem"></a>Åtgärda problemet

Om LVM-enheten inte finns skapar du den eller tar bort motsvarande parametrar från GRUB-konfigurationsfilerna. Försök sedan igen för att aktivera skyddet.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>En uppdatering av Site Recovery mobilitets tjänsten har avslut ATS med varningar (felkod 151083)

Tjänsten Site Recovery Mobility har många komponenter, en av dessa kallas filter driv rutinen. Filter driv rutinen läses in i system minnet endast under omstarten av systemet. När en mobilitets tjänst uppdatering innehåller ändringar av filter driv rutinen, uppdateras datorn men du ser fortfarande en varning om att vissa korrigeringar kräver en omstart. Varningen visas eftersom filter driv rutins korrigeringarna endast börjar gälla när den nya filter driv rutinen läses in, vilket bara sker under en omstart.

> [!NOTE]
> Detta är endast en varning. Befintlig replikering fortsätter att fungera även efter den nya agent uppdateringen. Du kan välja att starta om när du vill ha fördelarna med den nya filter driv rutinen, men den gamla filter driv rutinen fortsätter att fungera om du inte startar om.
>
> Förutom filter driv rutinen börjar fördelarna med eventuella andra förbättringar och korrigeringar i mobilitets tjänstens uppdatering att gälla utan att behöva starta om.  

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Det gick inte att aktivera skydd eftersom den replik hanterade disken redan finns, utan förväntade taggar, i mål resurs gruppen (felkod 150161)

### <a name="possible-cause"></a>Möjlig orsak

Det här problemet kan uppstå om den virtuella datorn tidigare skyddades, och när replikeringen inaktiverades, rensades inte replik disken.

### <a name="fix-the-problem"></a>Åtgärda problemet

Ta bort replik disken som identifierats i fel meddelandet och försök igen med det misslyckade skydds jobbet.

## <a name="next-steps"></a>Nästa steg

[Replikera virtuella Azure-datorer](site-recovery-replicate-azure-to-azure.md)
