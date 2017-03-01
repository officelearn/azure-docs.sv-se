---
title: 
description: 
services: site-recovery
documentationcenter: 
author: 
manager: 
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 4871e5959483058aa957e3373ddc5121b398e1d9
ms.openlocfilehash: 1d6d595bf788fc3fdba0bbc98887614e17c791dc


---



### <a name="install-the-mobility-service"></a>Installera mobilitetstjänsten
Det första steget för att aktivera skydd för virtuella datorer och fysiska servrar är att installera mobilitetstjänsten. Du kan göra det på ett antal olika sätt:

* **Processerver-push**: Skicka och installera mobilitetstjänstkomponenten från processervern när du aktiverar replikering på en dator. 
*Observera* att push-installationen inte inträffar om datorerna redan kör en uppdaterad version av komponenten.
* **Push för företag**: Installera komponenten automatiskt med hjälp av din pushprocess för företag, till exempel WSUS, System Center Configuration Manager eller [Azure Automation och Önskad tillståndskonfiguration](site-recovery-automate-mobility-service-install.md). Ställ in konfigurationsservern innan du gör detta.
* **Manuell installation**: Installera komponenten manuellt på varje dator som du vill replikera. Ställ in konfigurationsservern innan du gör detta.

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Förbered för automatisk push-installation på Windows-datorer
Så här förbereder du Windows-datorer så att mobilitetstjänsten kan installeras automatiskt av processervern.

1. Skapa ett konto som kan användas av processervern för att få åtkomst till datorn. Kontot bör ha administratörsbehörigheter (lokala eller domän) och bör bara användas för push-installationen.

   > [!NOTE]
   > Om du inte använder ett domänkonto, måste du inaktivera kontroll av åtkomst för fjärranvändare på den lokala datorn. Du gör detta genom att lägga till värdet 1 i DWORD-posten LocalAccountTokenFilterPolicy under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. För att lägga till registerposten från en CLI anger du **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. Välj **Tillåt en app eller funktion i brandväggen** i Windows-brandväggen på den dator som du vill skydda. Aktivera **Fil- och skrivardelning** samt **Windows Management Instrumentation**. Du kan konfigurera brandväggsinställningarna med ett grupprincipobjekt för datorer som tillhör en domän.

   ![Brandväggsinställningar](./media/site-recovery-vmware-to-azure/mobility1.png)
