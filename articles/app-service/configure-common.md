---
title: Konfigurera appar i portalen – Azure App Service
description: Lär dig att konfigurera vanliga inställningar för en App Service-app i Azure-portalen.
keywords: Azure apptjänst, webbapp, app-inställningar, miljövariabler
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bcc970375120f76e4ec8a90f487d251296f92dba
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957917"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Konfigurera en App Service-app i Azure portal

Det här avsnittet beskriver hur du konfigurerar vanliga inställningar för webbappar, mobil serverdel eller API med hjälp av den [Azure Portal].

## <a name="configure-app-settings"></a>Konfigurera appinställningar

I App Service använder du appinställningar som miljövariabler. I den [Azure Portal], gå till sidan för hantering av din app. I appens vänstra menyn, klickar du på **Configuration** > **programinställningar**.

![Programinställningar](./media/configure-common/open-ui.png)

För ASP.NET och ASP.NET Core-utvecklare är inställningen appinställningar i App Service som ställer in dem `<appSettings>` i *Web.config*, men värdena i App Service åsidosätta de i *Web.config*. Du kan behålla utvecklingsinställningar (t.ex. lokala MySQL lösenord) *Web.config*, men produktion hemligheter (t.ex. Azure MySQL-databaslösenord) säkra i App Service. Samma kod använder utvecklingsinställningarna för när du felsöker lokalt och den använder din produktion hemligheter när de distribueras till Azure.

