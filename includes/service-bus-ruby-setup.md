---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 16ce537a54fc77fc0f72b859d6d193501d86c1fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472326"
---
## <a name="create-a-ruby-application"></a>Skapa en Ruby-program
Anvisningar finns i [skapa en Ruby-App i Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet för att använda Service Bus
Om du vill använda Service Bus, hämta och använda Azure Ruby-paket som innehåller en uppsättning bekvämlighet bibliotek som kommunicerar med storage REST-tjänster.

### <a name="use-rubygems-to-obtain-the-package"></a>Hämta paketet med hjälp av RubyGems
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix).
2. Skriv ”gem installera azure” i kommandofönstret att installera gem och beroenden.

### <a name="import-the-package"></a>Importera paketet
Med hjälp av valfri textredigerare, Lägg till följande överst i filen Ruby som du tänker använda lagring:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Skapa en Service Bus-anslutning
Använd följande kod för att ange värden för namnområdet, namnet på nyckeln, nyckel, Signerare och värden:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Namnområdesvärdet inställd på värdet som du skapade i stället för hela URL: en. Till exempel använda **”yourexamplenamespace”**, inte ”yourexamplenamespace.servicebus.windows.net”.
