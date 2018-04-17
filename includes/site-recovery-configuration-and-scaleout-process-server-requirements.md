| **Komponent** | **Krav** |
| --- |---|
| Processorkärnor| 8 |
| RAM | 16 GB|
| Antal diskar | 3, inklusive OS-disk, disk för processen server cache och kvarhållningsenhetens för återställning efter fel |
| Ledigt diskutrymme (processerverns cacheminne) | 600 GB
| Ledigt diskutrymme (kvarhållningsdisken) | 600 GB|
| Operativsystem  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Nationella inställningar för operativsystem | Engelska (en-us)|
| VMware vSphere PowerCLI-version | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server-roller | Aktivera inte dessa roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Grupprinciper| Aktivera inte dessa grupp-principer: <br> -Förhindra åtkomst till Kommandotolken. <br> -Förhindra åtkomst till verktyg för redigering av registret. <br> -Förtroende för bifogade filer. <br> -Aktivera körning av skript. <br> [Läs mer](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)|
| IIS | -Ingen redan befintliga standardwebbplatsen <br> -Aktivera [anonym autentisering](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) <br> -Aktivera [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx) inställning  <br> -Ingen befintliga webbplats/programmet lyssnar på port 443<br>|
| NIC-typ | VMXNET3 (när distribueras som en VM VMware) |
| IP-adresstyp | Statisk |
| Internet-åtkomst | Servern behöver åtkomst till dessa webbadresser: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (krävs inte för skalbara servrar) <br> - time.nist.gov <br> - time.windows.com |
| Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring)|
