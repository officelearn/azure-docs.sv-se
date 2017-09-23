UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP-adress som ska användas för dataöverföring] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Parametrar:

* / ServerMode: Obligatorisk. Anger om både konfigurationen och processervrar ska installeras, eller endast processervern. Indatavärden: CS, PS.
* InstallLocation: Obligatorisk. Den mapp där komponenterna installeras.
* /MySQLCredsFilePath. Obligatorisk. Filsökvägen till platsen där autentiseringsuppgifterna för MySQL-servern lagras. Filen måste vara i formatet:
* [MySQLCredentials]
* MySQLRootPassword = "<Password>"
* MySQLUserPassword = "<Password>"
* /VaultCredsFilePath. Obligatorisk. Platsen för valvautentiseringsfilen
* /EnvType. Obligatorisk. Typ av installation. Värden: VMware, NonVMware
* /PSIP och /CSIP. Obligatorisk. IP-adressen för processervern och konfigurationsservern.
* /PassphraseFilePath. Obligatorisk. Platsen för lösenfrasfilen.
* /BypassProxy. Valfri. Anger att konfigurationsservern ansluter till Azure utan en proxyserver.
* /ProxySettingsFilePath. Valfri. Proxy-inställningar (standardproxy kräver autentisering, eller en anpassad proxy). Filen måste vara i formatet:
* [ProxySettings]
* ProxyAuthentication = "Ja/Nej"
* Proxy IP = "IP-adress >"
* ProxyPort = "<Port>"
* ProxyUserName="<User Name>"
* ProxyPassword="<Password>"
* DataTransferSecurePort. Valfri. Portnumret för replikeringsdata.
* SkipSpaceCheck. Valfri. Hoppa över utrymmeskontroll för cache.
* AcceptThirdpartyEULA. Obligatorisk. Godkänner licensavtalet (EULA) från tredje part.
* ShowThirdpartyEULA. Obligatorisk. Visar licensavtalet (EULA) från tredje part. Om detta anges som indata ignoreras alla andra parametrar.
