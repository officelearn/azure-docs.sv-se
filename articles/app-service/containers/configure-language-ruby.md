---
title: Konfigurera ruby-appar – Azure App Service
description: Lär dig hur du konfigurerar en fördefinierad ruby-behållare för din app. Den här artikeln visar de vanligaste konfigurations åtgärderna.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: seodec18
ms.openlocfilehash: b17bec5663cc8e9d199ad79bb5282b052b8c0182
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670395"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Konfigurera en Linux Ruby-app för Azure App Service

I den här artikeln beskrivs hur [Azure App Service](app-service-linux-intro.md) kör ruby-appar och hur du kan anpassa app service när det behövs. Ruby-appar måste distribueras med alla nödvändiga [pip](https://pypi.org/project/pip/) -moduler.

Den här guiden innehåller viktiga begrepp och instruktioner för ruby-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service bör du först följa anvisningarna för [ruby-snabb](quickstart-ruby.md) starten och [ruby med postgresql](tutorial-ruby-postgres-app.md) .

## <a name="show-ruby-version"></a>Visa ruby-version

Om du vill visa den aktuella ruby-versionen kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Om du vill visa alla ruby-versioner som stöds kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Du kan köra en version av ruby som inte stöds genom att skapa en egen behållar avbildning i stället. Mer information finns i [Använda anpassad Docker-avbildning](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Ange ruby-version

Kör följande kommando i [Cloud Shell](https://shell.azure.com) för att ange ruby-versionen till 2,3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Om du ser fel som liknar följande under distributions tiden:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> eller
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Det innebär att ruby-versionen som kon figurer ATS i projektet skiljer sig från den version som är installerad i den behållare som du kör (`2.3.3` i exemplet ovan). I exemplet ovan, kontrol lera både *Gemfile* och *. ruby-version* och kontrol lera att ruby-versionen inte har angetts eller har angetts till den version som är installerad i den behållare som du kör (`2.3.3` i exemplet ovan).

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [Ange inställningar för appar](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) utanför appens kod. Sedan kan du komma åt dem med hjälp av standard- [Kuvert ["\<sökväg-namn >"]](https://ruby-doc.org/core-2.3.3/ENV.html) mönster. Om du till exempel vill få åtkomst till en appinställning med namnet `WEBSITE_SITE_NAME` använder du följande kod:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Anpassa distribution

När du distribuerar en [git-lagringsplats](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), eller ett [zip-paket](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) med skapande processer växlat, kör distributions motorn (kudu) automatiskt följande steg efter distributionen som standard:

1. Kontrol lera om en *Gemfile* finns.
1. Kör `bundle clean`. 
1. Kör `bundle install --path "vendor/bundle"`.
1. Kör `bundle package` för att paketera Gems i mappen leverantör/cache.

### <a name="use---without-flag"></a>Använd--utan flagga

Om du vill köra `bundle install` med flaggan [--utan](https://bundler.io/man/bundle-install.1.html) flagga anger du [inställningen `BUNDLE_WITHOUT` app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) till en kommaavgränsad lista över grupper. Följande kommando ställer till exempel in det för att `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Om den här inställningen definieras kör distributions motorn `bundle install` med `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Förkompilera till gångar

Stegen efter distributionen förkompilerar inte till gångar som standard. Aktivera för inkompilering av till gångar genom att ange `ASSETS_PRECOMPILE` [app-inställningen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) till `true`. Sedan körs kommandot `bundle exec rake --trace assets:precompile` i slutet av stegen efter distribution. Exempel:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Mer information finns i [Hantera statiska till gångar](#serve-static-assets).

## <a name="customize-start-up"></a>Anpassa start

Som standard startar ruby-behållaren räler-servern i följande ordning (mer information finns i [Start skriptet](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Generera ett [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) värde, om det inte redan finns ett. Det här värdet krävs för att appen ska kunna köras i produktions läge.
1. Ange `RAILS_ENV`-miljövariabeln till `production`.
1. Ta bort alla *. pid* -filer i katalogen *tmp/PID* som är kvar av en tidigare igång-räler-Server.
1. Kontrol lera om alla beroenden är installerade. Annars kan du försöka installera Gems från den lokala katalogen *Vendor/cache* .
1. Kör `rails server -e $RAILS_ENV`.

Du kan anpassa start processen på följande sätt:

- [Hantera statiska till gångar](#serve-static-assets)
- [Kör i icke-produktions läge](#run-in-non-production-mode)
- [Ange secret_key_base manuellt](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Hantera statiska till gångar

Räler-servern i ruby-behållaren körs som standard i produktions läge och [förutsätter att till gångar är förkompilerade och betjänas av webb servern](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Om du vill hantera statiska till gångar från räler-servern måste du göra två saker:

- **Förkompilera till gångarna** - [förkompilera statiska till gångar lokalt](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) och distribuera dem manuellt. Eller låt distributions motorn hantera den i stället (se [förkompilera till gångar](#precompile-assets).
- **Aktivera betjäning av statiska filer** – för att betjäna statiska till gångar från ruby-behållaren [anger du `RAILS_SERVE_STATIC_FILES` app-inställningen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) till `true`. Exempel:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Kör i icke-produktions läge

Räler-servern körs som standard i produktions läge. Om du vill köra i utvecklings läge kan du till exempel ställa in `RAILS_ENV` [app-inställningen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) på `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Den här inställningen gör dock att räler-servern startar i utvecklings läge, som enbart accepterar localhost-begäranden och inte är tillgänglig utanför behållaren. Om du vill acceptera förfrågningar om fjärrklienter ställer du in `APP_COMMAND_LINE` [app-inställningen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) på `rails server -b 0.0.0.0`. Med den här inställningen för appen kan du köra ett anpassat kommando i ruby-behållaren. Exempel:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a>Ange secret_key_base manuellt

Om du vill använda ett eget `secret_key_base` värde i stället för att låta App Service generera ett åt dig, ställer du in inställningen för `SECRET_KEY_BASE` [app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) med det värde som du vill använda. Exempel:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsare

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: räler-app med PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service Linux](app-service-linux-faq.md)
