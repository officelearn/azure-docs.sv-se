---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 16ce537a54fc77fc0f72b859d6d193501d86c1fc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "67187576"
---
## <a name="create-a-ruby-application"></a>Skapa ett Ruby-program
Instruktioner finns i [Skapa ett Ruby-program på Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera ditt program för att använda servicebuss
Om du vill använda Service Bus laddar du ned och använder Azure Ruby-paketet, som innehåller en uppsättning bekvämlighetsbibliotek som kommunicerar med REST-lagringstjänsterna.

### <a name="use-rubygems-to-obtain-the-package"></a>Hämta paketet med hjälp av RubyGems
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix).
2. Skriv "gem install azure" i kommandofönstret för att installera pärla och beroenden.

### <a name="import-the-package"></a>Importera paketet
Med hjälp av din favorittextredigerare lägger du till följande längst upp i Ruby-filen där du tänker använda lagring:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Konfigurera en servicebussanslutning
Använd följande kod för att ange värden för namnområde, namn på nyckeln, nyckeln, undertecknaren och värden:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Ange namnområdesvärdet till det värde du skapade i stället för hela URL:en. Använd till exempel **"dittexamplenamespace"**, inte "yourexamplenamespace.servicebus.windows.net".
