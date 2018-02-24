1. Kopiera installationsprogrammet till en lokal mapp (exempelvis C:\Temp) på den server som du vill skydda. Kör följande kommandon som administratör i en kommandotolk:

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. För att installera Mobilitetstjänsten, kör du följande kommando:

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```
3. Agenten måste vara registrerad med konfigurationsservern.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe”  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Tjänsten Mobility installer kommandoradsargument

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| Parameter|Typ|Beskrivning|Möjliga värden|
|-|-|-|-|
|/ Roll|Obligatorisk|Anger om Mobility Service (MS) som ska installeras eller MasterTarget (Huvudmålservern) ska installeras.|MS </br> MT|
|/InstallLocation|Valfri|Plats där Mobilitetstjänsten är installerad.|Vilken mapp på datorn som helst|
|/ Platform|Obligatorisk|Anger plattformen Mobilitetstjänsten är installerad. </br> </br>- **VMware**: Använd det här värdet om du installerar Mobilitetstjänsten på en virtuell dator som körs på *VMware vSphere ESXi-värdar*, *Hyper-V-värdar*, och *fysiska servrar*. </br> - **Azure**: Använd det här värdet om du installerar en agent på en Azure IaaS-VM. | VMware </br> Azure|
|/ Tyst|Valfri|Anger att installationsprogrammet körs i tyst läge.| Gäller inte|

>[!TIP]
> Installationsloggarna finns under % ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.

#### <a name="mobility-service-registration-command-line-arguments"></a>Tjänsten Mobility registrering kommandoradsargument

```
Usage :
UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | Parameter|Typ|Beskrivning|Möjliga värden|
  |-|-|-|-|
  |/CSEndPoint |Obligatorisk|IP-adress för konfigurationsservern| En giltig IP-adress|
  |/PassphraseFilePath|Obligatorisk|Platsen för lösenordet |En giltig UNC- eller lokal filsökväg|


>[!TIP]
> Agentkonfiguration-loggar finns under % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
