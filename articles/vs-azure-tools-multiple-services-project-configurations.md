---
title: Konfigurera din Azure-projekt med flera tjänstkonfiguration | Microsoft Docs
description: Lär dig hur du konfigurerar en Azure cloud service-projekt genom att ändra ServiceDefinition.csdef, ServiceConfiguration.Local.cscfg och ServiceConfiguration.Cloud.cscfg-filer.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: a4fb79ed-384f-4183-9f74-5cac257206b9
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 919568922ada2d842233ade029a54d474b4a1a0e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="configuring-your-azure-project-in-visual-studio-to-use-multiple-service-configurations"></a>Konfigurera Azure-projekt i Visual Studio för att använda flera tjänstkonfiguration

Ett Azure cloud service-projekt i Visual Studio innehåller tre konfigurationsfiler: `ServiceDefinition.csdef`, `ServiceConfiguration.Local.cscfg`, och `ServiceConfiguration.Cloud.cscfg`:

- `ServiceDefinition.csdef` distribueras till Azure beskrivs kraven för Molntjänsten och dess roller och ange inställningar som gäller för alla instanser. Inställningarna kan läsas under körning med Azure-tjänsten värd för Runtime-API. Den här filen kan uppdateras på Azure endast när Molntjänsten har stoppats.
- `ServiceConfiguration.Local.cscfg` och `ServiceConfiguration.Cloud.cscfg` ange värden för inställningar i definitionen filen och ange antalet instanser körs för varje roll. ”Lokalt” filen innehåller värden som används i lokala felsökning. filen ”moln” distribueras till Azure som `ServiceConfiguration.cscfg` och innehåller inställningar för servermiljö. Den här filen kan uppdateras när Molntjänsten körs i Azure.

Konfigurationsinställningar för hanteras och ändras i Visual Studio med egenskapssidor för rollen tillämpliga (Högerklicka på rollen och välj **egenskaper**, eller dubbelklicka på rollen). Ändringar kan vara begränsad till konfigurationen som har valts i den **tjänstkonfiguration** listrutan. Egenskaperna för webb-och arbetsroller liknar varandra, utom där beskrivs i följande avsnitt.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Information om de underliggande scheman för tjänsten definitions- och konfigurationsfiler för tjänsten finns på [.csdef XML-Schema](cloud-services/schema-csdef-file.md) och [.cscfg XML-Schema](cloud-services/schema-cscfg-file.md) artiklar. Läs mer om tjänstkonfiguration [hur du konfigurerar molntjänster](cloud-services/cloud-services-how-to-configure-portal.md).


## <a name="configuration-page"></a>Konfigurationssidan

### <a name="service-configuration"></a>Tjänstkonfiguration

Väljer vilka `ServiceConfiguration.*.cscfg` filen påverkas av ändringar. Som standard är lokala och molnet varianter och du kan använda den **hantera...**  kommando för att kopiera, byta namn på och ta bort konfigurationsfiler. Dessa filer läggs till ditt molntjänstprojekt och visas i **Solution Explorer**. Byta namn på eller ta bort konfigurationer kan emellertid göras för endast från den här kontrollen.

### <a name="instances"></a>Instanser

Ange den **instans** räkna egenskap till antal instanser som tjänsten ska köras för den här rollen.

Ange den **VM-storlek** egenskapen **Extra liten**, **små**, **medel**, **stor**, eller **Extra stor**.  Mer information finns i [storlekar för molntjänster](cloud-services/cloud-services-sizes-specs.md).

### <a name="startup-action-web-role-only"></a>Startåtgärden (endast webbroll)

Ange den här egenskapen för att ange att Visual Studio ska starta en webbläsare för HTTP-slutpunkter och/eller HTTPS-slutpunkter när du startar felsökning.

Den **HTTPS-slutpunkt** alternativet är bara tillgängligt om du redan har definierat en HTTPS-slutpunkt för din roll. Du kan definiera en HTTPS-slutpunkt på den **slutpunkter** egenskapssida.

Om du redan har lagt till en HTTPS-slutpunkt alternativet HTTPS-slutpunkten är aktiverad som standard och Visual Studio öppnar en webbläsare för den här slutpunkten när du startar felsökning, förutom en webbläsare för HTTP-slutpunkten, förutsatt att båda startalternativ är aktiverade.

### <a name="diagnostics"></a>Diagnostik

