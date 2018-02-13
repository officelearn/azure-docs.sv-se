---
title: "Azure Site Recovery felsökning för Azure-Azure-replikeringsproblem och fel | Microsoft Docs"
description: "Felsökning av fel och problem vid replikering av virtuella Azure-datorer för katastrofåterställning"
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: sujayt
ms.openlocfilehash: be43e34976682847c4756e062ec5b638ebc26063
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Felsökning av problem med Virtuella Azure-Azure-replikering

Den här artikeln beskrivs hur du felsöker dem beskrivs vanliga problem i Azure Site Recovery när replikering och återställa virtuella Azure-datorer från en region till en annan region. Mer information om konfigurationer som stöds finns i [stöd matrix för att replikera virtuella datorer i Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problem med Azure-resurs kvoten (felkod 150097)
Din prenumeration måste vara aktiverat för att skapa virtuella Azure-datorer i mål-region som du tänker använda som disaster recovery region. Din prenumeration bör också ha tillräcklig kvot aktiverad för att skapa virtuella datorer i en viss storlek. Som standard hämtar Site Recovery för mål VM samma storlek som den Virtuella källdatorn. Om matchande storlek inte är tillgänglig, hämtas den närmaste möjliga storleken automatiskt. Om det finns ingen matchande storlek som stöder VM-konfiguration för datakällan, visas detta felmeddelande:

Felkod | **Möjliga orsaker** | Rekommendation
--- | --- | ---
150097<br></br>**Meddelandet**: Det gick inte att aktivera replikering för den virtuella datorn VmName. | -Ditt prenumerations-ID kanske inte är aktiverat för att skapa virtuella datorer på målplatsen region.</br></br>-Ditt prenumerations-ID kanske inte är aktiverat eller har inte tillräcklig kvot för att skapa specifika VM-storlekar på målplatsen region.</br></br>-Ett lämpligt mål VM-storlek som matchar källan VM NIC antalet (2) inte kan hittas för prenumerations-ID på målplatsen region.| Kontakta [Azure faktureringssupporten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) att skapa en virtuell dator för nödvändiga VM-storlekar på målplatsen för din prenumeration. Försök igen när den har aktiverats.

### <a name="fix-the-problem"></a>Åtgärda problemet
Du kan kontakta [Azure faktureringssupporten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) att aktivera din prenumeration att skapa virtuella datorer av nödvändiga storlekar på målplatsen.

Om platsen har en begränsning för kapacitet, inaktivera replikering och aktivera det på en annan plats där din prenumeration har tillräcklig kvot för att skapa virtuella datorer av nödvändiga storlekar.

## <a name="trusted-root-certificates-error-code-151066"></a>Betrodda rotcertifikat (felkod 151066)

Om alla de senaste betrodda rotcertifikat inte finns på den virtuella datorn kan utskriften ”Aktivera replikering” misslyckas. Utan certifikat misslyckas autentisering och auktorisering av Site Recovery-anrop från den virtuella datorn. Felmeddelandet för det misslyckade jobbet för ”Aktivera replikering” Site Recovery visas:

Felkod | **Möjlig orsak** | **Rekommendationer**
--- | --- | ---
151066<br></br>**Meddelandet**: Site Recovery-konfigurationen misslyckades. | Det obligatoriska betrodda rotcertifikat som används för auktorisering och autentisering inte finns på datorn. | – För en virtuell dator som kör Windows operativsystem, se till att de betrodda rotcertifikat finns på datorn. Mer information finns i [konfigurera betrodda rotcertifikat och otillåtna certifikat](https://technet.microsoft.com/library/dn265983.aspx).<br></br>– För en virtuell dator som kör Linux-operativsystem, följer du anvisningarna för betrodda rotcertifikat som publicerats av Linux operativsystem version distributören.

### <a name="fix-the-problem"></a>Åtgärda problemet
**Windows**

Installera de senaste uppdateringarna på den virtuella datorn så att alla betrodda rotcertifikat som finns på datorn. Om du är i en frånkopplad miljö, följ Windows uppdatering standardprocessen för i din organisation att hämta certifikaten. Om certifikat som krävs inte finns på den virtuella datorn inte anrop till Site Recovery-tjänsten av säkerhetsskäl.

Följ vanliga Windows update management eller certifikat management uppdateringsprocessen i din organisation att hämta de senaste rotcertifikat och listan över återkallade certifikat uppdaterade på virtuella datorer.

Kontrollera att problemet är löst, gå till login.microsoftonline.com från en webbläsare i den virtuella datorn.

**Linux**

Följ de riktlinjer som tillhandahålls av Linux-distributör för att hämta de senaste betrodda rotcertifikat och senaste listan över återkallade certifikat på den virtuella datorn.

Eftersom SuSE Linux använder symlinks för att underhålla en lista över certifikat, så här:

1.  Logga in som rotanvändare.

2.  Kör detta kommando för att ändra katalogen.

      ``# cd /etc/ssl/certs``

3. Kontrollera om det finns Symantec rot-CA-certifikat.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4. Om det gick inte att hitta Symantec rot-CA-certifikat, kör du följande kommando för att hämta filen. Kontrollera om några fel och följ rekommenderade åtgärden för nätverksfel.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

5. Kontrollera om det finns Baltimore rot-CA-certifikat.

      ``# ls Baltimore_CyberTrust_Root.pem``

6. Om det gick inte att hitta Baltimore rot-CA-certifikat, hämta certifikatet.  

    ``# wget http://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

7. Kontrollera om DigiCert_Global_Root_CA cert finns.

    ``# ls DigiCert_Global_Root_CA.pem``

8. Om det gick inte att hitta DigiCert_Global_Root_CA, kör du följande kommandon för att hämta certifikatet.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

9. Kör rehash skript för att uppdatera certifikatet ämne hashvärden för nyligen hämtade certifikat.

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


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Utgående anslutning för Site Recovery-URL: er eller IP-adressintervall (felkod 151037 eller 151072)

Site Recovery replikering till arbete, utgående anslutning till specifika URL: er eller IP-adressintervall krävs från den virtuella datorn. Om den virtuella datorn finns bakom en brandvägg eller använder regler för nätverkssäkerhetsgrupper (NSG) för att styra utgående anslutning, kan du se något av följande felmeddelanden:

**Felkoder** | **Möjliga orsaker** | **Rekommendationer**
--- | --- | ---
151037<br></br>**Meddelandet**: Det gick inte att registrera virtuella Azure-datorn med Site Recovery. | -Du använder NSG för utgående åtkomstkontroll på den virtuella datorn och nödvändiga IP-adressintervall som inte är godkända för utgående åtkomst.</br></br>-Du använder från tredje part brandväggen verktyg och de IP-adressintervall/URL: erna är inte godkända.</br>| – Se till att nödvändiga URL: er eller datacenter IP-adressintervall är godkända om du använder brandväggen proxy för att styra utgående nätverksanslutningen på den virtuella datorn. Mer information finns i [brandväggen proxy vägledning](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>– Om du använder NSG-regler för att styra utgående nätverksanslutningen på den virtuella datorn, se till att de nödvändiga datacenter IP-adressintervall är godkända. Mer information finns i [nätverk grupp säkerhetsvägledning](https://aka.ms/a2a-nsg-guidance).
151072<br></br>**Meddelandet**: Site Recovery-konfigurationen misslyckades. | Att det går inte upprätta en anslutning till Site Recovery-Tjänsteslutpunkter. | – Se till att nödvändiga URL: er eller datacenter IP-adressintervall är godkända om du använder brandväggen proxy för att styra utgående nätverksanslutningen på den virtuella datorn. Mer information finns i [brandväggen proxy vägledning](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>– Om du använder NSG-regler för att styra utgående nätverksanslutningen på den virtuella datorn, se till att de nödvändiga datacenter IP-adressintervall är godkända. Mer information finns i [nätverk grupp säkerhetsvägledning](https://aka.ms/a2a-nsg-guidance).

### <a name="fix-the-problem"></a>Åtgärda problemet
Att godkända [i URL: erna](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) eller [krävs för IP-adressintervall](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), följer du stegen i den [nätverk riktlinjerna](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Hittades inte på datorn (felkod 150039)

En ny disk ansluten till den virtuella datorn måste initieras.

Felkod | **Möjliga orsaker** | **Rekommendationer**
--- | --- | ---
150039<br></br>**Meddelandet**: Azure-datadisk (DiskName) (DiskURI) med logiska enhetsnummer (LUN) (LUNValue) inte har kopplats till en motsvarande disk som har rapporterats från den virtuella datorn som har samma LUN-värdet. | -En ny datadisk har kopplats till den virtuella datorn, men det fanns inte initierats.</br></br>-Datadisken inuti den virtuella datorn är inte rapporterar LUN-värdet som disken var kopplad till den virtuella datorn.| Kontrollera att datadiskar som har initierats och försök sedan igen.</br></br>För Windows: [Anslut och initiera en ny disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>För Linux: [initierar en ny datadisk i Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Åtgärda problemet
Se till att datadiskar som har initierats och försök sedan igen:

- För Windows: [Anslut och initiera en ny disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- För Linux: [lägger till en ny datadisk i Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Kontakta supporten om problemet kvarstår.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>Det går inte att se Azure VM väljas i ”Aktivera replikering”

Om du inte ser den virtuella datorn som du vill aktivera för replikering, kanske den på grund av en inaktuell konfiguration för Site Recovery lämnas på Azure VM. Inaktuell konfiguration kan lämnas på en Azure VM i följande fall:

- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och sedan bort Site Recovery-valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och ta bort resursgruppen som innehåller Site Recovery-valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

Du kan använda [ta bort inaktuella ASR konfigurationsskript](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) och ta bort inaktuella Site Recovery-konfigurationen på Azure VM. Du ska kunna se den virtuella datorn när du tar bort inaktuella konfigurationen.

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>Virtuella datorns etablering tillstånd är inte giltigt (felkod 150019)

Om du vill aktivera replikering på den virtuella datorn ska etableringsstatusen **lyckades**. Du kan kontrollera tillstånd för virtuell dator genom att följa stegen nedan.

1.  Välj den **Resursläsaren** från **alla tjänster** i Azure-portalen.
2.  Expandera den **prenumerationer** och väljer din prenumeration.
3.  Expandera den **resursgrupper** och väljer resursgruppen för den virtuella datorn.
4.  Expandera den **resurser** och väljer den virtuella datorn
5.  Kontrollera den **provisioningState** i instansvyn på höger sida.

### <a name="fix-the-problem"></a>Åtgärda problemet

- Om **provisioningState** är **misslyckades**, kontakta supporten med information om hur du felsöker.
- Om **provisioningState** är **uppdatering**, en annan utökning kan komma distribueras. Kontrollera om det finns några pågående åtgärder på den virtuella datorn, vänta tills de är klara och gör den misslyckade platsen har återställts **Aktivera replikering** jobb.

## <a name="next-steps"></a>Nästa steg
[Replikera virtuella Azure-datorer](site-recovery-replicate-azure-to-azure.md)
