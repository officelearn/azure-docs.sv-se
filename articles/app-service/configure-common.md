---
title: Konfigurera appar i portalen – Azure App Service
description: Lär dig att konfigurera vanliga inställningar för en App Service-app i Azure Portal.
keywords: Azure App Service, webbapp, app-inställningar, miljövariabler
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c22f88487fd8b34d48d3012c706bb0415760b21e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470935"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Konfigurera en App Service-app i Azure Portal

I det här avsnittet beskrivs hur du konfigurerar vanliga inställningar för webbappar, mobil Server delen eller API-appen med hjälp av [Azure Portal].

## <a name="configure-app-settings"></a>Konfigurera appinställningar

I App Service är app-inställningar variabler som skickas som miljövariabler till program koden. För Linux-appar och anpassade behållare, App Service skickar appinställningar till behållaren med hjälp av `--env`-flaggan för att ställa in miljövariabeln i behållaren.

Gå till appens hanterings sida i [Azure Portal]. I appens vänstra meny klickar du på **konfiguration** > **program inställningar**.

![Program inställningar](./media/configure-common/open-ui.png)

För ASP.NET-och ASP.NET Core-utvecklare kan ställa in appinställningar i App Service som att ställa in dem i `<appSettings>` i *Web. config* eller *appSettings. JSON*, men värdena i App Service åsidosätter dem i *Web. config* eller *appSettings. JSON* . Du kan behålla utvecklings inställningarna (till exempel lokalt MySQL-lösenord) i *Web. config* eller *appSettings. JSON*, men produktions hemligheter (till exempel Azure MySQL-databasens lösen ord) är säkert i App Service. Samma kod använder utvecklings inställningarna när du felsöker lokalt, och den använder dina produktions hemligheter när de distribueras till Azure.