Som standard aktiveras diagnostik för webbrollen. Azure-molnet projekt- och tjänstkontot är inställd på att använda den lokala lagringsemulatorn. När du är redo att distribuera till Azure kan du välja knappen builder (**...** ) att använda Azure storage i stället. Du kan överföra diagnostikdata till lagringskontot på begäran eller vid automatiskt schemalagda intervall. Läs mer om Azure diagnostics [aktiverar diagnostik i Azure-molntjänster och virtuella datorer](cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Sidan Inställningar

På den **inställningar** kan du lägga till inställningarna för en konfiguration som namn / värde-par. Kod som körs i rollen kan läsa värdena i inställningarna under körning med klasser som tillhandahålls av den [Azure hanteras Library](http://go.microsoft.com/fwlink?LinkID=171026), särskilt de [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) metod.

### <a name="configuring-a-connection-string-for-a-storage-account"></a>Konfigurera en anslutningssträng för ett lagringskonto

En anslutningssträng är en inställning som tillhandahåller information om anslutning och autentisering för storage-emulatorn eller ett Azure storage-konto. När koden i rollen har åtkomst till Azure storage (BLOB, köer och tabeller), måste en anslutningssträng.

> [!Note]
> En anslutningssträng för Azure storage-konto måste använda ett definierat format (se [konfigurera Azure Storage-anslutningssträngar](storage/common/storage-configure-connection-string.md)).

Du kan ange anslutningssträngen till använder lokal lagring efter behov, ange att ett Azure storage-konto när du distribuerar programmet till Molntjänsten. Ange anslutningssträngen korrekt kan medföra din roll inte kan starta eller växlar mellan de initierande upptagen och stoppas.

Om du vill skapa en anslutningssträng **Lägg till inställning** och ange **typen** ”anslutningssträngen”.

Ny eller befintlig anslutningssträngar, Välj **...** * till höger om den **värdet** fältet för att öppna den **skapa Lagringsanslutningssträng** dialogrutan:

1. Under **ansluta med**, Välj den **prenumerationen** möjlighet att välja ett lagringskonto från en prenumeration. Visual Studio hämtar sedan autentiseringsuppgifterna för ett lagringskonto automatiskt från den `.publishsettings` filen.
1. Att välja **autentiseringsuppgifterna anges manuellt** kan du ange kontonamnet och nyckeln direkt med information från Azure-portalen. Kopiera nyckeln för kontot: en. Navigera till lagringskontot på Azure portal och välj **hantera nycklar**.
    2. Om du vill kopiera nyckeln konto navigerar du till lagringskontot på Azure portal, väljer **Inställningar > åtkomstnycklar**, Använd kopieringsknappen för att kopiera den primära åtkomstnyckeln till Urklipp.
1. Välj något av anslutningsalternativen för. **Ange anpassade slutpunkter** du ombeds ange specifika URL: er för blobbar, tabeller och köer. Anpassade slutpunkter kan du använda [anpassade domäner](storage/blobs/storage-custom-domain-name.md) och kontrollera åtkomsten mer exakt. Se [konfigurera Azure Storage-anslutningssträngar](./storage/common/storage-configure-connection-string.md).
1. Välj **OK**, sedan **fil > Spara** att uppdatera konfigurationen med den nya anslutningssträngen.

När du publicerar programmet till Azure, Välj igen, konfigurationen av tjänsten som innehåller Azure storage-konto för anslutningssträngen. När programmet har publicerats kan du kontrollera att programmet fungerar som förväntat mot Azure storage-tjänster.

Mer information om hur du uppdaterar konfigurationer finns i avsnittet [anslutningssträngar för lagringskonton hantera](vs-azure-tools-configure-roles-for-cloud-service.md#manage-connection-strings-for-storage-accounts).

## <a name="endpoints-page"></a>Slutpunkter

En webbroll har en enda HTTP-slutpunkt på port 80. En arbetsroll å andra sidan kan ha valfritt antal HTTP, HTTPS eller TCP-slutpunkter. Slutpunkter kan vara inkommande slutpunkter som är tillgängliga för externa klienter, eller interna slutpunkter som är tillgängliga för andra roller som körs i tjänsten.

- Ändra slutpunktstypen av som indata för att en HTTP-slutpunkt ska vara tillgängliga för externa klienter och webbläsare och ange ett namn och en offentlig portnummer.
- Om du vill få en HTTPS-slutpunkt externa klienter och webbläsare, ändra slutpunktstypen av till **inkommande**, och ange ett namn, en offentlig portnummer och ett namn på certifikatet. Du måste också definiera certifikatet för den **certifikat** egenskapssidan innan du kan ange ett certifikat. 
- Ändra slutpunktstypen av till interna om du vill att en slutpunkt för intern åtkomst av andra roller i Molntjänsten och ange ett namn och möjliga privata portar för den här slutpunkten.

## <a name="local-storage-page"></a>Lokal lagring sida

Du kan använda den **lokal lagring** egenskapssidan för att reservera en eller flera lokala lagringsresurser för en roll. En resurs för lokal lagring är ett reserverat katalogen i filsystemet på den virtuella Azure-datorn som kör en instans av en roll.

## <a name="certificates-page"></a>Sidan Certifikat

Den **certifikat** egenskapssidan lägger till information om dina certifikat i tjänstkonfigurationen av. Observera att certifikaten inte paketeras med din tjänst. Du måste överföra ditt certifikat separat till Azure via den [Azure-portalen](http://portal.azure.com).

Lägga till ett certifikat här lägger du till information om dina certifikat tjänstkonfigurationen av. Certifikat paketeras inte med tjänsten; Du måste överföra ditt certifikat separat via Azure-portalen.

Om du vill koppla ett certifikat till din roll måste du ange ett namn för certifikatet. Du använder det här namnet för att referera till certifikatet när du konfigurerar en HTTPS-slutpunkt på den **slutpunkter** sidan. Ange sedan om certifikatarkivet är **lokal dator** eller **aktuell användare** och namnet på arkivet. Slutligen ange certifikatets tumavtryck. Om certifikatet finns i den aktuella User\Personal (min) store, kan du ange certifikatets tumavtryck genom att välja certifikatet från en lista. Om det finns någon annanstans, ange tumavtrycket värdet manuellt.

När du lägger till ett certifikat från certifikatarkivet läggs alla mellanliggande certifikat automatiskt till konfigurationsinställningarna för dig. Dessutom kan måste dessa mellanliggande certifikat laddas upp till Azure för att konfigurera din tjänst för SSL på rätt sätt.

Alla certifikat som associeras med din tjänst gäller för din tjänst endast när den körs i molnet. När din tjänst körs i den lokala utvecklingsmiljön använder en standard certifikat som hanteras av beräkningsemulatorn.