3. Lägg till kontot som du skapade:

   * Öppna **cspsconfigtool**. Filen finns som en genväg på skrivbordet och även på [INSTALLATIONSPLATSEN]\home\svsystems\bin folder.
   * I fliken **Hantera konton** klickar du på **Lägg till konto**.
   * Lägg till kontot som du skapade. När du har lagt till kontot måste du ange autentiseringsuppgifterna när du aktiverar replikering för en dator.

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Förbered för automatisk push-installation på Linux-servrar
1. Kontrollera att den Linux-dator som du vill skydda stöds enligt beskrivningen i [Krav för skyddade datorer](#protected-machine-prerequisites). Kontrollera att det finns en nätverksanslutning mellan Linux-datorn och processervern.
2. Skapa ett konto som kan användas av processervern för att få åtkomst till datorn. Kontot bör vara en rotanvändare på Linux-servern som används som källa och bör endast användas för push-installationen.

   * Öppna **cspsconfigtool**. Filen finns som en genväg på skrivbordet och även på [INSTALLATIONSPLATSEN]\home\svsystems\bin folder.
   * I fliken **Hantera konton** klickar du på **Lägg till konto**.
   * Lägg till kontot som du skapade. När du har lagt till kontot måste du ange autentiseringsuppgifterna när du aktiverar replikering för en dator.
3. Kontrollera att /etc/hosts-filen på Linux-servern som används som källa innehåller poster som kopplar det lokala värdnamnet till IP-adresser som är associerade med alla nätverkskort.
4. Installera den senaste openssh, openssh-server och openssl paketen på den dator du vill replikera.
5. Kontrollera att SSH är aktiverat och körs på port 22.
6. Aktivera SFTP-undersystemet och lösenordsautentisering i sshd_config-filen på följande sätt:

   * Logga in som en rotanvändare.
   * Hitta raden som börjar med **PasswordAuthentication** i filen /etc/ssh/sshd_config.
   * Ta bort raden och ändra värdet från **nej** till **ja**.
   * Hitta raden som börjar med **Subsystem** och ta bort den.

     ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>Installera mobilitetstjänsten manuellt
Installationsprogrammen är tillgängliga på konfigurationsservern på sökvägen **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

| Källoperativsystem | Installationsfil för mobilitetstjänsten |
| --- | --- |
| Windows Server (endast&64;-bitars) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4, 6.5, 6.6 (endast 64 bitars) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3 (endast 64 bitars) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4, 6.5 (endast 64 bitars) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-mobility-service-on-a-windows-server"></a>Installera mobilitetstjänsten på en Windows-server
1. Ladda ned och kör installationsprogrammet.
2. Välj **mobilitetstjänsten** i **innan du börjar**.

    ![Mobilitetstjänsten](./media/site-recovery-vmware-to-azure/mobility3.png)
3. I **Configuration Server Details** (Information om konfigurationsserver) anger du IP-adressen för konfigurationsservern samt lösenfrasen som skapades när du körde det enhetliga installationsprogrammet. Du kan hämta lösenfrasen genom att köra: **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – v** på konfigurationsservern.

    ![Mobilitetstjänsten](./media/site-recovery-vmware-to-azure/mobility6.png)
4. Under **Installationsplats** lämnar du standardinställningarna och klickar på **Nästa** för att påbörja installationen.
5. Under **Installationsförlopp** kan du övervaka installationen och starta om datorn när du uppmanas till detta. När du har installerat tjänsten kan det ta ungefär 15 minuter för statusen att uppdateras på portalen.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>Installera mobilitetstjänsten på en Windows-server med kommandotolken
1. Kopiera installationsprogrammet till en lokal mapp (t. ex. C:\Temp) på den server som du vill skydda. Installationsprogrammen finns på konfigurationsservern under **[Installationsplats]\home\svsystems\pushinstallsvc\repository**. Paketet för Windows-operativsystemet har namn som liknar Microsoft-ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe
2. Byt namn på den här filen till MobilitySvcInstaller.exe
3. Kör följande kommando för att extrahera MSI-installationsprogrammet:

    ``C:\> cd C:\Tempww
    ``C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted``
    ``C:\Temp> cd Extracted``
    ``C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>``

##### <a name="full-command-line-syntax"></a>Fullständig syntax för kommandorad
    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Parametrar**

* **/Role:** Obligatorisk. Anger om mobilitetstjänsten ska installeras. Agent för indatavärden | MasterTarget
* **/InstallLocation:** Obligatorisk. Anger var du vill installera tjänsten.
* **/PassphraseFilePath:** Obligatorisk. Konfigurationsserverns lösenfras.
* **/LogFilePath:** Obligatorisk. Plats där installationsloggfilerna ska skapas.

#### <a name="uninstall-the-mobility-service-manually"></a>Avinstallera mobilitetstjänsten manuellt
Mobilitetstjänsten kan avinstalleras med hjälp av Lägg till/ta bort program på Kontrollpanelen eller med hjälp av den här kommandoradsanvisningen: MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="install-the-mobility-service-on-a-linux-server"></a>Installera mobilitetstjänsten på en Linux-server
1. Kopiera det lämpliga tar-arkivet baserat på tabellen ovan till Linux-datorn som du vill replikera.
2. Öppna ett gränssnittsprogram och extrahera det komprimerade tar-arkivet till en lokal sökväg genom att köra: `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Skapa en passphrase.txt-fil i den lokala katalogen där du extraherade innehållet i tar-arkivet. För att göra det här kopierar du lösenfrasen från C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase på konfigurationsservern och sparar den i passphrase.txt genom att köra *`echo <passphrase> >passphrase.txt`* i gränssnittet.
4. Installera mobilitetstjänsten genom att köra *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Ange den interna IP-adressen för konfigurationsservern och kontrollera att port 443 har valts. När du har installerat tjänsten kan det ta ungefär 15 minuter för statusen att uppdateras i portalen.

**Du kan även installera från kommandoraden**:

Kopiera lösenfrasen från C:\Program Files (x86)\InMage Systems\private\connection på konfigurationsservern och spara den som "passphrase.txt" på konfigurationsservern. Kör sedan följande kommandon. I vårt exempel är IP-adressen för konfigurationsservern 104.40.75.37 och HTTPS-porten ska vara 443:


Installera på en produktionsserver:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Installera på en huvudmålserver:

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt






<!--HONumber=Feb17_HO3-->


