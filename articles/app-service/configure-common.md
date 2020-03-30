---
title: Konfigurera appar i portalen
description: Lär dig att konfigurera vanliga inställningar för en App Service-app i Azure-portalen. Appinställningar, anslutningssträngar, plattform, språkstack, behållare, etc.
keywords: azure app tjänst, webbapp, appinställningar, miljövariabler
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: seodec18
ms.openlocfilehash: ce0a170a629f347e2687a2e9f63fb3438fe2bd2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280175"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Konfigurera en App Service-app i Azure-portalen

I det här avsnittet beskrivs hur du konfigurerar vanliga inställningar för webbappar, mobila serverdels- eller API-appar med [Azure-portalen].

## <a name="configure-app-settings"></a>Konfigurera appinställningar

I App Service är appinställningar variabler som skickas som miljövariabler till programkoden. För Linux-appar och anpassade behållare skickar App Service `--env` appinställningar till behållaren med flaggan för att ställa in miljövariabeln i behållaren.

Sök efter och välj **App Services**i [Azure-portalen]och välj sedan din app. 

![Sök efter App Services](./media/configure-common/search-for-app-services.png)

Välj **Inställningar för konfigurationsprogram** > **på**appens vänstra meny .

![Programinställningar](./media/configure-common/open-ui.png)

För ASP.NET och ASP.NET Core-utvecklare är inställningen av appinställningar i `<appSettings>` App Service som att ange dem i *Web.config* eller *appsettings.json*, men värdena i App Service åsidosätter de i *Web.config* eller *appsettings.json*. Du kan behålla utvecklingsinställningar (till exempel lokalt MySQL-lösenord) i *Web.config* eller *appsettings.json*, men produktionshemligheter (till exempel Azure MySQL-databaslösenord) säkert i App Service. Samma kod använder dina utvecklingsinställningar när du felsöker lokalt och använder dina produktionshemligheter när de distribueras till Azure.

