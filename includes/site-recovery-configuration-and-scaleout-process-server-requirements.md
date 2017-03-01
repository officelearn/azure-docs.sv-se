| **Maskinvara** | |
| --- |---|
| Antal processorkärnor| 8 |
| RAM| 12 GB|
| Antal diskar | 3 <br><br> - OS-disk<br> - Processerverns cachedisk<br> - Kvarhållningsenhet (för återställning efter fel)|
| Ledigt diskutrymme (processerverns cacheminne) | 600 GB
| Ledigt diskutrymme (kvarhållningsdisken) | 600 GB|
| **Programvara** | |
| Operativsystemversion | Windows Server 2012 R2 |
| Nationella inställningar för operativsystem | Engelska (en-us)|
| VMware vSphere PowerCLI-version | [PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0 "PowerCLI 6.0")|
| Windows Server-roller | Aktivera inte följande roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| **Nätverk** | |
| Nätverksgränssnittets korttyp | VMXNET3 |
| IP-adresstyp | Statisk |
| Internet-åtkomst | Servern ska kunna komma åt följande webbadresser antingen direkt eller via en proxyserver: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (krävs inte för skalbara processervrar) <br> - time.nist.gov <br> - time.windows.com |
| Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring)|


<!--HONumber=Feb17_HO3-->


