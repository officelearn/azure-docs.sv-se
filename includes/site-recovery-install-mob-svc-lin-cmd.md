1. Kopiera installationsprogrammet till en lokal mapp (till exempel /tmp) på den server som du vill skydda. Kör följande kommandon i en terminal:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. För att installera Mobilitetstjänsten, kör du följande kommando:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. När installationen är klar, måste Mobilitetstjänsten registreras på konfigurationsservern. Kör följande kommando för att registrera Mobilitetstjänsten med konfigurationsservern.

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
|-r |Obligatorisk|Anger om Mobility Service (MS) som ska installeras eller MasterTarget(MT) ska installeras|MS </br> MT|
|-d |Valfri|Plats där Mobilitetstjänsten kommer att installeras|/usr/local/ASR|
|-v|Obligatorisk|Anger plattformen där Mobilitetstjänsten komma installeras </br> </br>-** VMware: Använd det här värdet om du installerar mobilitetstjänsten på en virtuell dator som körs på *vSphere VMware ESXi-värdar*, * Hyper-V-värdar och *fysiska servrar* </br> -** Azure: Använd det här värdet om du installerar agenten på en Azure IaaS-VM| VMware </br> Azure|
|-q|Valfri|Anger om du vill köra installer i tyst läge| Gäller inte|


#### <a name="mobility-service-configuration-command-line"></a>Mobility Service configuration kommandoraden

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parameter|Typ|Beskrivning|Möjliga värden|
|-|-|-|-|
|-i |Obligatorisk|Konfigurationsservern IP|Vilken giltig IP-adress som helst|
|-P |Obligatorisk|Fullständig sökväg där anslutning lösenfrasen sparas filen|En giltig mapp|
