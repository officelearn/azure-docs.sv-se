---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: aec13c6beb8dbfcdd5f38e7f96b86bf03e42fa37
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80986636"
---
## <a name="create-a-ruby-application"></a>Skapa ett ruby-program
Instruktioner finns i [skapa ett ruby-program på Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet så att det använder Service Bus
Om du vill använda Service Bus laddar du ned och använder Azure ruby-paketet, som innehåller en uppsättning av bekvämlighets bibliotek som kommunicerar med lagrings REST tjänsterna.

### <a name="use-rubygems-to-obtain-the-package"></a>Hämta paketet med hjälp av RubyGems
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix).
2. Skriv "gem install Azure" i kommando fönstret för att installera symbolen och beroendena.

### <a name="import-the-package"></a>Importera paketet
Använd din favorit text redigerare och Lägg till följande längst upp i ruby-filen där du vill använda lagring:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Konfigurera en Service Bus anslutning
Använd följande kod för att ange värden för namn området, namnet på nyckeln, nyckeln, undertecknaren och värden:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Ange värdet för namn området till det värde som du skapade i stället för hela URL: en. Använd till exempel **"yourexamplenamespace"**, inte "yourexamplenamespace.ServiceBus.Windows.net".

När du arbetar med flera namn rymder kan du skicka nyckeln och dess namn till konstruktorn när du `SharedAccessSigner` skapar objekt

```ruby
sb_namespace = '<your azure service bus namespace>'
sb_sas_key_name = '<your azure service bus access keyname>'
sb_sas_key = '<your azure service bus access key>'

signer = Azure::ServiceBus::Auth::SharedAccessSigner.new(sb_sas_key_name, sb_sas_key)
sb_host = "https://#{sb_namespace}.servicebus.windows.net"
```
