<properties services="virtual-machines" title="Using Azure CLI with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## Verwenden der Azure-CLI mit dem Azure-Ressourcen-Manager (ARM)

Bevor Sie die Azure-CLI mit Ressourcen-Manager-Befehlen und -Vorlagen zur Bereitstellung von Azure-Ressourcen und -Workloads über Ressourcengruppen verwenden können, benötigen Sie (natürlich) ein Azure-Konto. Wenn Sie nicht über ein Konto verfügen, erhalten Sie eine [kostenlose Azure-Testversion hier](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Wenn nicht bereits ein Azure-Konto haben, aber ein Abonnement MSDN-Abonnement haben, erhalten Sie kostenlose Azure-Gutschriften durch Aktivieren der [MSDN-Abonnent profitieren hier](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) -- oder kostenlose Konto verwenden. Beides ist für den Azure-Zugriff möglich.

### Schritt 1: Überprüfen der Azure-CLI-Version

Um die Azure-CLI für Befehle und ARM-Vorlagen verwenden zu können, benötigen Sie mindestens Version 0.8.17. Geben Sie zum Überprüfen Ihrer Version `azure --version` ein. Die Ausgabe sollte folgendermaßen aussehen:

    $ azure --version
    0.8.17 (node: 0.10.25)

Wenn Sie Ihre Version von Azure-CLI aktualisieren müssen, finden Sie unter [Befehlszeilenschnittstelle](https://github.com/Azure/azure-xplat-cli).

### Schritt 2: Überprüfen der für Azure verwendeten Identität (Arbeit oder Schule)

Sie können den ARM-Befehlsmodus nur verwenden, bei der Verwendung einer [Azure Active Directory-Mandanten](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) oder ein [Service Principal Name](https://msdn.microsoft.com/library/azure/dn132633.aspx). (Diese werden auch als bezeichnet *Organisations-Ids*.)

Um Ihre verwendete Identität zu ermitteln, melden Sie sich durch Eingabe von `azure login` an. Geben Sie, wenn Sie dazu aufgefordert werden, Ihren Arbeits- oder Schulbenutzernamen sowie das zugehörige Kennwort ein. Wenn Sie über die erforderliche ID verfügen, sollte etwa Folgendes angezeigt werden:

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
  	|info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK

Wenn dies nicht angezeigt wird, müssen Sie einen neuen Mandanten (oder Dienstprinzipal) für Ihre Microsoft-Kontoidentität erstellen. (Dies ist häufig der Fall bei persönlichen MSDN-Abonnements oder kostenlosen Testabonnements.) Erstellen eine Geschäfts- oder schulkonto-Id aus Ihrem Azure-Konto mit einer Microsoft-Id erstellt, finden Sie unter [ein neues Azure-Abonnement ein Azure AD-Verzeichnis zuordnen](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Wenn Sie glauben, dass Sie bereits über eine Organisations-ID verfügen, müssen Sie sich ggf. an die Person wenden, die das Konto für Sie erstellt hat.

### Schritt 3: Auswählen des Azure-Abonnements

Wenn Sie in Ihrem Azure-Konto nur über ein Abonnement verfügen, wird die Azure-CLI standardmäßig automatisch diesem Abonnement zugeordnet. Wenn Sie über mehrere Abonnements verfügen, müssen Sie das Abonnement auszuwählen, Sie verwenden möchten `azure account set <subscription id or name> true` in denen _Id oder Name des_ ist die Abonnement-Id oder den Namen des Abonnements an, die Sie in der aktuellen Sitzung arbeiten möchten.

Folgendes sollte angezeigt werden:

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### Schritt 4: Versetzen der Azure-CLI in den ARM-Modus

Um den ARM (Azure-Ressourcen-Manager)-Modus mit der Azure-Befehlszeilenschnittstelle zu verwenden, geben Sie `azure config mode arm` ein. Folgendes sollte angezeigt werden:

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE] Können Sie wechseln zurück zum Azure Service Management-Befehle verwenden `azure config mode asm`.


