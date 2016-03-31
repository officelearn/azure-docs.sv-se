<properties services="virtual-machines" title="Setting up Azure CLI for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## Verwenden der Azure-Befehlszeilenschnittstelle

Die folgenden Schritte unterstützen Sie bei der Verwendung der Azure-Befehlszeilenschnittstelle mit der neuesten Version und dem richtigen Abonnement. Wenn Sie müssen zum Installieren der Azure-CLI und verbinden Sie es zuerst mit Ihrem Konto finden Sie unter der [Azure-Befehlszeilenschnittstelle (Azure CLI)](xplat-cli-install.md).

### Schritt 1: Aktualisieren der Azure-CLI-Version

Um die Azure-Befehlszeilenschnittstelle für Befehle im Dienstverwaltungsmodus zu verwenden, sollten Sie über eine möglichst neue Version verfügen. Geben Sie zum Überprüfen Ihrer Version `azure --version` ein. Die Ausgabe sollte folgendermaßen aussehen:

    $ azure --version
    0.8.17 (node: 0.10.25)

Wenn Sie Ihre Version von Azure-Befehlszeilenschnittstelle aktualisieren möchten, finden Sie unter [Befehlszeilenschnittstelle](https://github.com/Azure/azure-xplat-cli).

### Schritt 2: Festlegen von Azure-Konto und -Abonnement

Nachdem Sie die Azure-Befehlszeilenschnittstelle mit dem zu verwendenden Konto verbunden haben, können Sie mehrere Abonnements verwenden. Wenn Sie dies tun, sollten Sie den verfügbaren Abonnements für Ihr Konto durch Eingabe `azure account list`, und wählen Sie dann das Abonnement, das Sie verwenden möchten `azure account set <subscription id or name> true` in denen _Id oder Name des_ ist die Abonnement-Id oder den Namen des Abonnements an, die Sie in der aktuellen Sitzung arbeiten möchten. Folgendes sollte angezeigt werden:

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE] Wenn nicht bereits ein Azure-Konto haben, aber ein Abonnement MSDN-Abonnement haben, erhalten Sie kostenlose Azure-Gutschriften durch Aktivieren der [MSDN-Abonnent profitieren hier](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) -- oder kostenlose Konto verwenden. Beides ist für den Azure-Zugriff möglich.


