Azure PowerShell ist derzeit in zwei Versionen verfügbar: 1.0 und 0.9.8. Wenn Sie bereits Skripts haben und diese nicht sofort ändern möchten, können Sie weiterhin Version 0.9.8 verwenden. Bei der Verwendung der Version 1.0 sollten Sie Ihre Skripts sorgfältig in Präproduktionsumgebungen testen, bevor Sie sie in der Produktionsumgebung einsetzen, um unerwartete Folgen zu vermeiden.

1.0 Cmdlets folgen dem Benennungsmuster {Verb}-AzureRm {Nomen}; während die Spaltennamen keine schließen 0.9.8 **Rm** (z. B. neu-AzureRmResourceGroup anstelle von New-AzureResourceGroup). Wenn Sie Azure PowerShell 0.9.8 verwenden, müssen Sie zuerst den Ressourcen-Manager-Modus aktivieren, durch Ausführen der **Switch-AzureMode AzureResourceManager** Befehl. Dieser Befehl ist in 1.0 nicht erforderlich.

Nur der Version 1.0 werden neue Features hinzugefügt. Informationen zu Version 1.0, darunter auch Informationen zum Installieren und deinstallieren Sie die Version finden Sie unter [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).



