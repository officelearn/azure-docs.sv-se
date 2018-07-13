### <a name="troubleshoot-azure-diagnostics"></a>Felsöka Azure Diagnostics

Resursprovidern Microsoft.insights har inte registrerats om följande felmeddelande visas:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Utför följande steg för att registrera resursprovidern i Azure Portal:

1.  Klicka på *Prenumerationer* i navigeringsfönstret till vänster
2.  Välj den prenumeration som anges i felmeddelandet
3.  Klicka på *Resursprovidrar*
4.  Leta reda på *Microsoft Insights*-providern
5.  Klicka på *Registrera*

![Registrera resursprovidern microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

När resursprovidern *Microsoft.insights* har registrerats kan du prova att konfigurera diagnostik på nytt.


Om du får följande felmeddelande visas i PowerShell måste du uppdatera din version av PowerShell:

`Set-AzureRmDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Uppdatera din version av PowerShell till November 2016 (v2.3.0) eller senare, frigöra med hjälp av anvisningarna i den [Kom igång med Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) artikeln.
