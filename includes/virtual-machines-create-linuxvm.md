1. Melden Sie sich bei Ihrem Azure-Abonnement mithilfe der Schritte im [Verbinden mit Azure in der Azure-Befehlszeilenschnittstelle](../articles/xplat-cli-connect.md).

2. Stellen Sie folgendermaßen sicher, dass der Dienstverwaltungsmodus aktiviert ist:

        azure config mode asm

3. Wählen Sie aus den verfügbaren Images das Linux-Image aus, das Sie laden möchten:

        azure vm image list | grep "Linux"

   In einem Windows-Eingabeaufforderungsfenster verwenden Sie „find“ anstelle von „grep“. 

4. Erstellen Sie mithilfe von `azure vm create` einen neuen virtuellen Computer mit dem Linux-Image aus der obigen Liste. Dadurch werden ein neuer Clouddienst und ein neues Speicherkonto erstellt. Sie können auch mithilfe der Option `-c` eine Verbindung zwischen diesem virtuellen Computer und einem vorhandenen Clouddienst herstellen. Zudem wird ein SSH-Endpunkt zum Anmelden beim virtuellen Linux-Computer mit der Option `-e` erstellt.

        ~$ azure vm create "MyTestVM" b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64 "adminUser" -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64
        Enter VM 'adminUser' password:*********
        Confirm password: *********
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating a new storage account 'mytestvm1437604756125'
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE] Für einen virtuellen Linux-Computer Geben Sie die `-e` option `vm create`; Es ist nicht möglich, SSH zu aktivieren, nachdem die virtuelle Maschine erstellt wurde. Weitere Informationen zu SSH finden Sie unter [Verwenden von SSH mit Linux auf Azure](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md).

    Beachten Sie, dass das Bild *b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64* wird es aus der im vorherigen Schritt ausgewählt haben. *MyTestVM* ist der Name des neuen virtuellen Computers, und *AdminUser* ist der Benutzername, die wir für SSH auf dem virtuellen Computer verwenden. Sie können diese Variablen nach Bedarf ersetzen. Weitere Informationen zu diesem Befehl finden Sie auf der [mithilfe der Azure-Befehlszeilenschnittstelle mit Azure Service Management](../articles/virtual-machines/virtual-machines-command-line-tools.md).

5. Der neu erstellte virtuelle Linux-Computer wird in der angegebenen Liste angezeigt:

        azure vm list

6. Sie können die Attribute des virtuellen Computers mithilfe des folgenden Befehls überprüfen:

        azure vm show MyTestVM

7. Der neu erstellte virtuelle Computer kann nun mit dem Befehl `azure vm start` gestartet werden.

Ausführliche Informationen über alle Befehle der virtuellen Maschine diese Azure-Befehlszeilenschnittstelle, lesen Sie bitte die [die Dienst-API der Azure-Befehlszeilenschnittstelle mit](../articles/virtual-machines/virtual-machines-command-line-tools.md).