Andra språk-stackar, på samma sätt kan få programinställningar som miljövariabler vid körning. Språk-stack finns specifika steg:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Anpassad behållare](containers/configure-custom-container.md#configure-environment-variables)

Appinställningar är alltid krypterade när lagrade (krypterat i vila).

> [!NOTE]
> Appinställningar kan också vara löst ur [Key Vault](/azure/key-vault/) med [Key Vault refererar till](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Visa dolda värden

Som standard värden för app-inställningar som är dolda i portalen för säkerhet. Om du vill se en dold värdet för en app-inställning klickar du på den **värdet** fältet för den här inställningen. Om du vill se värdena för alla appinställningar klickar du på den **Visa värde** knappen.

### <a name="add-or-edit"></a>Lägg till eller redigera

Lägg till en ny appinställning, klicka på **nya programinställning**. I dialogrutan kan du [behålls inställningen i aktuell plats](deploy-staging-slots.md#which-settings-are-swapped).

Om du vill redigera en inställning klickar du på den **redigera** knappen på höger sida.

När du är klar klickar du på **uppdatering**. Glöm inte att klicka på **spara** i den **Configuration** sidan.

> [!NOTE]
> I en standard Linux-behållare eller en anpassad Linux-behållare, alla kapslad JSON-nyckelstruktur i namnet på inställningen som `ApplicationInsights:InstrumentationKey` måste konfigureras i App Service som `ApplicationInsights__InstrumentationKey` som nyckelnamn. Med andra ord någon `:` ska ersättas med `__` (dubbla understreck).
>

### <a name="edit-in-bulk"></a>Redigera i grupp

Om du vill lägga till eller redigera appinställningar gruppvis genom att klicka på den **avancerade redigera** knappen. När du är klar klickar du på **uppdatering**. Glöm inte att klicka på **spara** i den **Configuration** sidan.

Appinställningar har följande JSON formatering:

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

I den [Azure Portal], gå till appens management-sidan. I appens vänstra menyn, klickar du på **Configuration** > **programinställningar**.

![Programinställningar](./media/configure-common/open-ui.png)

För ASP.NET och ASP.NET Core-utvecklare är inställningen anslutningssträngar i App Service som ställer in dem `<connectionStrings>` i *Web.config*, men värden som du angav i App Service åsidosätta de i *Web.config*. Du kan behålla utvecklingsinställningar (t.ex. en databasfil) *Web.config* och produktion hemligheter (t.ex. SQL Database-autentiseringsuppgifter) säkra i App Service. Samma kod använder utvecklingsinställningarna för när du felsöker lokalt och den använder din produktion hemligheter när de distribueras till Azure.

För andra språk-stackar, är det bättre att använda [appinställningar](#configure-app-settings) i stället eftersom anslutningssträngar kräver särskild formatering i variabeln nycklarna i för att få åtkomst till värden. Här är ett undantag, men: vissa databastyper av Azure-som säkerhetskopieras tillsammans med appen om du konfigurerar sina anslutningssträngar i din app. Mer information finns i [vad säkerhetskopieras](manage-backup.md#what-gets-backed-up). Om du inte behöver den här automatisk säkerhetskopiering, använder du appinställningar.

Vid körning, anslutningssträngar som är tillgängliga som miljövariabler, föregås av följande anslutningstyper:

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL-databas: `SQLAZURECONNSTR_`
* Custom: `CUSTOMCONNSTR_`

Till exempel en anslutningssträng för MySql med namnet *connectionstring1* kan nås som miljövariabeln `MYSQLCONNSTR_connectionString1`. Språk-stack finns specifika steg:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Anpassad behållare](containers/configure-custom-container.md#configure-environment-variables)

Anslutningssträngar krypteras alltid när lagras (krypterat i vila).

> [!NOTE]
> Anslutningssträngar kan också vara löst ur [Key Vault](/azure/key-vault/) med [Key Vault refererar till](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Visa dolda värden

Som standard värden för anslutningssträngar som är dolda i portalen för säkerhet. Om du vill se en dold värdet för en anslutningssträng, klicka bara på den **värdet** i strängen. Om du vill se värdena för alla anslutningssträngar, klickar du på den **Visa värde** knappen.

### <a name="add-or-edit"></a>Lägg till eller redigera

Lägg till en ny anslutningssträng, klicka på **ny anslutningssträng**. I dialogrutan kan du [behålls anslutningssträngen i aktuell plats](deploy-staging-slots.md#which-settings-are-swapped).

Om du vill redigera en inställning klickar du på den **redigera** knappen på höger sida.

När du är klar klickar du på **uppdatering**. Glöm inte att klicka på **spara** i den **Configuration** sidan.

### <a name="edit-in-bulk"></a>Redigera i grupp

Om du vill lägga till eller redigera anslutningssträngar gruppvis genom att klicka på den **avancerade redigera** knappen. När du är klar klickar du på **uppdatering**. Glöm inte att klicka på **spara** i den **Configuration** sidan.

Anslutningssträngar har följande JSON formatering:

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

I den [Azure Portal], gå till appens management-sidan. I appens vänstra menyn, klickar du på **Configuration** > **programinställningar**.

![Allmänna inställningar](./media/configure-common/open-general.png)

Här kan du konfigurera vissa vanliga inställningar för appen. Vissa inställningar kräver att du [skala upp till högre prisnivåer](web-sites-scale.md).

- **Stack inställningar**: Programvarustack att köra appen, inklusive språk och SDK-versioner. För Linux-appar och anpassade behållarappar, kan du också ange ett valfritt startkommando eller en fil.
- **Plattformsinställningarna**: Du kan konfigurera inställningar för värdplattform, inklusive:
    - **Bitarna**: 32-bitars eller 64-bitars.
    - **WebSocket-protokoll**: För [ASP.NET SignalR] eller [socket.io](https://socket.io/), till exempel.
    - **Always On**: Behåll appen läses in även när det finns ingen trafik. Du måste aktivera det för kontinuerliga WebJobs eller för WebJobs som utlöses med hjälp av ett CRON-uttryck.
    - **Hanterad pipelineversion**: IIS [Pipeline-läge]. Ange den till **klassiska** om du har en äldre app som kräver en äldre version av IIS.
    - **HTTP-version**: Ange **2.0** aktivera stöd för [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) protokoll.
    > [!NOTE]
    > De flesta moderna webbläsare stöder HTTP/2-protokollet via TLS blir bara, medan icke-krypterad trafik fortsätter att använda HTTP/1.1. Se till att webbläsare ansluta till din app med HTTP/2, antingen [köpa ett App Service Certificate](web-sites-purchase-ssl-web-site.md) för appens anpassade domän eller [bind ett certifikat från tredje part](app-service-web-tutorial-custom-ssl.md).
    - **ARR-tillhörighet**: Se till att klienten dirigeras till samma-instans för resten av sessionen i en distribution med flera instanser. Du kan välja alternativet **av** för tillståndslösa program.
- **Felsökning**: Aktivera fjärrfelsökning för [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure), eller [Node.js](containers/configure-language-nodejs.md#debug-remotely) appar. Det här alternativet inaktiverar automatiskt efter 48 timmar.
- **Inkommande klientcertifikat**: Kräv klientcertifikat i [ömsesidig autentisering](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Konfigurera standarddokument

Den här inställningen är endast för Windows-appar.

I den [Azure Portal], gå till appens management-sidan. I appens vänstra menyn, klickar du på **Configuration** > **standard dokument**.

![Allmänna inställningar](./media/configure-common/open-documents.png)

Standarddokument är den webbsida som visas på rot-URL för en webbplats. Den första matchande filen i listan används. Lägg till ett nytt standarddokument, klicka på **nytt dokument**. Glöm inte att klicka på **spara**.

Om appen använder moduler som dirigerar baserat på URL i stället för att serva statiskt innehåll, finns det inget behov av standarddokument.

## <a name="configure-path-mappings"></a>Konfigurera sökvägen mappningar

I den [Azure Portal], gå till appens management-sidan. I appens vänstra menyn, klickar du på **Configuration** > **sökväg mappningar**.

![Allmänna inställningar](./media/configure-common/open-path.png)

Den **sökväg mappningar** sidan visar olika saker, beroende på vilken typ av operativsystem.

### <a name="windows-apps-uncontainerized"></a>Windows-appar (uncontainerized)

Du kan anpassa IIS Hanterarmappningar och virtuella program och kataloger för Windows-appar.

Hanterarmappningar kan du lägga till processorer för anpassat skript för att hantera begäranden för specifika filtillägg. Om du vill lägga till en anpassad hanterare, klickar du på **nya hanteraren**. Konfigurera hanteraren enligt följande:

- **Tillägget**. Filnamnstillägg som du vill hantera, till exempel  *\*.php* eller *handler.fcgi*.
- **Skriptprocessor**. Den absoluta sökvägen till Skriptprocessor till dig. Begäranden om filer som matchar filnamnstillägget bearbetas av Skriptprocessor. Använd sökvägen med `D:\home\site\wwwroot` att referera till rotkatalogen för din app.
- **Argument**. Valfria kommandoradsargument för Skriptprocessor.

Varje app har standard rotsökvägen (`/`) mappas till `D:\home\site\wwwroot`, där din kod distribueras som standard. Om din app rot är i en annan mapp, eller om din databas har mer än ett program, kan du redigera eller lägga till virtuella program och kataloger här. Klicka på **ny virtuell installation eller katalog**.

För att konfigurera virtuella program och kataloger, ange varje virtuell katalog och dess motsvarande fysisk sökväg i förhållande till webbplatsroten (`D:\home`). Du kan också markera den **program** kryssrutan för att markera en virtuell katalog som ett program.

### <a name="containerized-apps"></a>Behållarbaserade appar

Du kan [Lägg till anpassad lagring för dina behållarbaserad app](containers/how-to-serve-content-from-azure-storage.md). Behållarbaserade appar omfattar alla Linux-appar och Windows- och Linux anpassad behållare som körs i App Service. Klicka på **nya Azure-lagring och montera** och konfigurera din anpassade lagring på följande sätt:

- **Namn på**: Visningsnamnet.
- **Konfigurationsalternativ**: **Grundläggande** eller **avancerade**.
- **Storage-konton**: Lagringskontot med behållaren du vill.
- **Lagringstyp**: **Azure-Blobar** eller **Azure Files**.
  > [!NOTE]
  > Windows-behållarappar har endast stöd för Azure Files.
- **Lagringsbehållare**: För grundläggande konfiguration, den behållare som du vill.
- **Resursnamnet**: Namn på filresursen för avancerad konfiguration.
- **Åtkomstnyckel**: För avancerad konfiguration, åtkomstnyckeln.
- **Monteringssökväg**: Den absoluta sökvägen i din behållare att montera anpassad lagring.

Mer information finns i [leverera innehåll från Azure Storage i App Service i Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Konfigurera inställningar för språk-stack

Linux-appar, finns här:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Konfigurera anpassade behållare

Se [konfigurera en anpassad Linux-behållare för Azure App Service](containers/configure-custom-container.md)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera ett anpassat domännamn i Azure App Service]
- [Konfigurera mellanlagringsmiljöer i Azure App Service]
- [Aktivera HTTPS för en app i Azure App Service]
- [Aktivera diagnostikloggar](troubleshoot-diagnostic-logs.md)
- [Skala en app i Azure App Service]
- [Övervakning av grunderna i Azure App Service]
- [Ändra applicationHost.config inställningar med applicationHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurera ett anpassat domännamn i Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Konfigurera mellanlagringsmiljöer i Azure App Service]: ./deploy-staging-slots.md
[Aktivera HTTPS för en app i Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Övervakning av grunderna i Azure App Service]: ./web-sites-monitor.md
[Pipeline-läge]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Skala en app i Azure App Service]: ./web-sites-scale.md
