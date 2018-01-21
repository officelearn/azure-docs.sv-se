## <a name="create-a-ruby-application"></a>Skapa ett Ruby-program
Instruktioner finns i [skapa ett Ruby-program på Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet att använda Service Bus
Om du vill använda Service Bus, hämtar och använder Azure Ruby-paketet, som innehåller en uppsättning bekvämlighet bibliotek som kommunicerar med storage REST-tjänster.

### <a name="use-rubygems-to-obtain-the-package"></a>Använda RubyGems för att hämta paketet
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac), eller **Bash** (Unix).
2. Skriv ”symbolen installera azure” i kommandofönstret att installera symbolen och beroenden.

### <a name="import-the-package"></a>Importera paketet
Med hjälp av valfri textredigerare, lägger du till följande upp i filen Ruby som du tänker använda lagring:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Konfigurera en Service Bus-anslutning
Använd följande kod för att ange värden för namnområde, namnet på nyckeln, nyckel, Signerare och värden:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Ange namnområdesvärdet för det värde som du skapade i stället för hela URL: en. Till exempel använda **”yourexamplenamespace”**, inte ”yourexamplenamespace.servicebus.windows.net”.
