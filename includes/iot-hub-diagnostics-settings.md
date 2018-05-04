### <a name="enable-logging-with-diagnostics-settings"></a>Aktivera loggning med diagnostikinställningar

1. Logga in på den [Azure-portalen] [ lnk-portal] och navigera till din IoT-hubb.
1. Välj **diagnostikinställningar**.
1. Välj **aktivera diagnostiken**.

   ![Slå på diagnostik][1]

1. Namnge diagnostikinställningar för.
1. Välj om du vill skicka loggar. Du kan välja valfri kombination av tre alternativ:
   * Arkivera till ett lagringskonto
   * Strömma till en händelsehubb
   * Skicka till Log Analytics
1. Välj vilka åtgärder som du vill övervaka och aktivera loggar för dessa åtgärder. De åtgärder som diagnostikinställningar kan rapportera om är:
   * Anslutningar
   * Enhetstelemetrin
   * Meddelanden moln till enhet
   * Enhetens identitet åtgärder
   * Filöverföringar
   * Meddelanderedigering
   * Moln till enhet dubbla åtgärder
   * Enhet till moln dubbla åtgärder
   * Dubbla åtgärder
   * Jobbåtgärder
   * Direkta metoder  
1. Spara de nya inställningarna. 

Om du vill aktivera diagnostikinställningar med PowerShell använder du följande kod:

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nya inställningar börjar gälla i cirka 10 minuter. Efter det loggar visas i konfigurerade mål-arkivering på den **diagnostikinställningar** bladet. Mer information om hur du konfigurerar diagnostik finns [samla in och använda loggdata från resurserna i azure][lnk-diagnostics-settings].

<!-- Images -->
[1]: ./media/iot-hub-diagnostics-settings/turnondiagnostics.png

<!-- Links -->
[lnk-portal]: https://portal.azure.com
[lnk-diagnostics-settings]: ../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
