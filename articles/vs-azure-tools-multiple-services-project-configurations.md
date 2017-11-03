---
title: "Konfigurera din Azure-projekt med flera tjänstkonfiguration | Microsoft Docs"
description: "Lär dig hur du konfigurerar en Azure cloud service-projekt genom att ändra ServiceDefinition.csdef och ServiceConfiguration.cscfg-filer."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: a4fb79ed-384f-4183-9f74-5cac257206b9
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: deb69101e855bcad56b9212736c52ace72631f0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-your-azure-project-using-multiple-service-configurations"></a>Konfigurera din Azure-projekt med flera tjänstkonfiguration
Ett Azure cloud service-projekt innehåller två konfigurationsfiler: ServiceDefinition.csdef och ServiceConfiguration.cscfg. De här filerna paketeras med ditt Azure-molnet tjänstprogram och distribueras till Azure.

* Den **ServiceDefinition.csdef** filen innehåller metadata som krävs av Azure-miljön för ditt tjänstprogram för molntjänster, inklusive vilka roller som den innehåller. Den här filen innehåller också konfigurationsinställningar som gäller för alla instanser. Dessa konfigurationsinställningar kan läsas under körning med Azure-tjänsten värd för Runtime-API. Den här filen kan inte uppdateras, medan tjänsten körs i Azure.
* Den **ServiceConfiguration.cscfg** filen anger värden för konfigurationsinställningar som definierats i tjänstdefinitionsfilen och anger antalet instanser som körs för varje roll. Den här filen kan uppdateras när Molntjänsten körs i Azure.

