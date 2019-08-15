---
title: Azure Site Recovery felsökning för Azure till Azure-replikeringsproblem och fel | Microsoft Docs
description: Felsöka fel och problem vid replikering av virtuella datorer i Azure för haveriberedskap
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: 4d8ba44cdd5161a1a5ff108837cb57af4cd98835
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034796"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Felsöka problem med Azure till Azure VM-replikering

Den här artikeln beskriver vanliga problem i Azure Site Recovery när replikera och återställa virtuella Azure-datorer från en region till en annan region och förklarar hur du felsöker dem. Mer information om konfigurationer som stöds finns i den [stöd matrix för att replikera virtuella Azure-datorer](site-recovery-support-matrix-azure-to-azure.md).

## <a name="list-of-errors"></a>Lista över fel
- **[Problem med resurs kvoter i Azure (felkod 150097)](#azure-resource-quota-issues-error-code-150097)**
- **[Betrodda rot certifikat (felkod 151066)](#trusted-root-certificates-error-code-151066)**
- **[Utgående anslutning för Site Recovery (felkod 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)**

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problem med resurs kvoter i Azure (felkod 150097)
Prenumerationen måste vara aktiverat för att skapa virtuella Azure-datorer i målregionen som du tänker använda som din region för disaster recovery. Din prenumeration bör också ha tillräckligt många aktiverat för att skapa virtuella datorer med specifik storlek. Som standard väljer Site Recovery samma storlek för den Virtuella måldatorn som virtuella datorn. Om matchande storleken är inte tillgängligt plockas närmaste möjliga storlek automatiskt. Om det finns ingen matchande storlek som stöder Virtuella källdatorkonfigurationen, visas det här felmeddelandet:

**Felkod** | **Möjliga orsaker** | **Rekommendationen**
--- | --- | ---
150097<br></br>**Meddelande**: Det gick inte att aktivera replikering för den virtuella datorns VmName. | -Ditt prenumerations-ID kanske inte är aktiverat för att skapa virtuella datorer på målplatsen för regionen.</br></br>-Ditt prenumerations-ID kanske inte är aktiverat eller har inte tillräcklig kvot för att skapa specifika VM-storlekar på målplatsen för regionen.</br></br>– En lämplig VM-storlek som matchar källan VM NIC antalet (2) är inte att hitta målet för prenumerations-ID på målplatsen för regionen.| Kontakta [Azure faktureringshjälp](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) att skapa virtuella datorer med storlekarna som krävs på målplatsen för din prenumeration. När den är aktiverad, försök igen.

### <a name="fix-the-problem"></a>Åtgärda problemet
Du kan kontakta [Azure faktureringshjälp](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) att aktivera din prenumeration för att skapa virtuella datorer av nödvändiga storlekar på målplatsen.

Om målplatsen har en begränsning på kapacitet, inaktiverar du replikering och aktivera det på en annan plats där din prenumeration har tillräckligt stor kvot för att skapa virtuella datorer av nödvändiga storlekar.

## <a name="trusted-root-certificates-error-code-151066"></a>Betrodda rot certifikat (felkod 151066)

Om alla de senaste betrodda rotcertifikaten inte finns på den virtuella datorn, kan ”aktivera replikering” jobbet misslyckas. Utan certifikat misslyckas autentisering och auktorisering av Site Recovery-tjänst-anrop från den virtuella datorn. Felmeddelandet för det misslyckade jobbet för ”Aktivera replikering” Site Recovery visas:

**Felkod** | **Möjlig orsak** | **Rekommendationer**
--- | --- | ---
151066<br></br>**Meddelande**: Site Recovery konfigurationen misslyckades. | De begärda betrodda rotcertifikaten för auktorisering och autentisering inte finns på datorn. | – Se till att det finns betrodda rotcertifikat på datorn för en virtuell dator som kör operativsystemet Windows. Mer information finns i [konfigurera betrodda rotcertifikat och otillåtna certifikat](https://technet.microsoft.com/library/dn265983.aspx).<br></br>– För en virtuell dator som kör Linux-operativsystem, följer du anvisningarna för betrodda rotcertifikat som publicerats av distributören Linux operativsystemets version.

### <a name="fix-the-problem"></a>Åtgärda problemet
**Windows**

Installera alla de senaste Windows-uppdateringarna på den virtuella datorn så att alla betrodda rotcertifikat finns på datorn. Om du arbetar i en frånkopplad miljö, följer du Windows update standardprocessen i din organisation för att hämta certifikaten. Om certifikat som krävs inte finns på den virtuella datorn kan misslyckas anrop till Site Recovery-tjänsten av säkerhetsskäl.

Följ vanliga Windows update management eller certifikat management uppdateringsprocessen i din organisation att få de senaste rotcertifikaten och listan över återkallade certifikat uppdaterade certifikatet på de virtuella datorerna.

Kontrollera att problemet är löst genom att gå till login.microsoftonline.com från en webbläsare i den virtuella datorn.

**Linux**

Följ anvisningarna som tillhandahålls av Linux-distributören för att få de senaste betrodda rotcertifikaten och senaste listan över återkallade certifikat på den virtuella datorn.

Eftersom SuSE Linux använder symlinks för att underhålla en lista över certifikat, gör du följande:

1.  Logga in som en rotanvändare.

2.  Kör detta kommando för att ändra katalogen.

      ``# cd /etc/ssl/certs``

1. Kontrollera om Symantec CA: N rotcertifikatet finns.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. Om Symantec CA: N rotcertifikatet inte finns, kör du följande kommando för att hämta filen. Kontrollera om några fel och följ rekommenderade åtgärden för nätverksfel.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. Kontrollera om Baltimore rot-CA-certifikat finns.

      ``# ls Baltimore_CyberTrust_Root.pem``

4. Hämta certifikatet om Baltimore rot-CA-certifikat inte hittas.  

    ``# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. Kontrollera om det finns DigiCert_Global_Root_CA-certifikat.

    ``# ls DigiCert_Global_Root_CA.pem``

6. Om DigiCert_Global_Root_CA inte hittas, kör du följande kommandon för att hämta certifikatet.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. Kör rehash skript för att uppdatera certifikatet ämne hashvärden för de nyligen hämtade certifikat.

    ``# c_rehash``

8.  Kontrollera om ämnet hashar som symlinks skapas för certifikaten.

    - Kommando

      ``# ls -l | grep Baltimore``

    - Resultat

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Kommando

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Resultat

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Kommando

      ``# ls -l | grep DigiCert_Global_Root``

    - Resultat

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  Skapa en kopia av filen VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem med filename b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. Skapa en kopia av filen Baltimore_CyberTrust_Root.pem med filename 653b494a.0

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Skapa en kopia av filen DigiCert_Global_Root_CA.pem med filename 3513523f.0

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Kontrollera om filerna finns.  

    - Kommando

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Resultat

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Utgående anslutning för Site Recovery-webbadresser eller IP-intervall (felkod: 151037 eller 151072)

För Site Recovery-replikering till arbete, utgående anslutning till specifika URL: er eller IP-intervall krävs från den virtuella datorn. Om den virtuella datorn finns bakom en brandvägg eller använder regler för nätverkssäkerhetsgrupper (NSG) för att styra utgående anslutningar, kan du står inför ett av de här problemen.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Ärende 1: Det gick inte att registrera den virtuella Azure-datorn med Site Recovery (151195) </br>
- **Möjlig orsak** </br>
  - Det går inte att upprätta en anslutning till Site Recovery slut punkter på grund av ett DNS-matchningsfel.
  - Detta visas oftare vid nytt skydd när du har misslyckats under den virtuella datorn men DNS-servern kan inte nås från regionen för Haveriberedskap.

- **Lösning**
   - Om du använder anpassade DNS Se till att är DNS-servern tillgänglig från regionen för Haveriberedskap. Kontrollera om du har en anpassad DNS går du till den virtuella datorn > Disaster Recovery-nätverket > DNS-servrar. Försök att öppna DNS-servern från den virtuella datorn. Om den inte är tillgänglig sedan när du gör det tillgängligt genom att antingen växling över DNS-server eller skapa verksamhetsspecifika plats mellan DR-nätverk och DNS.

    ![COM-fel](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Ärende 2: Site Recovery konfiguration misslyckades (151196)
- **Möjlig orsak** </br>
  - Att går inte ansluta till Office 365-autentisering och identitet IP4-slutpunkter.

- **Lösning**
  - Azure Site Recovery krävs åtkomst till Office 365-IP-intervall för autentisering.
    Om du använder Azure Network security group (NSG) regler/brandväggsproxy för att styra utgående nätverksanslutning på den virtuella datorn, kontrollerar du att du tillåter kommunikation till IP-intervall för O365. Skapa en [Azure Active Directory (AAD) tjänsttagg](../virtual-network/security-overview.md#service-tags) baserat NSG-regel för att tillåta åtkomst till alla IP-adresser för AAD
      - Om nya adresser läggs till Azure Active Directory (AAD) i framtiden, måste du skapa nya NSG-regler.

> [!NOTE]
> Om de virtuella datorerna ligger bakom den interna **standard** belastnings utjämningen skulle den inte ha åtkomst till O365-IP: er, dvs. login.microsoftonline.com som standard. Ändra den till **grundläggande** typ av belastningsutjämnare eller skapa en gräns för åtkomst som anges i [artikeln](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Site Recovery konfiguration misslyckades (151197)
- **Möjlig orsak** </br>
  - Att går inte ansluta till Azure Site Recovery-Tjänsteslutpunkter.

- **Lösning**
  - Azure Site Recovery krävs för åtkomst till [Site Recovery IP-intervall](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) beroende på regionen. Kontrollera att som krävs för ip-intervall som är tillgängliga från den virtuella datorn.


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Ärende 4: A2A-replikering misslyckades när nätverks trafiken går genom lokal proxyserver (151072)
- **Möjlig orsak** </br>
  - Inställningarna för den anpassade proxyn är ogiltiga och Azure Site Recovery mobilitets tjänst agenten automatiskt identifierade proxyinställningarna från IE


- **Lösning**
  1. Mobilitetstjänstagenten identifierar proxyinställningar från Internet Explorer på Windows och /etc/environment i Linux.
  2. Om du föredrar att bara ange proxy för Azure Site Recovery mobilitets tjänst kan du ange proxyinformation i ProxyInfo. conf på:</br>
     - ``/usr/local/InMage/config/`` på ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` på ***Windows***
  3. ProxyInfo.conf ska ha rätt proxyinställningar har följande INI-format.</br>
                *[proxy]*</br>
                *Adress =http://1.2.3.4*</br>
                *Port = 567*</br>
  4. Azure Site Recovery Mobility Service Agent stöder bara ***oautentiserade proxyservrar***.


### <a name="fix-the-problem"></a>Åtgärda problemet
Om du vill tillåta [nödvändiga URL: er](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) eller de [IP-adressintervall som krävs](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)följer du stegen i [dokumentet om nätverks vägledning](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Hittades inte på datorn (felkod 150039)

En ny disk som är ansluten till den virtuella datorn måste initieras.

**Felkod** | **Möjliga orsaker** | **Rekommendationer**
--- | --- | ---
150039<br></br>**Meddelande**: Azure data disk (DiskName) (DiskURI) med Logical Unit Number (LUN) (LUNValue) har inte mappats till en motsvarande disk som rapporterades från den virtuella datorn med samma LUN-värde. | – En ny datadisk har anslutits till den virtuella datorn men den har inte initierats.</br></br>-Datadisken i den virtuella datorn rapporterar felaktigt det LUN-värde som disken anslöts med till den virtuella datorn.| Kontrollera att datadiskarna har initierats och försök sedan igen.</br></br>För Windows: [Anslut och initiera en ny disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>För Linux: [Initiera en ny data disk i Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Åtgärda problemet
Kontrollera att datadiskarna har initierats och försök sedan igen:

- För Windows: [Anslut och initiera en ny disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- För Linux: [lägga till en ny datadisk i Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Kontakta supporten om problemet kvarstår.

## <a name="one-or-more-disks-are-available-for-protectionerror-code-153039"></a>En eller flera diskar är tillgängliga för skydd (felkod 153039)
- **Möjlig orsak** </br>
  - om en eller flera diskar nyligen har lagts till i den virtuella datorn efter skyddet. 
  - om en eller flera disk (er) initierades senare efter skyddet av den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet
Du kan antingen välja att skydda diskarna eller ignorera varningen för att göra den virtuella datorns replikeringsstatus felfritt igen.</br>
1. För att skydda diskarna. Navigera till replikerade objekt > virtuella datorer > diskar > Klicka på oskyddad disk > Aktivera replikering.
 ![add_disks](./media/azure-to-azure-troubleshoot-errors/add-disk.png)
2. För att ignorera varningen. Gå till replikerade objekt > VM > Klicka på aviseringen Stäng under översikt.
![dismiss_warning](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)


## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information--error-code-150225"></a>Ta bort den virtuella datorn från valvet som slutfördes med information (felkod 150225)
Vid tidpunkten för att skydda den virtuella datorn skapar Azure Site Recovery några länkar på den virtuella käll datorn. När du tar bort skyddet eller inaktiverar replikeringen Azure Site Recovery du ta bort dessa länkar som en del av rensnings jobbet. Om den virtuella datorn har ett resurs lås slutförs jobbet med informationen. Det anger att den virtuella datorn har tagits bort från Recovery Services-valvet men att vissa av de inaktuella länkarna inte kunde rensas från käll datorn.

Du kan ignorera den här varningen om du aldrig planerar att skydda den här virtuella datorn igen i framtiden. Men om du behöver skydda den virtuella datorn senare bör du rensa länkarna som anges i stegen nedan. 

**Om du inte gör det rensar du:**

1.  Under tiden då replikeringen aktiverades via Recovery Services-valvet visas inte den virtuella datorn. 
2.  Om du försöker skydda den virtuella datorn via den **virtuella datorn > inställningar > haveri beredskap** Miss lyckas det med felet "det*går inte att aktivera replikering på grund av befintliga inaktuella resurs länkar på den virtuella*datorn".


### <a name="fix-the-problem"></a>Åtgärda problemet

>[!NOTE]
>
>Azure Site Recovery inte tar bort den virtuella käll datorn eller påverkar den på något sätt medan du utför stegen nedan.
>

1. Ta bort låset från den virtuella datorn eller resurs gruppen för den virtuella datorn. Exempel: Under VM-namnet "MoveDemo" har resurs låset som måste tas bort.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Hämta skriptet [ta bort inaktuell Azure Site Recovery konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Kör skriptet *Cleanup-Stale-ASR-config-Azure-VM. ps1*.
4. Ange prenumerations-ID, resurs grupp för virtuell dator och VM-namn som en parameter.
5. Om du har begärt Azure-autentiseringsuppgifter anger du det och kontrollerar att skriptet körs utan några problem. 


## <a name="replication-cannot-be-enabled-because-of-the-existing-stale-resource-links-on-the-vm-error-code-150226"></a>Det går inte att aktivera replikering på grund av befintliga inaktuella resurs länkar på den virtuella datorn (felkod 150226)

**Orsak: Den virtuella datorn har inaktuell konfiguration kvar från tidigare Site Recovery skydd**

Den inaktuella konfigurationen kan finnas kvar i en Azure-dator i följande fall:

- Du har aktiverat replikering för den virtuella Azure-datorn genom att använda Site Recovery och sedan inaktivera replikering, men den **virtuella käll datorn hade ett resurs lås**.
- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och tas sedan bort valvet för Site Recovery utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och tas sedan bort resursgruppen som innehåller Site Recovery-valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

>[!NOTE]
>
>Azure Site Recovery inte tar bort den virtuella käll datorn eller påverkar den på något sätt medan du utför stegen nedan.


1. Ta bort låset från den virtuella datorn eller resurs gruppen för den virtuella datorn, om det finns några. *Till exempel:* Under VM-namnet "MoveDemo" har resurs låset som måste tas bort.
   
   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Hämta skriptet [ta bort inaktuell Azure Site Recovery konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Kör skriptet *Cleanup-Stale-ASR-config-Azure-VM. ps1*.
4. Ange prenumerations-ID, resurs grupp för virtuell dator och VM-namn som en parameter.
5. Om du har begärt Azure-autentiseringsuppgifter anger du det och kontrollerar att skriptet körs utan några problem.  

## <a name="unable-to-see-the-azure-vm-or-resource-group--for-selection-in-enable-replication"></a>Det går inte att visa den virtuella Azure-datorn eller resurs gruppen för val i "Aktivera replikering"

 **Orsak 1:  Resurs gruppen och den virtuella käll datorn finns på olika platser**
 
Azure Site Recovery för närvarande uppdrag som resurs gruppen för käll regionen och de virtuella datorerna ska finnas på samma plats. Om detta inte är fallet kan du inte hitta den virtuella datorn eller resurs gruppen under skydds tiden. 

**Som en lösning**kan du aktivera replikering från den virtuella datorn i stället för Recovery Services-valvet. Gå till käll-VM > Egenskaper > haveri beredskap och aktivera replikeringen.

**Orsak 2: Resurs gruppen tillhör inte den valda prenumerationen**

Du kanske inte att hitta resursgruppen vid tidpunkten för skydd om det inte är en del av den givna prenumerationen. Kontrollera att resursgruppen tillhör prenumerationen som används.

 **Orsak 3: Föråldrad konfiguration**
 
Om du inte ser den virtuella datorn som du vill aktivera för replikering, kan det på grund av en inaktuell konfiguration av Site Recovery bli virtuella Azure-datorn. Den inaktuella konfigurationen kan finnas kvar i en Azure-dator i följande fall:

- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och tas sedan bort valvet för Site Recovery utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och tas sedan bort resursgruppen som innehåller Site Recovery-valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.

- Du har aktiverat replikering för den virtuella Azure-datorn genom att använda Site Recovery och sedan inaktivera replikering, men den virtuella käll datorn hade ett resurs lås.

### <a name="fix-the-problem"></a>Åtgärda problemet

> [!NOTE]
>
> Se till att uppdatera modulen "" AzureRM. Resources "innan du använder skriptet nedan. Azure Site Recovery inte tar bort den virtuella käll datorn eller påverkar den på något sätt medan du utför stegen nedan.
>

1. Ta bort låset från den virtuella datorn eller resurs gruppen för den virtuella datorn, om det finns några. *Till exempel:* Under VM-namnet "MoveDemo" har resurs låset som måste tas bort.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. Hämta skript [ta bort inaktuell konfiguration](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
3. Kör skriptet *Cleanup-Stale-ASR-config-Azure-VM. ps1*.
4. Ange prenumerations-ID, resurs grupp för virtuell dator och VM-namn som en parameter.
5. Om du har begärt Azure-autentiseringsuppgifter anger du det och kontrollerar att skriptet körs utan några problem.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Det går inte att välja virtuell dator för skydd
 **Orsak 1:  En del tillägg har installerats i ett tillstånd som misslyckats eller inte svarar på den virtuella datorn** <br>
 Gå till virtuella datorer > inställningen > tillägg och kontrollera om det finns några tillägg i ett felaktigt tillstånd. Avinstallera tillägget misslyckades och försök sedan skydda den virtuella datorn.<br>
 **Orsak 2:  [Etablerings statusen för den virtuella datorn är inte giltig](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>Virtuella datorns Etableringsstatus är inte giltigt (felkod 150019)

Om du vill aktivera replikering på den virtuella datorn, Etableringsstatus måste vara **lyckades**. Du kan kontrollera tillstånd för virtuell dator genom att följa stegen nedan.

1.  Välj den **Resursläsaren** från **alla tjänster** i Azure-portalen.
2.  Expandera den **prenumerationer** listan och välj din prenumeration.
3.  Expandera den **ResourceGroups** listan och välj resursgruppen för den virtuella datorn.
4.  Expandera den **resurser** listan och välj den virtuella datorn
5.  Kontrollera den **provisioningState** i Instansvy på höger sida.

### <a name="fix-the-problem"></a>Åtgärda problemet

- Om **provisioningState** är **misslyckades**, kontakta supporten med information om hur du felsöker.
- Om **provisioningState** är **uppdaterar**, ytterligare ett tillägg kunde komma distribueras. Kontrollera om det finns några pågående åtgärder på den virtuella datorn, vänta tills de slutförts och försök misslyckade Site Recovery **Aktivera replikering** jobbet.

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>Det går inte att välja mål virtuellt nätverk – fliken för val av nätverk är nedtonat.

**Orsak 1: Om din virtuella dator är ansluten till ett nätverk som redan har mappats till ett mål nätverk.**
- Om den Virtuella källdatorn är en del av ett virtuellt nätverk och en annan virtuell dator från samma virtuella nätverk har redan mappats till ett nätverk i målresursgruppen, kommer sedan av nätverket standardvalet nedrullningsbara att inaktiveras.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**Orsak 2: Om du tidigare har skyddat den virtuella datorn med Azure Site Recovery och inaktiverat replikeringen.**
 - Inaktivera replikering för en virtuell dator tas inte bort mappning av nätverket. Det måste tas bort från det recovery service-valv där den virtuella datorn har skyddats. </br>
 Gå till recovery service-valv > Site Recovery-infrastruktur > nätverksmappning. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - Målnätverket som konfigurerades vid installationen för disaster recovery kan ändras efter det första konfigurera när den virtuella datorn är skyddad. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Observera att om du ändrar nätverksmappning påverkas alla skyddade virtuella datorer som använder den specifika nätverksmappningen.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM +/ fel i tjänsten Volume Shadow Copy (felkod 151025)

**Felkod** | **Möjliga orsaker** | **Rekommendationer**
--- | --- | ---
151025<br></br>**Meddelande**: Det gick inte att installera Site Recovery tillägget | -, COM + System Application-tjänsten är inaktiverad.</br></br>-'Volume Shadow Copy-tjänsten är inaktiverad.| Ange ”COM + System Application” och ”Volume Shadow Copy-tjänster till automatiskt eller manuellt startläge.

### <a name="fix-the-problem"></a>Åtgärda problemet

Du kan öppna ”tjänster”-konsolen och se till att den ”COM + System Application” och ”Volume Shadow Copy” har angetts inte till ”Disabled” för ”starttyp”.
  ![COM-fel](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Hanterad disk storlek stöds inte (felkod 150172)


**Felkod** | **Möjliga orsaker** | **Rekommendationer**
--- | --- | ---
150172<br></br>**Meddelande**: Det gick inte att aktivera skydd för den virtuella datorn eftersom den har (DiskName) med storlek (DiskSize) som är mindre än den minsta storlek som stöds 1024 MB. | -Disken är mindre än den storlek som stöds på 1024 MB| Se till att disk storlekarna ligger inom det storleks intervall som stöds och försök igen.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Det gick inte att aktivera skydd eftersom enhets namnet som anges i GRUB-konfigurationen i stället för UUID (felkod 151126)

**Möjlig orsak:** </br>
Konfigurationsfilerna för GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/Boot/grub2/grub.cfg" eller "/etc/default/grub") kan innehålla värdet för parameter **roten** och återupptas som faktiska enhets namn i stället för uuid. Site Recovery bestämmer UUID-metoden som enhets namn kan ändras vid omstart av den virtuella datorn eftersom den virtuella datorn kanske inte har samma namn på redundansväxling, vilket leder till problem. Exempel: </br>


- Följande rad är från GRUB-filen **/Boot/grub2/grub.cfg**. <br>
  *Linux/Boot/vmlinuz-3.12.49-11-default **root =/dev/sda2** $ {extra_cmdline} **Resume =/dev/sda1** Started = tyst tyst showopts*


- Följande rad är GRUB-fil **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **rot = / dev/sda2** **återuppta = / dev/sda1** stänker = tyst crashkernel = 256M-:128M showopts vga = 0x314*

Om du observerar den feta strängen ovan, har GRUB de faktiska enhets namnen för parametrarna "root" och "Resume" i stället för UUID.

**Så här åtgärdar du:**<br>
Enhets namnen bör ersättas med motsvarande UUID.<br>


1. Hitta enhetens UUID genom att köra kommandot "blkid \<Device name >". Exempel:<br>
   ```
   blkid /dev/sda1
   ```<br>
   ```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
   ```blkid /dev/sda2```<br>
   ```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```<br>
   ```



1. Ersätt nu enhets namnet med dess UUID i formatet "root = UUID =\<UUID >". Om vi till exempel ersätter enhets namnen med UUID för rot-och återställnings parameter som nämns ovan i filerna "/Boot/grub2/grub.cfg", "/Boot/grub2/grub.cfg" eller "/etc/default/grub:, ser raderna i filerna ut som. <br>
   *kernel/Boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **Resume = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** Starter = tyst crashkernel = 256M-: 128M showopts VGA = 0x314*
1. Starta om skyddet igen

## <a name="enable-protection-failed-as-device-mentioned-in-the-grub-configuration-doesnt-existerror-code-151124"></a>Det gick inte att aktivera skydd eftersom enheten som nämns i GRUB-konfigurationen inte finns (felkod 151124)
**Möjlig orsak:** </br>
Konfigurationsfilerna för GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/Boot/grub2/grub.cfg" eller "/etc/default/grub") kan innehålla parametrarna "rd.lvm.lv" eller "rd_LVM_LV" för att ange den LVM-enhet som ska identifieras vid tidpunkten för start. Om dessa LVM-enheter inte finns kommer det skyddade systemet inte att starta och fastna i Start processen. Även om samma kommer att observeras med den virtuella redundansväxlingen. Nedan visas några exempel:

Några exempel: </br>

1. Följande rad är från GRUB-filen **"/Boot/grub2/grub.cfg"** på RHEL7. </br>
   *linux16/vmlinuz-3.10.0-957.el7.x86_64 root =/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel = 128M\@64M **Rd. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/swap** rhgb quiet lang = en_US. UTF-8*</br>
   Den markerade delen visar att GRUB måste identifiera två LVM-enheter med namnen **"rot"** och **"växling"** från volym gruppen "rootvg".
1. Följande rad är från GRUB-filen **"/etc/default/grub"** på RHEL7 </br>
   *GRUB_CMDLINE_LINUX = "crashkernel = Auto **Rd. LVM. lv = rootvg/root Rd. LVM. lv = rootvg/växling** rhgb quiet"*</br>
   Den markerade delen visar att GRUB måste identifiera två LVM-enheter med namnen **"rot"** och **"växling"** från volym gruppen "rootvg".
1. Följande rad är från GRUB-filen **"/boot/grub/menu.lst"** på RHEL6 </br>
   *kernel/vmlinuz-2.6.32-754.EL6.x86_64 ro root = UUID = 36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG = en_US. UTF-8 rd_NO_MD SYSFONT = latarcyrheb-sun16 crashkernel = Auto rd_LVM_LV = rootvg/lv_root KEYBOARDTYPE = PC-= US rd_LVM_LV = rootvg/lv_swap rd_NO_DM rhgb quiet* </br>
   Den markerade delen visar att GRUB måste identifiera två LVM-enheter med namnen **"rot"** och **"växling"** från volym gruppen "rootvg".<br>

**Så här åtgärdar du:**<br>

Om LVM-enheten inte finns, korrigerar du antingen genom att skapa den eller ta bort parametern för samma konfigurations fil för GRUB och sedan försöka aktivera skydd igen. </br>

## <a name="site-recovery-mobility-service-update-completed-with-warnings--error-code-151083"></a>Uppdatering av Site Recovery mobilitets tjänsten har slutförts med varningar (felkod 151083)
Site Recovery mobilitets tjänsten har många komponenter, en av dessa kallas filter driv rutin. Filter driv rutin läses bara in i system minnet i taget vid system omstarten. När det finns Site Recovery mobilitets tjänst uppdateringar som har filter driv rutins ändringar, uppdaterar vi datorn men ändå får du en varning om att vissa korrigeringar kräver en omstart. Det innebär att filter driv rutins korrigeringarna endast kan realiseras när en ny filter driv rutin läses in som bara kan ske vid system start.<br>
**Observera** att detta bara är en varning och att den befintliga replikeringen fortsätter att fungera även efter den nya agent uppdateringen. Du kan välja att starta om varje gång som du vill få fördelarna med den nya filter driv rutinen, men om du inte startar om än även den gamla filter driv rutinen fortsätter att fungera. Utöver filter driv rutinen kommer **fördelarna med andra förbättringar och korrigeringar i mobilitets tjänsten att komma fram utan omstart när agenten uppdateras.**  


## <a name="protection-couldnt-be-enabled-as-replica-managed-disk-diskname-replica-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>Det gick inte att aktivera skydd eftersom den hanterade disken "diskname-Replica" redan finns utan förväntade Taggar i mål resurs gruppen (felkod 150161

**Orsak**: Det kan inträffa om den virtuella datorn var skyddad tidigare tidigare och under inaktive ringen av replikeringen rensades inte replik disken på grund av en orsak.</br>
**Så här åtgärdar du:** Ta bort den angivna replik disken i fel meddelandet och starta om det misslyckade skydds jobbet igen.

## <a name="next-steps"></a>Nästa steg
[Replikera virtuella Azure-datorer](site-recovery-replicate-azure-to-azure.md)
