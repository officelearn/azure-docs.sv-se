---
title: Azure Site Recovery felsökning för Azure till Azure-replikeringsproblem och fel | Microsoft Docs
description: Felsöka fel och problem vid replikering av virtuella datorer i Azure för haveriberedskap
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: sujayt
ms.openlocfilehash: 7d11460fd1db5ba92725567a41aaaeab9e752adb
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308140"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Felsöka problem med Azure till Azure VM-replikering

Den här artikeln beskriver vanliga problem i Azure Site Recovery när replikera och återställa virtuella Azure-datorer från en region till en annan region och förklarar hur du felsöker dem. Mer information om konfigurationer som stöds finns i den [stöd matrix för att replikera virtuella Azure-datorer](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problem med kvoten på Azure-resurs (felkod 150097)
Prenumerationen måste vara aktiverat för att skapa virtuella Azure-datorer i målregionen som du tänker använda som din region för disaster recovery. Din prenumeration bör också ha tillräckligt många aktiverat för att skapa virtuella datorer med specifik storlek. Som standard väljer Site Recovery samma storlek för den Virtuella måldatorn som virtuella datorn. Om matchande storleken är inte tillgängligt plockas närmaste möjliga storlek automatiskt. Om det finns ingen matchande storlek som stöder Virtuella källdatorkonfigurationen, visas det här felmeddelandet:

**Felkod** | **Möjliga orsaker** | **Rekommendationen**
--- | --- | ---
150097<br></br>**Meddelandet**: Det gick inte att aktivera replikering för den virtuella datorn VmName. | -Ditt prenumerations-ID kanske inte är aktiverat för att skapa virtuella datorer på målplatsen för regionen.</br></br>-Ditt prenumerations-ID kanske inte är aktiverat eller har inte tillräcklig kvot för att skapa specifika VM-storlekar på målplatsen för regionen.</br></br>– En lämplig VM-storlek som matchar källan VM NIC antalet (2) är inte att hitta målet för prenumerations-ID på målplatsen för regionen.| Kontakta [Azure faktureringshjälp](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) att skapa virtuella datorer med storlekarna som krävs på målplatsen för din prenumeration. När den är aktiverad, försök igen.

### <a name="fix-the-problem"></a>Åtgärda problemet
Du kan kontakta [Azure faktureringshjälp](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) att aktivera din prenumeration för att skapa virtuella datorer av nödvändiga storlekar på målplatsen.

Om målplatsen har en begränsning på kapacitet, inaktiverar du replikering och aktivera det på en annan plats där din prenumeration har tillräckligt stor kvot för att skapa virtuella datorer av nödvändiga storlekar.

## <a name="trusted-root-certificates-error-code-151066"></a>Betrodda rotcertifikat (felkod 151066)

Om alla de senaste betrodda rotcertifikaten inte finns på den virtuella datorn, kan ”aktivera replikering” jobbet misslyckas. Utan certifikat misslyckas autentisering och auktorisering av Site Recovery-tjänst-anrop från den virtuella datorn. Felmeddelandet för det misslyckade jobbet för ”Aktivera replikering” Site Recovery visas:

**Felkod** | **Möjlig orsak** | **Rekommendationer**
--- | --- | ---
151066<br></br>**Meddelandet**: Site Recovery-konfigurationen misslyckades. | De begärda betrodda rotcertifikaten för auktorisering och autentisering inte finns på datorn. | – Se till att det finns betrodda rotcertifikat på datorn för en virtuell dator som kör operativsystemet Windows. Mer information finns i [konfigurera betrodda rotcertifikat och otillåtna certifikat](https://technet.microsoft.com/library/dn265983.aspx).<br></br>– För en virtuell dator som kör Linux-operativsystem, följer du anvisningarna för betrodda rotcertifikat som publicerats av distributören Linux operativsystemets version.

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

3. Kontrollera om Symantec CA: N rotcertifikatet finns.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4. Om Symantec CA: N rotcertifikatet inte finns, kör du följande kommando för att hämta filen. Kontrollera om några fel och följ rekommenderade åtgärden för nätverksfel.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

5. Kontrollera om Baltimore rot-CA-certifikat finns.

      ``# ls Baltimore_CyberTrust_Root.pem``

6. Hämta certifikatet om Baltimore rot-CA-certifikat inte hittas.  

    ``# wget http://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

7. Kontrollera om det finns DigiCert_Global_Root_CA-certifikat.

    ``# ls DigiCert_Global_Root_CA.pem``

8. Om DigiCert_Global_Root_CA inte hittas, kör du följande kommandon för att hämta certifikatet.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

9. Kör rehash skript för att uppdatera certifikatet ämne hashvärden för de nyligen hämtade certifikat.

    ``# c_rehash``

10. Kontrollera om ämnet hashar som symlinks skapas för certifikaten.

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

11. Skapa en kopia av filen VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem med filename b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

12. Skapa en kopia av filen Baltimore_CyberTrust_Root.pem med filename 653b494a.0

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

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problem 1: Det gick inte att registrera Azure-dator med Site Recovery (151195) </br>
- **Möjlig orsak** </br>
  - Det går inte att upprätta anslutning till site recovery-slutpunkter på grund av DNS-matchningsfel.
  - Detta visas oftare vid nytt skydd när du har misslyckats under den virtuella datorn men DNS-servern kan inte nås från regionen för Haveriberedskap.
  
- **Lösning**
   - Om du använder anpassade DNS Se till att är DNS-servern tillgänglig från regionen för Haveriberedskap. Kontrollera om du har en anpassad DNS går du till den virtuella datorn > Disaster Recovery-nätverket > DNS-servrar. Försök att öppna DNS-servern från den virtuella datorn. Om den inte är tillgänglig sedan när du gör det tillgängligt genom att antingen växling över DNS-server eller skapa verksamhetsspecifika plats mellan DR-nätverk och DNS.
  
    ![COM-fel](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)
 

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Site Recovery-konfigurationen misslyckades (151196)
- **Möjlig orsak** </br>
  - Att går inte ansluta till Office 365-autentisering och identitet IP4-slutpunkter.

- **Lösning**
  - Azure Site Recovery krävs åtkomst till Office 365-IP-intervall för autentisering.
    Om du använder Azure Network security group (NSG) regler/brandväggsproxy för att styra utgående nätverksanslutning på den virtuella datorn, kontrollerar du att du tillåter kommunikation till IP-intervall för O365. Skapa en [Azure Active Directory (AAD) tjänsttagg](../virtual-network/security-overview.md#service-tags) baserat NSG-regel för att tillåta åtkomst till alla IP-adresser för AAD
        - Om nya adresser läggs till Azure Active Directory (AAD) i framtiden, måste du skapa nya NSG-regler.


### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Site Recovery-konfigurationen misslyckades (151197)
- **Möjlig orsak** </br>
  - Att går inte ansluta till Azure Site Recovery-Tjänsteslutpunkter.

- **Lösning**
  - Azure Site Recovery krävs för åtkomst till [Site Recovery IP-intervall](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) beroende på regionen. Kontrollera att som krävs för ip-intervall som är tillgängliga från den virtuella datorn.
    

### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>Problem 4: A2A-replikeringen misslyckades när nätverkstrafiken som går via en lokal proxyserver (151072)
 - **Möjlig orsak** </br>
   - De anpassade proxyinställningarna är ogiltiga och mobilitetstjänstagenten för ASR identifieras inte automatiskt proxyinställningar från Internet Explorer


 - **Lösning**
  1.    Mobilitetstjänstagenten identifierar proxyinställningar från Internet Explorer på Windows och /etc/environment i Linux.
  2.  Om du föredrar att ställa in proxy endast för ASR-Mobilitetstjänsten kan ange du proxyinformationen i ProxyInfo.conf som finns på:</br>
      - ``/usr/local/InMage/config/`` på ***Linux***
      - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` på ***Windows***
  3.    ProxyInfo.conf ska ha rätt proxyinställningar har följande INI-format. </br>
                   *[proxy]*</br>
                   *Adress =http://1.2.3.4*</br>
                   *Port = 567*</br>
  4. ASR mobilitetstjänstagenten stöder endast ***oautentiserade proxyservrar***.

### <a name="fix-the-problem"></a>Åtgärda problemet
Godkänna [de URL: erna](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) eller [krävs för IP-intervall](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), följer du stegen i den [vägledningsdokumentet nätverk om](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Hittades inte på datorn (felkod 150039)

En ny disk som är ansluten till den virtuella datorn måste initieras.

**Felkod** | **Möjliga orsaker** | **Rekommendationer**
--- | --- | ---
150039<br></br>**Meddelandet**: Azure-datadisk (DiskName) (DiskURI) med logiska enhetsnummer (LUN) (LUNValue) inte har kopplats till en disk som rapporterades från den virtuella datorn har samma LUN-värde. | – En ny datadisk har anslutits till den virtuella datorn men den har inte initierats.</br></br>-Datadisken i den virtuella datorn rapporterar felaktigt det LUN-värde som disken anslöts med till den virtuella datorn.| Kontrollera att datadiskarna har initierats och försök sedan igen.</br></br>För Windows: [Anslut och initiera en ny disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>För Linux: [initiera en ny datadisk i Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Åtgärda problemet
Kontrollera att datadiskarna har initierats och försök sedan igen:

- För Windows: [Anslut och initiera en ny disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- För Linux: [lägga till en ny datadisk i Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Kontakta supporten om problemet kvarstår.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>Det går inte att se Azure VM väljas i ”Aktivera replikering”

 **Orsak 1: Resursgrupp och virtuella källdatorn finns på olika platser** <br>
Azure Site Recovery ut för närvarande källresursgruppen för region och virtuella datorer ska finnas på samma plats. Om detta inte är fallet skulle sedan du inte att hitta den virtuella datorn vid tidpunkten för skyddet.

**Orsak 2: Resursgrupp ingår inte i vald prenumeration** <br>
Du kanske inte att hitta resursgruppen vid tidpunkten för skydd om det inte är en del av den givna prenumerationen. Kontrollera att resursgruppen tillhör prenumerationen som används.

 **Orsak 3: Inaktuell konfiguration** <br>
Om du inte ser den virtuella datorn som du vill aktivera för replikering, kan det på grund av en inaktuell konfiguration av Site Recovery bli virtuella Azure-datorn. Den inaktuella konfigurationen kan finnas kvar i en Azure-dator i följande fall:

- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och tas sedan bort valvet för Site Recovery utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och tas sedan bort resursgruppen som innehåller Site Recovery-valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

Du kan använda [ta bort inaktuella ASR-konfigurationsskript](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) och ta bort den inaktuella konfigurationen för Site Recovery på Azure-VM. Du bör kunna se den virtuella datorn när du tar bort den inaktuella konfigurationen.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Det går inte att välja virtuell dator för skydd 
 **Orsak 1: virtuell dator har vissa tillägg som installeras i tillståndet misslyckad eller svarar inte** <br>
 Gå till virtuella datorer > inställningen > tillägg och kontrollera om det finns några tillägg i ett felaktigt tillstånd. Avinstallera tillägget misslyckades och försök sedan skydda den virtuella datorn.<br>
 **Orsak 2: [Virtuella datorns Etableringsstatus är inte giltig](#vms-provisioning-state-is-not-valid-error-code-150019)**

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

**Orsak 1: Om den virtuella datorn är ansluten till ett nätverk som har redan mappats till ett Målnätverk.**
- Om den Virtuella källdatorn är en del av ett virtuellt nätverk och en annan virtuell dator från samma virtuella nätverk har redan mappats till ett nätverk i målresursgruppen, kommer sedan av nätverket standardvalet nedrullningsbara att inaktiveras.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**Orsak 2: Om du tidigare har skyddat den virtuella datorn med hjälp av Azure Site Recovery och inaktiverat replikeringen.**
 - Inaktivera replikering för en virtuell dator tas inte bort mappning av nätverket. Det måste tas bort från det recovery service-valv där den virtuella datorn har skyddats. </br>
 Gå till recovery service-valv > Site Recovery-infrastruktur > nätverksmappning. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - Målnätverket som konfigurerades vid installationen för disaster recovery kan ändras efter det första konfigurera när den virtuella datorn är skyddad. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Observera att om du ändrar nätverksmappning påverkas alla skyddade virtuella datorer som använder den specifika nätverksmappningen.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM +/ fel i tjänsten Volume Shadow Copy (felkod 151025)
**Felkod** | **Möjliga orsaker** | **Rekommendationer**
--- | --- | ---
151025<br></br>**Meddelandet**: Det gick inte att installera Site recovery-tillägget | -, COM + System Application-tjänsten är inaktiverad.</br></br>-'Volume Shadow Copy-tjänsten är inaktiverad.| Ange ”COM + System Application” och ”Volume Shadow Copy-tjänster till automatiskt eller manuellt startläge.

### <a name="fix-the-problem"></a>Åtgärda problemet

Du kan öppna ”tjänster”-konsolen och se till att den ”COM + System Application” och ”Volume Shadow Copy” har angetts inte till ”Disabled” för ”starttyp”.
  ![COM-fel](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="next-steps"></a>Nästa steg
[Replikera virtuella Azure-datorer](site-recovery-replicate-azure-to-azure.md)
