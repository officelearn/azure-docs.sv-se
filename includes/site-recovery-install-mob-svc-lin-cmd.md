1. Kopiera installationsprogrammet till en lokal mapp (till exempel /tmp) på den server som du vill skydda. Kör följande kommandon i en terminal:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. För att installera Mobilitetstjänsten, kör du följande kommando:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. När installationen är klar, måste Mobilitetstjänsten vara registrerad på konfigurationsservern. Kör följande kommando för att registrera Mobilitetstjänsten med konfigurationsservern:

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>Mobility installer kommandoraden

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Parameter|Typ|Beskrivning|Möjliga värden|
|-|-|-|-|
|-r |Obligatorisk|Anger om Mobility Service (MS) som ska installeras eller MasterTarget (Huvudmålservern) ska installeras.|MS </br> HUVUDMÅLSERVERN|
|-d |Valfri|Plats där Mobilitetstjänsten är installerad.|/usr/local/ASR|
|-v|Obligatorisk|Anger plattformen Mobilitetstjänsten är installerad. </br> </br>- **VMware**: Använd det här värdet om du installerar Mobilitetstjänsten på en virtuell dator som körs på *VMware vSphere ESXi-värdar*, *Hyper-V-värdar*, och *fysiska servrar*. </br> - **Azure**: Använd det här värdet om du installerar en agent på en Azure IaaS-VM.| VMware </br> Azure|
|-q|Valfri|Anger att installationsprogrammet körs i tyst läge.| Gäller inte|


#### <a name="mobility-service-configuration-command-line"></a>Mobility Service configuration kommandoraden

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parameter|Typ|Beskrivning|Möjliga värden|
|-|-|-|-|
|-i |Obligatorisk|Konfigurationsservern IP|Vilken giltig IP-adress som helst|
|-P |Obligatorisk|Fullständig sökväg till filen där anslutningen passerar frasen sparas|En giltig mapp|
