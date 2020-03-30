---
title: Konfigurera Ruby-appar – Azure App Service
description: Läs om hur du konfigurerar en förbyggd Ruby-behållare för din app. Den här artikeln visar de vanligaste konfigurationsuppgifterna.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 8daebba840223d050a14b4b99cb6ae15472ee4f5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046330"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Konfigurera en Linux Ruby-app för Azure App Service

I den här artikeln beskrivs hur [Azure App Service](app-service-linux-intro.md) kör Ruby-appar och hur du kan anpassa apptjänstens beteende när det behövs. Ruby apps måste distribueras med alla nödvändiga [pärlor](https://rubygems.org/gems).

Den här guiden innehåller viktiga begrepp och instruktioner för Ruby-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service, bör du följa [Ruby snabbstart](quickstart-ruby.md) och [Ruby med PostgreSQL handledning](tutorial-ruby-postgres-app.md) först.

## <a name="show-ruby-version"></a>Visa Ruby-version

Om du vill visa den aktuella Ruby-versionen kör du följande kommando i [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Om du vill visa alla Ruby-versioner som stöds kör du följande kommando i [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Du kan köra en version av Ruby som inte stöds genom att skapa en egen behållaravbildning i stället. Mer information finns i [Använda anpassad Docker-avbildning](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Ange Ruby-version

Kör följande kommando i [Cloud Shell](https://shell.azure.com) för att ställa in Ruby-versionen till 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Om du ser fel som liknar följande under distributionstiden:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> eller
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Det innebär att Ruby-versionen som konfigurerats i projektet skiljer sig från den`2.3.3` version som är installerad i behållaren du kör (i exemplet ovan). I exemplet ovan kontrollerar du både *Gemfile* och RUBY-versionen och kontrollerar att *Ruby-versionen* inte är inställd, eller`2.3.3` är inställd på den version som är installerad i behållaren du kör (i exemplet ovan).

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [ställa in appinställningar](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) utanför appkoden. Sedan kan du komma åt dem med hjälp av standard [ENV['\<path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html) mönster. Om du till exempel vill få åtkomst till en appinställning med namnet `WEBSITE_SITE_NAME` använder du följande kod:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Anpassa distributionen

När du distribuerar en [Git-databas](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)eller ett [Zip-paket](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) med byggprocesser påslagna, kör distributionsmotorn (Kudu) automatiskt följande steg efter distributionen som standard:

1. Kontrollera om det finns en *Gemfile.*
1. Kör `bundle clean`. 
1. Kör `bundle install --path "vendor/bundle"`.
1. Kör `bundle package` till paket pärlor i leverantör / cache mapp.

### <a name="use---without-flag"></a>Använd --utan-flagga

Om `bundle install` du vill köra med flaggan [--without](https://bundler.io/man/bundle-install.1.html) ställer du in `BUNDLE_WITHOUT` [appinställningen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) på en kommaavgränsad lista över grupper. Följande kommando anger till exempel `development,test`det till .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Om den här inställningen har definierats körs `bundle install` distributionsmotorn med `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Förkompilering av tillgångar

Stegen efter distributionen förkompileras inte som standard. Om du vill aktivera förkompilering av tillgångar ställer du in `ASSETS_PRECOMPILE` [appinställningen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) på `true`. Sedan körs `bundle exec rake --trace assets:precompile` kommandot i slutet av stegen efter distributionen. Ett exempel:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Mer information finns i [Visa statiska tillgångar](#serve-static-assets).

## <a name="customize-start-up"></a>Anpassa start

Som standard startar Ruby-behållaren Rails-servern i följande ordning (mer information finns i [startskriptet):](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)

1. Generera ett [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) värde, om det inte redan finns något värde. Det här värdet krävs för att appen ska köras i produktionsläge.
1. Ställ `RAILS_ENV` in miljövariabeln på `production`.
1. Ta bort alla *PID-filer* i *tmp/pids-katalogen* som finns kvar av en rails-server som tidigare kördes.
1. Kontrollera om alla beroenden är installerade. Om inte, prova att installera pärlor från den lokala *leverantören / cache* katalogen.
1. Kör `rails server -e $RAILS_ENV`.

Du kan anpassa uppstartsprocessen på följande sätt:

- [Betjäna statiska tillgångar](#serve-static-assets)
- [Kör i icke-produktionsläge](#run-in-non-production-mode)
- [Ställ in secret_key_base manuellt](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Betjäna statiska tillgångar

Rails-servern i Ruby-behållaren körs som standard i produktionsläge och [förutsätter att tillgångarna är förkompilerade och betjänas av webbservern](https://guides.rubyonrails.org/asset_pipeline.html#in-production). För att betjäna statiska tillgångar från Rails-servern måste du göra två saker:

- **Förkompilera tillgångarna** - [Förkompilera de statiska tillgångarna lokalt](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) och distribuera dem manuellt. Du kan också låta distributionsmotorn hantera den i stället (se [Förkompilera tillgångar](#precompile-assets).
- **Aktivera visning av statiska filer** - Om du vill `true`betjäna statiska resurser från Ruby-behållaren ställer du in [ `RAILS_SERVE_STATIC_FILES` appinställningen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) på . Ett exempel:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Kör i icke-produktionsläge

Rails-servern körs som standard i produktionsläge. Om du vill köra i utvecklingsläge ställer du till exempel in `RAILS_ENV` [appinställningen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) på `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Den här inställningen gör dock att Rails-servern startar i utvecklingsläge, som endast accepterar localhost-begäranden och inte är tillgänglig utanför behållaren. Om du vill acceptera `APP_COMMAND_LINE` fjärrklientbegäranden ställer du in [appinställningen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) på `rails server -b 0.0.0.0`. Med den här appinställningen kan du köra ett anpassat kommando i Ruby-behållaren. Ett exempel:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a>Ställ in secret_key_base manuellt

Om du `secret_key_base` vill använda ditt eget värde i stället `SECRET_KEY_BASE` för att låta App Service generera en åt dig anger du [appinställningen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) med önskat värde. Ett exempel:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsaren

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Handledning: Rails app med PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service Linux](app-service-linux-faq.md)
