---
title: "Azure Site Recovery felsökning för Azure-Azure-replikeringsproblem och fel | Microsoft Docs"
description: "Felsökning av fel och problem vid replikering av virtuella Azure-datorer för katastrofåterställning"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: sujayt
ms.openlocfilehash: 5e4de47de554f36e7797b7994faee4e90c3a8186
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2017
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Felsökning av problem med Virtuella Azure-Azure-replikering

Den här artikeln beskrivs hur du felsöker dem beskrivs vanliga problem i Azure Site Recovery när replikering och återställa virtuella Azure-datorer från en region till en annan region. Mer information om konfigurationer som stöds finns i [stöd matrix för att replikera virtuella datorer i Azure](site-recovery-support-matrix-azure-to-azure.md).

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problem med Azure-resurs kvoten (felkod 150097)
Din prenumeration måste vara aktiverat för att skapa virtuella Azure-datorer i mål-region som du tänker använda som disaster recovery region. Din prenumeration bör också ha tillräcklig kvot aktiverad för att skapa virtuella datorer i en viss storlek. Som standard hämtar Site Recovery för mål VM samma storlek som den Virtuella källdatorn. Om matchande storlek inte är tillgänglig, hämtas den närmaste möjliga storleken automatiskt. Om det finns ingen matchande storlek som stöder VM-konfiguration för datakällan, visas detta felmeddelande:

**Felkod** | **Möjliga orsaker** | **Rekommendation**
--- | --- | ---
150097<br></br>**Meddelandet**: Det gick inte att aktivera replikering för den virtuella datorn VmName. | -Ditt prenumerations-ID kanske inte är aktiverat för att skapa virtuella datorer på målplatsen region.</br></br>-Ditt prenumerations-ID kanske inte är aktiverat eller har inte tillräcklig kvot för att skapa specifika VM-storlekar på målplatsen region.</br></br>-Ett lämpligt mål VM-storlek som matchar källan VM NIC antalet (2) inte kan hittas för prenumerations-ID på målplatsen region.| Kontakta [Azure faktureringssupporten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) att skapa en virtuell dator för nödvändiga VM-storlekar på målplatsen för din prenumeration. Försök igen när den har aktiverats.

### <a name="fix-the-problem"></a>Åtgärda problemet
Du kan kontakta [Azure faktureringssupporten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) att aktivera din prenumeration att skapa virtuella datorer av nödvändiga storlekar på målplatsen.

Om platsen har en begränsning för kapacitet, inaktivera replikering och aktivera det på en annan plats där din prenumeration har tillräcklig kvot för att skapa virtuella datorer av nödvändiga storlekar.

## <a name="trusted-root-certificates-error-code-151066"></a>Betrodda rotcertifikat (felkod 151066)

Om alla de senaste betrodda rotcertifikat inte finns på den virtuella datorn kan utskriften ”Aktivera replikering” misslyckas. Utan certifikat misslyckas autentisering och auktorisering av Site Recovery-anrop från den virtuella datorn. Felmeddelandet för det misslyckade jobbet för ”Aktivera replikering” Site Recovery visas:

**Felkod** | **Möjlig orsak** | **Rekommendationer**
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

2.  Kör följande kommando:

      ``# cd /etc/ssl/certs``

3.  För att se om Symantec certifikatet för rotcertifikatutfärdaren är tillgänglig eller inte, kör du kommandot:

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

4.  Om filen inte hittas kan du köra följande kommandon:

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

      ``# c_rehash``

5.  Skapa en symlink med b204d74a.0 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem -> kan du köra det här kommandot:

      ``# ln -s  VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

6.  Kontrollera om det här kommandot har följande utdata. Om inte, måste du skapa en symlink:

      ``# ls -l | grep Baltimore
      -rw-r--r-- 1 root root   1303 Apr  7  2016 Baltimore_CyberTrust_Root.pem
      lrwxrwxrwx 1 root root     29 May 30 04:47 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      lrwxrwxrwx 1 root root     29 May 30 05:01 653b494a.0 -> Baltimore_CyberTrust_Root.pem``

7. Om symlink 653b494a.0 inte finns, Använd följande kommando för att skapa en symlink:

      ``# ln -s Baltimore_CyberTrust_Root.pem 653b494a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Utgående anslutning för Site Recovery-URL: er eller IP-adressintervall (felkod 151037 eller 151072)

Site Recovery replikering till arbete, utgående anslutning till specifika URL: er eller IP-adressintervall krävs från den virtuella datorn. Om den virtuella datorn finns bakom en brandvägg eller använder regler för nätverkssäkerhetsgrupper (NSG) för att styra utgående anslutning, kan du se något av följande felmeddelanden:

