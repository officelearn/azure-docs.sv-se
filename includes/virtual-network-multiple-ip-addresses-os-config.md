## <a name="os-config"></a>Lägg till IP-adresser till ett VM-operativsystem

Anslut och logga in på en virtuell dator som du skapade med flera privata IP-adresser. Du måste manuellt lägga till alla de privata IP-adresser (inklusive det primära) som du lade till den virtuella datorn. Utför följande steg för ditt VM-operativsystem:

### <a name="windows"></a>Windows

1. Från en kommandotolk skriver du *ipconfig /all*.  Du ser bara den *primära* privata IP-adressen (via DHCP).
2. Skriv *ncpa.cpl* i kommandotolken för att öppna fönstret **Nätverksanslutningar**.
3. Öppna egenskaperna för det passande nätverkskortet: **Anslutning till lokalt nätverk**.
4. Dubbelklicka på Internet Protocol version 4 (IPv4).
5. Välj **Använd följande IP-adress** och ange följande värden:

    * **IP-adress**: Ange den *primära* privata IP-adressen
    * **Nätmask**: Ställ in beroende på ditt undernät. Om undernätet till exempel är ett /24-undernät så är nätmasken 255.255.255.0.
    * **Standard-gateway**: Den första IP-adressen i undernätet. Om undernätet är 10.0.0.0/24 så är gateway-IP-adressen 10.0.0.1.
    * Klicka på **Använd följande DNS-serveradresser** och ange följande värden:
        * **Önskad DNS-server**: Ange 168.63.129.16 om du inte använder en egen DNS-server.  Om du använder en egen DNS-server måste du ange dess IP-adress.
    * Klicka på knappen **Avancerat** och lägg till ytterligare IP-adresser. Lägg till varje sekundär privat IP-adress som anges i steg 8 till NIC med samma undernät som angetts för den primära IP-adressen.
        >[!WARNING] 
        >Om du inte följer du stegen ovan på rätt sätt kan du förlora anslutningen till den virtuella datorn. Kontrollera att informationen i steg 5 är korrekt innan du fortsätter.

    * Klicka på **OK** för att stänga TCP/IP-inställningarna och sedan på **OK** igen för att stänga inställningarna för nätverkskortet. RDP-anslutningen återupprättats.

6. Från en kommandotolk skriver du *ipconfig /all*. Alla IP-adresser som du lade till visas och DHCP är avstängt.


### <a name="validation-windows"></a>Validering (Windows)

Om du vill kontrollera att du kan ansluta till internet från den sekundära IP-konfigurationen via den offentliga IP-adress som är knuten till den använder du följande kommando när du har lagt till den korrekt med hjälp av stegen ovan:

```bash
ping -S 10.0.0.5 hotmail.com
```
>[!NOTE]
>För sekundära IP-konfigurationer, kan du endast pinga till Internet om konfigurationen har en offentlig IP-adress som är kopplade till den. För den primära IP-konfigurationer krävs inte en offentlig IP-adress för att pinga till Internet.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Öppna ett terminalfönster.
2. Kontrollera att du är rotanvändaren. Om inte anger du följande kommando:

    ```bash
    sudo -i
    ```

3. Uppdatera konfigurationsfilen för nätverksgränssnittet (förutsätter 'eth0').

    * Behåll det befintliga radobjektet för dhcp. Den primära IP-adressen är fortfarande konfigurerad som den var tidigare.
    * Lägg till en konfiguration för ytterligare en statisk IP-adress med följande kommandon:

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    Du bör se en .cfg-fil.
4. Öppna filen. Du bör se följande rader i slutet av filen:

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. Lägg till följande rader efter de rader som redan finns i den här filen:

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    netmask <your subnet mask>
    ```

6. Spara filen med följande kommando:

    ```bash
    :wq
    ```

7. Återställ nätverksgränssnittet med följande kommando:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > Kör både ifdown och ifup i samma rad om du använder en fjärranslutning.
    >

8. Kontrollera att IP-adressen läggs till i nätverksgränssnittet med följande kommando:

    ```bash
    ip addr list eth0
    ```

    Du bör se IP-adressen som du lade till i listan.

### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat, CentOS och andra)

1. Öppna ett terminalfönster.
2. Kontrollera att du är rotanvändaren. Om inte anger du följande kommando:

    ```bash
    sudo -i
    ```

3. Ange ditt lösenord och följ instruktionerna som efterfrågas. När du är rotanvändaren navigerar du till mappen för nätverksskript med följande kommando:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Skapa en lista över de relaterade ifcfg-filerna med följande kommando:

    ```bash
    ls ifcfg-*
    ```

    Du bör se *ifcfg-eth0* som en av filerna.

5. För att lägga till en IP-adress skapar du en konfigurationsfil för den enligt anvisningarna nedan. Observera att en fil måste skapas för varje IP-konfiguration.

    ```bash
    touch ifcfg-eth0:0
    ```

6. Öppna filen *ifcfg-eth0:0* med följande kommando:

    ```bash
    vi ifcfg-eth0:0
    ```

7. Lägg till innehåll till filen *eth0:0*, i det här fallet med följande kommando. Se till att uppdatera informationen baserat på din IP-adress.

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. Spara filen med följande kommando:

    ```bash
    :wq
    ```

9. Starta om nätverkstjänsterna och kontrollera att ändringarna körs med följande kommandon:

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    Du bör se IP-adressen som du lade till, *eth0:0*, i listan som returneras.

### <a name="validation-linux"></a>Validering (Linux)

Om du vill kontrollera att du kan ansluta till Internet från en sekundär IP-konfiguration via den offentliga IP-adress som är kopplad till den använder du följande kommando:

```bash
ping -I 10.0.0.5 hotmail.com
```
>[!NOTE]
>För sekundära IP-konfigurationer, kan du endast pinga till Internet om konfigurationen har en offentlig IP-adress som är kopplade till den. För den primära IP-konfigurationer krävs inte en offentlig IP-adress för att pinga till Internet.

För virtuella Linux-datorer kan du behöva lägga till lämpliga vägar när du försöker verifiera utgående anslutningar från ett sekundärt nätverkskort. Det finns flera sätt att göra detta på. Se motsvarande dokumentation för din distribution av Linux. Detta går exempelvis att åstadkomma med hjälp av följande metod:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Se till att ersätta:
    - **10.0.0.5** med den privata IP-adress som har en offentlig IP-adress som är kopplad till den
    - **10.0.0.1** till standard-gatewayen
    - **eth2** till namnet på det sekundära nätverkskortet