Azure-verktyg för Microsoft Visual Studio innehåller egenskapssidor som du kan använda för att ange konfigurationsinställningar som lagras i dessa filer. För att öppna egenskapssidor dubbelklickar du på roll-referens under Azure cloud service-projekt i Solution Explorer eller högerklicka på rollen referens och välj **egenskaper**som visas i följande bild.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Information om de underliggande scheman för tjänsten definitions- och konfigurationsfiler för tjänsten finns på [Schemareferens](https://msdn.microsoft.com/library/azure/dd179398.aspx). Läs mer om tjänstkonfiguration [hur du konfigurerar molntjänster](cloud-services/cloud-services-how-to-configure.md).

## <a name="configuring-role-properties"></a>Konfigurera rollegenskaper för en
Egenskapssidorna för en webbroll och en arbetsroll liknar varandra, även om det finns några skillnader som framgår i följande avsnitt.

Från den **cachelagring** kan du konfigurera Azure caching services.

### <a name="configuration-page"></a>Konfigurationssidan
På den **Configuration** kan du ange dessa egenskaper:

**Instanser**

Ange den **instans** räkna egenskap till antal instanser som tjänsten ska köras för den här rollen.

Ange den **VM-storlek** egenskapen **Extra liten**, **små**, **medel**, **stor**, eller **Extra stor**.  Mer information finns i [storlekar för molntjänster](cloud-services/cloud-services-sizes-specs.md).

**Startåtgärden** (Web roll endast)

Ange den här egenskapen för att ange att Visual Studio ska starta en webbläsare för HTTP-slutpunkter och/eller HTTPS-slutpunkter när du startar felsökning.

HTTPS-slutpunkt-alternativet är bara tillgängligt om du redan har definierat en HTTPS-slutpunkt för din roll. Du kan definiera en HTTPS-slutpunkt på den **slutpunkter** egenskapssida.

Om du redan har lagt till en HTTPS-slutpunkt alternativet HTTPS-slutpunkten är aktiverad som standard och Visual Studio startas en webbläsare för den här slutpunkten när du startar felsökning, förutom en webbläsare för HTTP-slutpunkten. Detta förutsätter att båda startalternativ är aktiverade.

**Diagnostik**

Som standard aktiveras diagnostik för webbrollen. Azure-molnet projekt- och tjänstkontot är inställd på att använda den lokala lagringsemulatorn. När du är redo att distribuera till Azure kan du välja knappen builder (**...** ) att uppdatera lagringskontot för att använda Azure storage i molnet. Du kan överföra diagnostikdata till lagringskontot på begäran eller vid automatiskt schemalagda intervall. Läs mer om Azure diagnostics [aktiverar diagnostik i Azure-molntjänster och virtuella datorer](cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Inställningssidan
På den **inställningar** sida, du kan lägga till konfigurationsinställningarna för tjänsten. Konfigurationsinställningarna är namn / värde-par. Kod som körs i rollen kan läsa värdena i inställningarna under körning med klasser som tillhandahålls av den [Azure hanteras Library](http://go.microsoft.com/fwlink?LinkID=171026). Mer specifikt den [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) metoden returnerar värdet för en namngiven konfigurationsinställning vid körning.

### <a name="configuring-a-connection-string-to-a-storage-account"></a>Konfigurera en anslutningssträng för ett lagringskonto
En anslutningssträng är en konfigurationsinställning som tillhandahåller information om anslutning och autentisering för storage-emulatorn eller ett Azure storage-konto. När din kod måste komma åt Azure storage services-data – det vill säga blob, kö eller tabelldata – från kod som körs i en roll, behöver du ange en anslutningssträng för det lagringskontot.

En anslutningssträng som pekar på ett Azure storage-konto måste använda ett definierat format. Information om hur du skapar anslutningssträngar finns [konfigurera Azure Storage-anslutningssträngar](storage/common/storage-configure-connection-string.md).

När du är redo att testa din tjänst mot Azure storage-tjänster, eller när du är redo att distribuera din tjänst i molnet till Azure, kan du ändra värdet för alla anslutningssträngar för att peka på ditt Azure storage-konto. Välj (**...** ), Välj **ange lagringskontouppgifter**. Ange din kontoinformation som innehåller ditt kontonamn och kontonyckel. I den **Lagringsanslutningssträng för kontot** dialogrutan, du kan också ange om du vill använda HTTPS standardslutpunkterna (standardalternativet), http-slutpunkter eller anpassade slutpunkter. Du kan välja att använda anpassade slutpunkter om du har registrerat ett anpassat domännamn för din tjänst som beskrivs i [konfigurera ett anpassat domännamn för blob-data i ett Azure storage-konto](storage/blobs/storage-custom-domain-name.md).

> [!IMPORTANT]
> Du måste ändra din anslutningssträngar för att peka på ett Azure storage-konto innan du distribuerar din tjänst. Om du inte gör detta kan orsaka din roll inte kan starta eller växlar mellan de initierande upptagen och stoppas.
> 
> 

## <a name="endpoints-page"></a>Slutpunkter
En arbetsroll kan ha valfritt antal HTTP, HTTPS eller TCP-slutpunkter. Slutpunkter kan vara inkommande slutpunkter som är tillgängliga för externa klienter, eller interna slutpunkter som är tillgängliga för andra roller som körs i tjänsten.

* Ändra slutpunktstypen av som indata för att en HTTP-slutpunkt ska vara tillgängliga för externa klienter och webbläsare och ange ett namn och en offentlig portnummer.
* Om du vill få en HTTPS-slutpunkt externa klienter och webbläsare, ändra slutpunktstypen av till **inkommande**, och ange ett namn, en offentlig portnummer och ett namn på certifikatet.
  
    Observera att innan du kan ange ett certifikat, måste du definiera certifikatet på den **certifikat** egenskapssida.
* Ändra slutpunktstypen av till interna om du vill att en slutpunkt för intern åtkomst av andra roller i Molntjänsten och ange ett namn och möjliga privata portar för den här slutpunkten.

## <a name="local-storage-page"></a>Lokal lagring sida
Du kan använda den **lokal lagring** egenskapssidan för att reservera en eller flera lokala lagringsresurser för en roll. En resurs för lokal lagring är ett reserverat katalogen i filsystemet på den virtuella Azure-datorn som kör en instans av en roll.

## <a name="certificates-page"></a>Sidan Certifikat
På den **certifikat** sidan kan du koppla certifikat till din roll. De certifikat som du lägger till kan användas för att konfigurera HTTPS-slutpunkter på den **slutpunkter** egenskapssida.

Den **certifikat** egenskapssidan lägger till information om dina certifikat i tjänstkonfigurationen av. Observera att certifikaten inte paketeras med din tjänst. Du måste överföra ditt certifikat separat till Azure via den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885).

Om du vill koppla ett certifikat till din roll måste du ange ett namn för certifikatet. Du använder det här namnet för att referera till certifikatet när du konfigurerar en HTTPS-slutpunkt på den **slutpunkter** egenskapssida. Ange sedan om certifikatarkivet är **lokal dator** eller **aktuell användare** och namnet på arkivet. Slutligen ange certifikatets tumavtryck. Om certifikatet finns i den aktuella User\Personal (min) store, kan du ange certifikatets tumavtryck genom att välja certifikatet från en lista. Om det finns någon annanstans, ange tumavtrycket värdet manuellt.

När du lägger till ett certifikat från certifikatarkivet läggs alla mellanliggande certifikat automatiskt till konfigurationsinställningarna för dig. Dessa mellanliggande certifikat måste också överföras till Azure för att kunna konfigurera din tjänst för SSL på rätt sätt.

Alla certifikat som associeras med din tjänst gäller för din tjänst endast när den körs i molnet. När din tjänst körs i den lokala utvecklingsmiljön använder en standard certifikat som hanteras av beräkningsemulatorn.

## <a name="configuring-the-azure-cloud-service-project"></a>Konfigurera Azure cloud service-projekt
Om du vill konfigurera inställningar som gäller för en hel Azure cloud service-projekt du först öppna snabbmenyn för projektnoden och välj Egenskaper för att öppna egenskapssidorna. I följande tabell visas de egenskapssidorna.

| Egenskapssida | Beskrivning |
| --- | --- |
| Program |Den här sidan kan visa du information om versionen av Azure-verktyg som använder den här molntjänstprojekt, och du kan uppgradera till den aktuella versionen av verktygen. |
| Skapa händelser |Du kan ange build före och efter build händelser från den här sidan. |
| Utveckling |Du kan ange build konfigurationsanvisningar och de villkor under vilka händelser efter build körs från den här sidan. |
| Webb |Den här sidan kan konfigurera du inställningar som relaterar till webbservern. |