**Felkoder** | **Möjliga orsaker** | **Rekommendationer**
--- | --- | ---
151037<br></br>**Meddelandet**: Det gick inte att registrera virtuella Azure-datorn med Site Recovery. | -Du använder NSG för utgående åtkomstkontroll på den virtuella datorn och nödvändiga IP-adressintervall som inte är godkända för utgående åtkomst.</br></br>-Du använder från tredje part brandväggen verktyg och de IP-adressintervall/URL: erna är inte godkända.</br>| – Se till att nödvändiga URL: er eller datacenter IP-adressintervall är godkända om du använder brandväggen proxy för att styra utgående nätverksanslutningen på den virtuella datorn. Mer information finns i [brandväggen proxy vägledning](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>– Om du använder NSG-regler för att styra utgående nätverksanslutningen på den virtuella datorn, se till att de nödvändiga datacenter IP-adressintervall är godkända. Mer information finns i [nätverk grupp säkerhetsvägledning](https://aka.ms/a2a-nsg-guidance).
151072<br></br>**Meddelandet**: Site Recovery-konfigurationen misslyckades. | Att det går inte upprätta en anslutning till Site Recovery-Tjänsteslutpunkter. | – Se till att nödvändiga URL: er eller datacenter IP-adressintervall är godkända om du använder brandväggen proxy för att styra utgående nätverksanslutningen på den virtuella datorn. Mer information finns i [brandväggen proxy vägledning](https://aka.ms/a2a-firewall-proxy-guidance).</br></br>– Om du använder NSG-regler för att styra utgående nätverksanslutningen på den virtuella datorn, se till att de nödvändiga datacenter IP-adressintervall är godkända. Mer information finns i [nätverk grupp säkerhetsvägledning](https://aka.ms/a2a-nsg-guidance).

### <a name="fix-the-problem"></a>Åtgärda problemet
Att godkända [i URL: erna](site-recovery-azure-to-azure-networking-guidance.md#outbound-connectivity-for-azure-site-recovery-urls) eller [krävs för IP-adressintervall](site-recovery-azure-to-azure-networking-guidance.md#outbound-connectivity-for-azure-site-recovery-ip-ranges), följer du stegen i den [nätverk riktlinjerna](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>Hittades inte på datorn (felkod 150039)

En ny disk ansluten till den virtuella datorn måste initieras.

**Felkod** | **Möjliga orsaker** | **Rekommendationer**
--- | --- | ---
150039<br></br>**Meddelandet**: Azure-datadisk (DiskName) (DiskURI) med logiska enhetsnummer (LUN) (LUNValue) inte har kopplats till en motsvarande disk som har rapporterats från den virtuella datorn som har samma LUN-värdet. | -En ny datadisk har kopplats till den virtuella datorn, men det fanns inte initierats.</br></br>-Datadisken inuti den virtuella datorn är inte rapporterar LUN-värdet som disken var kopplad till den virtuella datorn.| Kontrollera att datadiskar som har initierats och försök sedan igen.</br></br>För Windows: [Anslut och initiera en ny disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-portal#option-1-attach-and-initialize-a-new-disk).</br></br>För Linux: [initierar en ny datadisk i Linux](https://docs.microsoft.com/azure/virtual-machines/linux/classic/attach-disk#initialize-a-new-data-disk-in-linux).

### <a name="fix-the-problem"></a>Åtgärda problemet
Se till att datadiskar som har initierats och försök sedan igen:

- För Windows: [Anslut och initiera en ny disk](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-portal#option-1-attach-and-initialize-a-new-disk).
- För Linux: [initierar en ny datadisk i Linux](https://docs.microsoft.com/azure/virtual-machines/linux/classic/attach-disk#initialize-a-new-data-disk-in-linux).

Kontakta supporten om problemet kvarstår.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>Det går inte att se Azure VM väljas i ”Aktivera replikering”

Du kanske inte se dina Azure-VM för val i [Aktivera replikering: steg 2](./site-recovery-azure-to-azure.md#step-2-select-virtual-machines). Det här problemet kan på grund av inaktuella Site Recovery-konfiguration lämnas på Azure VM. Inaktuell konfiguration kan lämnas på en Azure VM i följande fall:

- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och sedan bort Site Recovery-valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.
- Du har aktiverat replikering för den virtuella Azure-datorn med hjälp av Site Recovery och ta bort resursgruppen som innehåller Site Recovery-valvet utan att uttryckligen inaktivera replikering på den virtuella datorn.

### <a name="fix-the-problem"></a>Åtgärda problemet

Du kan använda [ta bort inaktuella ASR konfigurationsskript](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) och ta bort inaktuella Site Recovery-konfigurationen på Azure VM. Du bör se den virtuella datorn i [Aktivera replikering: steg 2](./site-recovery-azure-to-azure.md#step-2-select-virtual-machines) när du tar bort inaktuella konfigurationen.


## <a name="next-steps"></a>Nästa steg
[Replikera virtuella Azure-datorer](site-recovery-replicate-azure-to-azure.md)
