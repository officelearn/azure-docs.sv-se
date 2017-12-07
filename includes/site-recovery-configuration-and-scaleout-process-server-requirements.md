| **Maskinvara** | |
| --- |---|
| Antal processorkärnor| 8 |
| RAM| 12 GB|
| Antal diskar | 3 <br><br> - OS-disk<br> - Processerverns cachedisk<br> - Kvarhållningsenhet (för återställning efter fel)|
| Ledigt diskutrymme (processerverns cacheminne) | 600 GB
| Ledigt diskutrymme (kvarhållningsdisken) | 600 GB|
| **Programvara** | |
| Operativsystemversion | Windows Server 2012 R2 <br> Windows Server 2016 |
| Nationella inställningar för operativsystem | Engelska (en-us)|
| VMware vSphere PowerCLI-version | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server-roller | Aktivera inte följande roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Grupprinciper| Följande grupprinciper bör inte aktiveras på servern <br> -Förhindra åtkomst till via Kommandotolken <br> -Förhindra åtkomst till verktyg för redigering av registret <br> -Förtroende för bifogade filer <br> -Aktivera körning av skript <br> **Obs:** finns mer information om dessa grupprinciper [här](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)|
| Internet Information Service(IIS) konfiguration | -Ingen befintlig standardwebbplatsen <br> -Aktivera [anonym autentisering](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) <br> -Aktivera [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx) inställning  <br> -Ingen befintlig websit/application bör lyssnar på port 443<br>|
| **Nätverk** | |
| Nätverksgränssnittets korttyp | VMXNET3 (när distribueras som en virtuell VMware-dator) |
| IP-adresstyp | Statisk |
| Internet-åtkomst | Servern ska kunna komma åt följande webbadresser antingen direkt eller via en proxyserver: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (krävs inte för skalbara processervrar) <br> - time.nist.gov <br> - time.windows.com |
| Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring)|