Andra språk stackar, på samma sätt, får också appens inställningar som miljövariabler vid körning. För språkspecifika steg, se:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Anpassade behållare](containers/configure-custom-container.md#configure-environment-variables)

Appinställningar krypteras alltid när de lagras (krypterat i vila).

> [!NOTE]
> Du kan också lösa appinställningar från [Key Vault](/azure/key-vault/) som använder [Key Vault referenser](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Visa dolda värden

Som standard är värden för appinställningar dolda i portalen för säkerhet. Om du vill se ett dolt värde för en app-inställning klickar du på fältet **värde** för den inställningen. Klicka på knappen **Visa värde** om du vill se värdena för alla appdata.

### <a name="add-or-edit"></a>Lägg till eller redigera

Om du vill lägga till en ny app-inställning klickar du på **ny program inställning**. I dialog rutan kan du [ställa in inställningen på den aktuella platsen](deploy-staging-slots.md#which-settings-are-swapped).

Om du vill redigera en inställning klickar du på knappen **Redigera** på höger sida.

Klicka på **Uppdatera**när du är färdig. Glöm inte att klicka på **Spara** tillbaka på sidan **konfiguration** .

> [!NOTE]
> I en standard-Linux-behållare eller en anpassad Linux-behållare måste alla kapslade JSON-nycklar i namnet på appens inställningar, t. ex. `ApplicationInsights:InstrumentationKey` konfigureras i App Service som `ApplicationInsights__InstrumentationKey` för nyckel namnet. Med andra ord bör alla `:` ersättas med `__` (dubbla understreck).
>

### <a name="edit-in-bulk"></a>Redigera i bulk

Klicka på knappen **Avancerad redigering** om du vill lägga till eller redigera appinställningar i grupp. Klicka på **Uppdatera**när du är färdig. Glöm inte att klicka på **Spara** tillbaka på sidan **konfiguration** .

Appinställningar har följande JSON-formatering:

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

Gå till appens hanterings sida i [Azure Portal]. I appens vänstra meny klickar du på **konfiguration** > **program inställningar**.

![Program inställningar](./media/configure-common/open-ui.png)

För ASP.NET och ASP.NET Core utvecklare ska du ställa in anslutnings strängar i App Service till exempel att ställa in dem i `<connectionStrings>` i *Web. config*, men värdena som du anger i App Service åsidosätter dem i *Web. config*. Du kan behålla utvecklings inställningarna (till exempel en databas fil) i *Web. config* och produktions hemligheter (till exempel SQL Database autentiseringsuppgifter) som är säkra i App Service. Samma kod använder utvecklings inställningarna när du felsöker lokalt, och den använder dina produktions hemligheter när de distribueras till Azure.

För andra språk stackar är det bättre att använda [appinställningar](#configure-app-settings) istället, eftersom anslutnings strängar kräver speciell formatering i variabel nycklar för att få åtkomst till värdena. Här är ett undantag, men vissa Azure Database-typer säkerhets kopie ras tillsammans med appen om du konfigurerar anslutnings strängar i appen. Mer information finns i [vad säkerhets kopie ras](manage-backup.md#what-gets-backed-up). Om du inte behöver denna automatiserade säkerhets kopiering ska du använda inställningarna för appen.

Vid körning är anslutnings strängar tillgängliga som miljövariabler, som har prefixet till följande anslutnings typer:

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL Database: `SQLAZURECONNSTR_`
* Anpassad: `CUSTOMCONNSTR_`

Till exempel kan en MySql-anslutningssträng med namnet *connectionstring1* nås som miljö variabel `MYSQLCONNSTR_connectionString1`. För språkspecifika steg, se:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Anpassade behållare](containers/configure-custom-container.md#configure-environment-variables)

Anslutnings strängar krypteras alltid när de lagras (krypterat i vila).

> [!NOTE]
> Anslutnings strängar kan också matchas från [Key Vault](/azure/key-vault/) som använder [Key Vault referenser](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Visa dolda värden

Som standard är värden för anslutnings strängar dolda i portalen för säkerhet. Om du vill se ett dolt värde för en anslutnings sträng klickar du bara på **värde** fältet för den strängen. Klicka på knappen **Visa värde** om du vill se värdena för alla anslutnings strängar.

### <a name="add-or-edit"></a>Lägg till eller redigera

Klicka på **ny anslutnings sträng**för att lägga till en ny anslutnings sträng. I dialog rutan kan du [fästa anslutnings strängen till den aktuella platsen](deploy-staging-slots.md#which-settings-are-swapped).

Om du vill redigera en inställning klickar du på knappen **Redigera** på höger sida.

Klicka på **Uppdatera**när du är färdig. Glöm inte att klicka på **Spara** tillbaka på sidan **konfiguration** .

### <a name="edit-in-bulk"></a>Redigera i bulk

Om du vill lägga till eller redigera anslutnings strängar i bulk klickar du på knappen **Avancerad redigering** . Klicka på **Uppdatera**när du är färdig. Glöm inte att klicka på **Spara** tillbaka på sidan **konfiguration** .

Anslutnings strängar har följande JSON-formatering:

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

Gå till appens hanterings sida i [Azure Portal]. I appens vänstra meny klickar du på **konfiguration** > **program inställningar**.

![Allmänna inställningar](./media/configure-common/open-general.png)

Här kan du konfigurera några vanliga inställningar för appen. Vissa inställningar kräver att du [skalar upp till högre pris nivåer](manage-scale-up.md).

- **Stack-inställningar**: program varu stacken för att köra appen, inklusive språk-och SDK-versioner. För Linux-appar och anpassade behållar appar kan du också ange ett valfritt start kommando eller en valfri fil.
- **Plattforms inställningar**: gör att du kan konfigurera inställningar för värd plattformen, inklusive:
    - **Bitness**: 32-bitars eller 64-bitars.
    - **WebSocket-protokoll**: för [ASP.NET-SignalR] eller [socket.io](https://socket.io/), till exempel.
    - **Always on**: Håll appen inläst även när det inte finns någon trafik. Det krävs för kontinuerliga WebJobs eller för WebJobs som utlöses med ett CRON-uttryck.
    - **Hanterad pipeline-version**: läge för IIS- [pipeline]. Ange den som **klassisk** om du har en äldre app som kräver en äldre version av IIS.
    - **Http-version**: ange till **2,0** för att aktivera stöd för [https/2-](https://wikipedia.org/wiki/HTTP/2) protokoll.
    > [!NOTE]
    > De flesta moderna webbläsare stöder HTTP/2-protokoll över TLS, medan icke-krypterad trafik fortsätter att använda HTTP/1.1. För att säkerställa att klient webbläsare ansluter till din app med HTTP/2 [skyddar du ditt anpassade DNS-namn med en SSL-bindning i Azure App Service](configure-ssl-bindings.md).
    - **Arr-tillhörighet**: i en distribution med flera instanser kontrollerar du att klienten dirigeras till samma instans under sessionens livs längd. Du kan ställa in det här alternativet på **av** för tillstånds lösa program.
- **Fel sökning**: Aktivera fjärrfelsökning för [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug)-, [ASP.net Core](/visualstudio/debugger/remote-debugging-azure)-eller [Node. js](containers/configure-language-nodejs.md#debug-remotely) -appar. Det här alternativet stängs av automatiskt efter 48 timmar.
- **Inkommande klient certifikat**: Kräv klient certifikat i [ömsesidig autentisering](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Konfigurera standard dokument

Den här inställningen gäller endast för Windows-appar.

Gå till appens hanterings sida i [Azure Portal]. I appens vänstra meny klickar du på **konfiguration** > **standard dokument**.

![Allmänna inställningar](./media/configure-common/open-documents.png)

Standard dokumentet är webb sidan som visas i rot-URL: en för en webbplats. Den första matchande filen i listan används. Om du vill lägga till ett nytt standard dokument klickar du på **nytt dokument**. Glöm inte att klicka på **Spara**.

Om appen använder moduler som dirigerar baserat på URL i stället för att betjäna statiskt innehåll behöver du inte använda standard dokument.

## <a name="configure-path-mappings"></a>Konfigurera Sök vägs mappningar

Gå till appens hanterings sida i [Azure Portal]. I appens vänstra meny klickar du på **konfiguration** > **Sök vägs mappningar**.

![Allmänna inställningar](./media/configure-common/open-path.png)

På sidan **Sök vägs mappningar** visas olika saker baserade på typen av operativ system.

### <a name="windows-apps-uncontainerized"></a>Windows-appar (inte container)

För Windows-appar kan du anpassa mappningar för IIS-hanteraren och virtuella program och kataloger.

Med mappningar för hanterare kan du lägga till anpassade skript processorer för att hantera begär Anden för vissa fil namns tillägg. Om du vill lägga till en anpassad hanterare klickar du på **ny hanterare**. Konfigurera hanteraren på följande sätt:

- **Tillägg**. Det fil namns tillägg som du vill hantera, till exempel *\*. php* eller *handler. FCGI*.
- **Skript processor**. Den absoluta sökvägen till skript processorn till dig. Begär anden till filer som matchar fil namns tillägget bearbetas av skript processorn. Använd Sök vägs `D:\home\site\wwwroot` för att referera till appens rot Katalog.
- **Argument**. Valfria kommando rads argument för skript processorn.

Varje app har standard rot Sök vägen (`/`) som är mappad till `D:\home\site\wwwroot`, där din kod distribueras som standard. Om din app-rot finns i en annan mapp, eller om din lagrings plats har fler än ett program, kan du redigera eller lägga till virtuella program och kataloger här. Klicka på **nytt virtuellt program eller katalog**.

Om du vill konfigurera virtuella program och kataloger anger du varje virtuell katalog och dess motsvarande fysiska sökväg i förhållande till webbplatsens rot (`D:\home`). Du kan också markera kryss rutan **program** för att markera en virtuell katalog som ett program.

### <a name="containerized-apps"></a>Appar i behållare

Du kan [lägga till anpassad lagring för din behållares app](containers/how-to-serve-content-from-azure-storage.md). Appar i behållare inkluderar alla Linux-appar och även anpassade Windows-och Linux-behållare som körs på App Service. Klicka på **ny Azure Storage montera** och konfigurera din anpassade lagring på följande sätt:

- **Namn**: visnings namnet.
- **Konfigurations alternativ**: **Basic** eller **Advanced**.
- **Lagrings konton**: lagrings kontot med den behållare som du vill använda.
- **Lagrings typ**: **Azure-blobbar** eller **Azure Files**.
  > [!NOTE]
  > Windows container Apps stöder bara Azure Files.
- **Lagrings behållare**: för grundläggande konfiguration, den behållare som du vill använda.
- **Resurs namn**: för Avancerad konfiguration är fil resurs namnet.
- **Åtkomst nyckel**: för Avancerad konfiguration är åtkomst nyckeln.
- **Monterings Sök väg**: den absoluta sökvägen i din behållare för att montera den anpassade lagringen.

Mer information finns i [Hantera innehåll från Azure Storage i App Service på Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Konfigurera språk Stacks inställningar

För Linux-appar, se:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
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
- [Grundläggande övervakning i Azure App Service]
- [Ändra inställningarna för applicationHost. config med applicationHost. XDT](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET-SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurera ett anpassat domännamn i Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Konfigurera mellanlagringsmiljöer i Azure App Service]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Grundläggande övervakning i Azure App Service]: ./web-sites-monitor.md
[pipeline]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Skala en app i Azure App Service]: ./manage-scale-up.md
