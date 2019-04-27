---
title: Konfigurera Ruby-appar – Azure App Service
description: Den här självstudien beskrivs alternativ för att redigera och konfigurera Ruby-appar för Azure App Service i Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: 402c85e7902c8c2f612ad6c777d8f6773a4d0ca3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60854875"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Konfigurera en Linux-Ruby-app för Azure App Service

Den här artikeln beskrivs hur [Azure App Service](app-service-linux-intro.md) kör Ruby-appar och hur du kan anpassa beteendet för App Service när det behövs. Ruby-appar måste distribueras med alla de nödvändiga [pip](https://pypi.org/project/pip/) moduler.

Den här guiden innehåller viktiga begrepp och instruktioner för Ruby-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service ska du följa den [Ruby Snabbstart](quickstart-ruby.md) och [Ruby med PostgreSQL självstudiekursen](tutorial-ruby-postgres-app.md) första.

## <a name="show-ruby-version"></a>Visa Ruby-version

För att visa den aktuella versionen för Ruby, kör du följande kommando den [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Kör följande kommando för att visa alla Ruby-versioner som stöds, den [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Du kan köra en version av Ruby genom att skapa en egen behållaravbildning i stället. Mer information finns i [Använda anpassad Docker-avbildning](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Ange Ruby-version

Kör följande kommando den [Cloud Shell](https://shell.azure.com) att ställa in Ruby-version på 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Om du ser fel som liknar följande under distributionen:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> eller
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Det innebär att Ruby-versionen konfigureras i ditt projekt är annorlunda än den version som är installerad i behållaren som du kör (`2.3.3` i exemplet ovan). I exemplet ovan, markerar du båda *Gemfile* och *.ruby-version* och kontrollera att Ruby-version inte har angetts eller är inställd på den version som är installerad i behållaren som du kör (`2.3.3` i den exemplet ovan).

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [konfigurera appinställningar](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) utanför din Appkod. Du kan använda dem med hjälp av standard [ENV ['< sökväg-name >']](https://ruby-doc.org/core-2.3.3/ENV.html) mönster. Om du till exempel vill få åtkomst till en appinställning med namnet `WEBSITE_SITE_NAME` använder du följande kod:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Anpassa distributionen

När du distribuerar en [Git-lagringsplats](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), eller en [Zip-paketet](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) med produktionsprocesser påslaget, distributionsmotorn (Kudu) körs automatiskt efter distributionen följande som standard:

1. Kontrollera om en *Gemfile* finns.
1. Kör `bundle clean`. 
1. Kör `bundle install --path "vendor/bundle"`.
1. Kör `bundle package` till paketet gems till leverantör/cachemappen.

### <a name="use---without-flag"></a>Använd--utan flaggan

Att köra `bundle install` med den [--utan](https://bundler.io/man/bundle-install.1.html) flaggan genom att ange den `BUNDLE_WITHOUT` [appinställningen](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) till en kommaavgränsad lista över grupper. Till exempel följande kommando anger till `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Om den här inställningen har definierats så distributionsmotorn körs `bundle install` med `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Förkompilera tillgångar

Steg efter distribution förkompilera inte tillgångar som standard. Om du vill aktivera tillgången precompilation, ange den `ASSETS_PRECOMPILE` [appinställningen](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) till `true`. Sedan kommandot `bundle exec rake --trace assets:precompile` körs i slutet av stegen efter distributionen. Exempel:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Mer information finns i [leverera statiska resurser](#serve-static-assets).

## <a name="customize-start-up"></a>Anpassa start

Som standard startar behållaren Ruby Rails-servern i följande ordning (Mer information finns i den [Start skriptet](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Generera en [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) värde, om det inte finns redan. Det här värdet krävs för programmet i Produktionsläge.
1. Ange den `RAILS_ENV` miljövariabeln till `production`.
1. Ta bort alla *.pid* fil i den *tmp/PID* katalog som återstår av en tidigare pågående Rails-server.
1. Kontrollera om alla beroenden är installerade. Om inte, försök installera gems från lokalt *leverantör/cache* directory.
1. Kör `rails server -e $RAILS_ENV`.

Du kan anpassa startprocessen på följande sätt:

- [Leverera statiska resurser](#serve-static-assets)
- [Kör i läget för icke-produktion](#run-in-non-production-mode)
- [Ange secret_key_base manuellt](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Leverera statiska resurser

Rails-servern i Ruby behållaren som körs i Produktionsläge som standard och [förutsätter att tillgångar är förkompilerad version och hanteras av webbservern](https://guides.rubyonrails.org/asset_pipeline.html#in-production). För att leverera statiska resurser från Rails-servern, måste du göra två saker:

- **Förkompilera tillgångar** - [förkompilera statiska resurser lokalt](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) och distribuera dem manuellt. Eller låt distributionsmotorn hantera den i stället (se [förkompilera tillgångar](#precompile-assets).
- **Aktivera betjänar statiska filer** – för att leverera statiska resurser från behållaren Ruby genom att ange den `RAILS_SERVE_STATIC_FILES` [ange den `RAILS_SERVE_STATIC_FILES` appinställningen](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) till `true`. Exempel:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Kör i läget för icke-produktion

Rails-servern körs i Produktionsläge som standard. Om du vill köra i utvecklingsläge, till exempel den `RAILS_ENV` [appinställningen](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) till `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Den här inställningen som enbart gör dock Rails-servern att starta i utvecklingsläge som tar emot localhost begär bara och inte är tillgängligt utanför behållaren. För att acceptera begäranden för fjärrklient, ange den `APP_COMMAND_LINE` [appinställningen](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) till `rails server -b 0.0.0.0`. Den här appinställningen kan du köra ett kommando i behållaren Ruby. Exempel:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secretkeybase-manually"></a>Ange secret_key_base manuellt

Att använda din egen `secret_key_base` värde i stället för att låta App Service Generera en åt dig genom att ange den `SECRET_KEY_BASE` [appinställningen](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) med värdet som du vill. Exempel:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsare

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Rails-app med PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [App Service Linux vanliga frågor och svar](app-service-linux-faq.md)