Andra språkstaplar får också appinställningarna som miljövariabler vid körning. För språkstackspecifika steg finns i:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [Php](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Anpassade containrar](containers/configure-custom-container.md#configure-environment-variables)

Appinställningar krypteras alltid när de lagras (krypterad efter vila).

> [!NOTE]
> Appinställningar kan också lösas från [Key Vault](/azure/key-vault/) med hjälp av [Key Vault-referenser](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Visa dolda värden

Som standard döljs värden för appinställningar i portalen för säkerhet. Om du vill visa ett dolt värde för en appinställning klickar du på fältet **Värde** för den inställningen. Om du vill visa värdena för alla appinställningar klickar du på knappen **Visa värde.**

### <a name="add-or-edit"></a>Lägga till eller redigera

Om du vill lägga till en ny appinställning klickar du på **Ny programinställning**. I dialogrutan kan du [hålla fast inställningen på den aktuella platsen](deploy-staging-slots.md#which-settings-are-swapped).

Om du vill redigera en inställning klickar du på knappen **Redigera** till höger.

När du är klar klickar du på **Uppdatera**. Glöm inte att klicka på **Spara** tillbaka på **sidan Konfiguration.**

> [!NOTE]
> I en standardbehållare för Linux eller en anpassad Linux-behållare måste alla `ApplicationInsights:InstrumentationKey` kapslade JSON-nyckelstrukturer i appinställningsnamnet som konfigureras i App Service som `ApplicationInsights__InstrumentationKey` för nyckelnamnet. Med andra ord `:` bör alla `__` ersättas med (dubbel understreck).
>

### <a name="edit-in-bulk"></a>Redigera i grupp

Om du vill lägga till eller redigera appinställningar i grupp klickar du på knappen **Avancerad redigering.** När du är klar klickar du på **Uppdatera**. Glöm inte att klicka på **Spara** tillbaka på **sidan Konfiguration.**

Appinställningarna har följande JSON-formatering:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>Konfigurera anslutningssträngar

Sök efter och välj **App Services**i [Azure-portalen]och välj sedan din app. Välj **Inställningar för konfigurationsprogram** > **på**appens vänstra meny .

![Programinställningar](./media/configure-common/open-ui.png)

För ASP.NET och ASP.NET Core-utvecklare är inställningen av anslutningssträngar i `<connectionStrings>` App Service som att ange dem i *Web.config*, men de värden som du anger i App Service åsidosätter de i *Web.config*. Du kan behålla utvecklingsinställningar (till exempel en databasfil) i *Web.config* och produktionshemligheter (till exempel SQL Database-autentiseringsuppgifter) på ett säkert sätt i App Service. Samma kod använder dina utvecklingsinställningar när du felsöker lokalt och använder dina produktionshemligheter när de distribueras till Azure.

För andra språkstaplar är det bättre att använda [appinställningar](#configure-app-settings) i stället, eftersom anslutningssträngar kräver särskild formatering i variabeltangenterna för att komma åt värdena. Här är ett undantag: vissa Azure-databastyper säkerhetskopieras tillsammans med appen om du konfigurerar deras anslutningssträngar i din app. Mer information finns i [Vad som säkerhetskopieras](manage-backup.md#what-gets-backed-up). Om du inte behöver den här automatiska säkerhetskopian använder du appinställningar.

Vid körning är anslutningssträngar tillgängliga som miljövariabler, som föregås av följande anslutningstyper:

* SQL Server:`SQLCONNSTR_`
* Mysql:`MYSQLCONNSTR_`
* SQL-databas:`SQLAZURECONNSTR_`
* Anpassade:`CUSTOMCONNSTR_`

En MySql-anslutningssträng med namnet *connectionstring1* kan till `MYSQLCONNSTR_connectionString1`exempel nås som miljövariabel . För språkstackspecifika steg finns i:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [Php](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Anpassade containrar](containers/configure-custom-container.md#configure-environment-variables)

Anslutningssträngar krypteras alltid när de lagras (krypterad efter vila).

> [!NOTE]
> Anslutningssträngar kan också matchas från [Key Vault](/azure/key-vault/) med hjälp av [Key Vault-referenser](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Visa dolda värden

Som standard döljs värden för anslutningssträngar i portalen för säkerhet. Om du vill visa ett dolt värde för en anslutningssträng klickar du bara på fältet **Värde för** strängen. Om du vill visa värdena för alla anslutningssträngar klickar du på knappen **Visa värde.**

### <a name="add-or-edit"></a>Lägga till eller redigera

Om du vill lägga till en ny anslutningssträng klickar du på **Ny anslutningssträng**. I dialogrutan kan du [hålla anslutningssträngen till den aktuella platsen](deploy-staging-slots.md#which-settings-are-swapped).

Om du vill redigera en inställning klickar du på knappen **Redigera** till höger.

När du är klar klickar du på **Uppdatera**. Glöm inte att klicka på **Spara** tillbaka på **sidan Konfiguration.**

### <a name="edit-in-bulk"></a>Redigera i grupp

Om du vill lägga till eller redigera anslutningssträngar i grupp klickar du på knappen **Avancerad redigering.** När du är klar klickar du på **Uppdatera**. Glöm inte att klicka på **Spara** tillbaka på **sidan Konfiguration.**

Anslutningssträngar har följande JSON-formatering:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Konfigurera allmänna inställningar

Sök efter och välj **App Services**i [Azure-portalen]och välj sedan din app. Välj > **Konfigurationsinställningar**på appens vänstra meny . **Configuration**

![Allmänna inställningar](./media/configure-common/open-general.png)

Här kan du konfigurera några vanliga inställningar för appen. Vissa inställningar kräver att du [skalar upp till högre prisnivåer](manage-scale-up.md).

- **Stack-inställningar:** Programvaran stacken för att köra appen, inklusive språk- och SDK-versioner. För Linux-appar och anpassade behållarappar kan du också ange ett valfritt startkommando eller en valfri fil.
- **Plattformsinställningar:** Låter dig konfigurera inställningar för värdplattformen, inklusive:
    - **Bithet:** 32-bitars eller 64-bitars.
    - **WebSocket protokoll**: För [ASP.NET SignalR] eller [socket.io](https://socket.io/), till exempel.
    - **Alltid på**: Håll appen laddad även när det inte finns någon trafik. Det krävs för kontinuerliga WebJobs eller för WebJobs som utlöses med ett CRON-uttryck.
      > [!NOTE]
      > Med funktionen Alltid på kan du inte styra slutpunkten. Den skickar alltid en begäran till programroten.
    - **Hanterad pipeline-version:** [IIS-pipelineläge]. Ställ in den på **Classic** om du har en äldre app som kräver en äldre version av IIS.
    - **HTTP-version**: Ställ in på **2.0** för att aktivera stöd för [HTTPS/2-protokoll.](https://wikipedia.org/wiki/HTTP/2)
    > [!NOTE]
    > De flesta moderna webbläsare stöder endast HTTP/2-protokoll via TLS, medan icke-krypterad trafik fortsätter att använda HTTP/1.1. Skydda [ditt anpassade DNS-namn med en SSL-bindning i Azure App Service](configure-ssl-bindings.md)för att säkerställa att klientwebbläsare ansluter till din app med HTTP/2 .
    - **ARR-tillhörighet**: I en distribution med flera instanser, se till att klienten dirigeras till samma instans under sessionens livstid. Du kan ställa in det här alternativet till **Av** för tillståndslösa program.
- **Felsökning:** Aktivera fjärrfelsökning för [ASP.NET,](troubleshoot-dotnet-visual-studio.md#remotedebug) [ASP.NET Core-](/visualstudio/debugger/remote-debugging-azure)eller [Node.js-appar.](containers/configure-language-nodejs.md#debug-remotely) Det här alternativet stängs av automatiskt efter 48 timmar.
- **Inkommande klientcertifikat:** kräver klientcertifikat i [ömsesidig autentisering](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Konfigurera standarddokument

Den här inställningen är endast för Windows-appar.

Sök efter och välj **App Services**i [Azure-portalen]och välj sedan din app. Välj **Konfigurationsstandarddokument** > **Default documents**på appens vänstra meny.

![Standarddokument](./media/configure-common/open-documents.png)

Standarddokumentet är webbsidan som visas vid rotadressen för en webbplats. Den första matchande filen i listan används. Om du vill lägga till ett nytt standarddokument klickar du på **Nytt dokument**. Glöm inte att klicka på **Spara**.

Om appen använder moduler som cirkulerar baserat på URL i stället för att visa statiskt innehåll, finns det inget behov av standarddokument.

## <a name="configure-path-mappings"></a>Konfigurera sökvägsmappningar

Sök efter och välj **App Services**i [Azure-portalen]och välj sedan din app. På appens vänstra meny väljer du **Mappningar av** > **konfigurationssökväg .**

![Sökvägsmappningar](./media/configure-common/open-path.png)

Sidan **Sökvägsmappningar** visar olika saker baserat på os-typen.

### <a name="windows-apps-uncontainerized"></a>Windows-appar (ej invecklade)

För Windows-appar kan du anpassa IIS-hanterare och virtuella program och kataloger.

Med hanterare kan du lägga till anpassade skriptprocessorer för att hantera begäranden om specifika filtillägg. Om du vill lägga till en anpassad hanterare klickar du på **Ny hanterare**. Konfigurera hanteraren enligt följande:

- **Förlängning**. Filändelsen du vill hantera, till exempel * \*.php* eller *handler.fcgi*.
- **Skriptprocessor**. Den absoluta sökvägen för skriptprocessorn till dig. Begäranden till filer som matchar filtillägget bearbetas av skriptprocessorn. Använd sökvägen `D:\home\site\wwwroot` för att referera till appens rotkatalog.
- **Argument**. Valfria kommandoradsargument för skriptprocessorn.

Varje app har standardrotsökvägen`/`( `D:\home\site\wwwroot`) mappad till , där koden distribueras som standard. Om approten finns i en annan mapp, eller om databasen har mer än ett program, kan du redigera eller lägga till virtuella program och kataloger här. Klicka på **Nytt virtuellt program eller katalog**.

Om du vill konfigurera virtuella program och kataloger anger du varje`D:\home`virtuell katalog och dess motsvarande fysiska sökväg i förhållande till webbplatsroten ( ). Du kan också markera kryssrutan **Program för** att markera en virtuell katalog som ett program.

### <a name="containerized-apps"></a>Appar med behållare

Du kan [lägga till anpassad lagring för din behållarapp](containers/how-to-serve-content-from-azure-storage.md). Containerized apps inkluderar alla Linux-appar och även Windows och Linux anpassade behållare som körs på App Service. Klicka på **Ny Azure Storage Mount** och konfigurera din anpassade lagring enligt följande:

- **Namn**: Visningsnamnet.
- **Konfigurationsalternativ**: **Grundläggande** eller **Avancerat**.
- **Lagringskonton**: Lagringskontot med den behållare du vill använda.
- **Lagringstyp:** **Azure Blobbar** eller **Azure-filer**.
  > [!NOTE]
  > Windows-behållarappar stöder bara Azure-filer.
- **Förvaringsbehållare**: För grundläggande konfiguration, den behållare du vill ha.
- **Resursnamn**: För avancerad konfiguration, filresursnamnet.
- **Åtkomstnyckel:** För avancerad konfiguration, åtkomstnyckeln.
- **Montera bana:** Den absoluta sökvägen i behållaren för att montera den anpassade lagringen.

Mer information finns i [Hantera innehåll från Azure Storage i App Service på Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Konfigurera inställningar för språkstapel

För Linux-appar, se:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [Php](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Konfigurera anpassade behållare

Se [Konfigurera en anpassad Linux-behållare för Azure App Service](containers/configure-custom-container.md)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera ett anpassat domännamn i Azure App Service]
- [Konfigurera mellanlagringsmiljöer i Azure App Service]
- [Skydda ett anpassat DNS-namn med en SSL-bindning i Azure App Service](configure-ssl-bindings.md)
- [Aktivera diagnostikloggar](troubleshoot-diagnostic-logs.md)
- [Skala en app i Azure App Service]
- [Övervaka grunderna i Azure App Service]
- [Ändra applicationHost.config-inställningar med applicationHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurera ett anpassat domännamn i Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Konfigurera mellanlagringsmiljöer i Azure App Service]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Övervaka grunderna i Azure App Service]: ./web-sites-monitor.md
[rörledningsläge]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Skala en app i Azure App Service]: ./manage-scale-up.md
