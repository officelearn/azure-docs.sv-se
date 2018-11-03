---
title: Konfigurera din Azure-projekt med hjälp av flera tjänstkonfigurationer | Microsoft Docs
description: Lär dig hur du konfigurerar ett Azure cloud service-projekt genom att ändra filerna ServiceDefinition.csdef och ServiceConfiguration.Local.cscfg ServiceConfiguration.Cloud.cscfg.
services: visual-studio-online
author: ghogen
manager: douge
assetId: a4fb79ed-384f-4183-9f74-5cac257206b9
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 1ec587f4a4519f86efceb7cefa0acb372035a9a9
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969715"
---
# <a name="configuring-your-azure-project-in-visual-studio-to-use-multiple-service-configurations"></a>Konfigurera din Azure-projekt i Visual Studio för att använda flera tjänstkonfiguration

Ett Azure cloud service-projekt i Visual Studio innehåller tre konfigurationsfiler: `ServiceDefinition.csdef`, `ServiceConfiguration.Local.cscfg`, och `ServiceConfiguration.Cloud.cscfg`:

- `ServiceDefinition.csdef` distribueras till Azure för att beskriva kraven för Molntjänsten och dess roller och för att ange inställningar som tillämpas på alla instanser. Inställningarna kan läsas in vid körning med hjälp av Azure-tjänsten som är värd för Runtime API. Den här filen kan uppdateras på Azure endast när Molntjänsten har stoppats.
- `ServiceConfiguration.Local.cscfg` och `ServiceConfiguration.Cloud.cscfg` ange värden för inställningarna i definitionen för filen och ange antalet instanser körs för varje roll. Filen ”Local” innehåller värden som används i lokal felsökning. filen ”Cloud” distribueras till Azure som `ServiceConfiguration.cscfg` och tillhandahåller inställningar för server-miljö. Den här filen kan uppdateras medan cloud Services körs i Azure.

Konfigurationsinställningar hanteras och ändras i Visual Studio med egenskapssidor för rollen tillämpliga (Högerklicka på rollen och välj **egenskaper**, eller dubbelklicka på rollen). Ändringar kan vara begränsad till konfigurationen som har valts i den **tjänstkonfiguration** utskriftsjobb. Egenskaperna för webb-och arbetsroller liknar varandra, utom där det beskrivs i följande avsnitt.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Information om alla underliggande scheman för tjänsten definitions- och konfigurationsfiler för tjänsten finns i den [.csdef XML-Schema](cloud-services/schema-csdef-file.md) och [.cscfg XML-Schema](cloud-services/schema-cscfg-file.md) artiklar. Mer information om konfiguration finns i [hur du konfigurerar Cloud Services](cloud-services/cloud-services-how-to-configure-portal.md).


## <a name="configuration-page"></a>Konfigurationssidan

### <a name="service-configuration"></a>Tjänstkonfiguration

Väljer vilka `ServiceConfiguration.*.cscfg` filen påverkas av ändringar. Som standard finns lokalt och molnet du kan använda den **hantera...**  kommando för att kopiera, byta namn på och ta bort konfigurationsfiler. Dessa filer läggs till i ditt molntjänstprojekt och visas i **Solution Explorer**. Byta namn på eller ta bort konfigurationer kan dock göras för endast från den här kontrollen.

### <a name="instances"></a>Instanser

Ange den **instans** räknas egenskapen till antal instanser som tjänsten ska köras för den här rollen.

Ange den **VM-storlek** egenskap **instanserna Extra liten**, **små**, **medel**, **stor**, eller **Extra stor**.  Mer information finns i [storlekar för Cloud Services](cloud-services/cloud-services-sizes-specs.md).

### <a name="startup-action-web-role-only"></a>Startåtgärden (endast webbroll)

Ange den här egenskapen för att ange att Visual Studio ska starta en webbläsare för HTTP-slutpunkter och/eller HTTPS-slutpunkter när du startar felsökning.

Den **HTTPS-slutpunkt** alternativet är bara tillgängligt om du redan har definierat en HTTPS-slutpunkt för din roll. Du kan definiera en HTTPS-slutpunkt på den **slutpunkter** egenskapssidan.

Om du har redan lagt till en HTTPS-slutpunkt, HTTPS-slutpunkt-alternativet är aktiverat som standard och Visual Studio öppnas en webbläsare för den här slutpunkten när du startar felsökning, förutom en webbläsare för HTTP-slutpunkten, förutsatt att båda alternativen är aktiverade.

### <a name="diagnostics"></a>Diagnostik

Som standard aktiveras diagnostik för webbrollen. Azure-molnet projekt och lagring tjänstkontot är inställd på att använda den lokala lagringsemulatorn. När du är redo att distribuera till Azure kan du välja knappen builder (**...** ) att använda Azure storage i stället. Du kan överföra diagnostics-data till lagringskontot på begäran eller vid automatiskt schemalagda intervall. Läs mer om Azure diagnostics [hur du aktiverar diagnostik i Azure Cloud Services och virtuella datorer](cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Sidan Inställningar

På den **inställningar** kan du kan lägga till inställningar till en konfiguration som namn / värde-par. Kod som körs i rollen kan läsa värdena för dina inställningar under körning med klasser som tillhandahålls av den [Azure hanterade biblioteket](http://go.microsoft.com/fwlink?LinkID=171026), särskilt den [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) metod.

### <a name="configuring-a-connection-string-for-a-storage-account"></a>Konfigurera en anslutningssträng för ett lagringskonto

En anslutningssträng är en inställning som tillhandahåller anslutning och autentiseringsinformation för storage-emulatorn eller för ett Azure storage-konto. När koden i en roll har åtkomst till Azure storage (blobs, köer och tabeller), måste en anslutningssträng.

> [!Note]
> En anslutningssträng för Azure storage-konto måste använda ett definierat format (se [konfigurera anslutningssträngar för Azure Storage](storage/common/storage-configure-connection-string.md)).

Du kan ange anslutningssträngen för att använda lokal lagring vid behov, ställs in på ett Azure storage-konto när du distribuerar programmet till Molntjänsten. Det gick inte att ange anslutningssträngen korrekt leda din roll inte kan starta eller växlar mellan de initierar, upptagen och stoppar.

Om du vill skapa en anslutningssträng, Välj **Lägg till inställning** och ange **typ** på ”anslutningssträng”.

Ny eller befintlig anslutningssträngar, Välj **...** * till höger om den **värdet** fältet för att öppna den **skapa Lagringsanslutningssträng** dialogrutan:

1. Under **ansluta med hjälp av**, Välj den **prenumerationen** möjlighet att välja ett lagringskonto från en prenumeration. Visual Studio hämtar sedan autentiseringsuppgifterna för ett lagringskonto automatiskt från den `.publishsettings` filen.
1. Att välja **angett autentiseringsuppgifterna manuellt** kan du ange kontonamnet och nyckeln direkt med hjälp av information från Azure-portalen. Kopiera kontonyckeln:
    1. Gå till storage-konto på Azure portal och väljer **hantera nycklar**.
    1. Gå till storage-konto på Azure portal, Välj om du vill kopiera kontonyckeln **Inställningar > åtkomstnycklar**, sedan använda kopieringsknappen för att kopiera den primära åtkomstnyckeln till Urklipp.
1. Välj en av anslutningsalternativ. **Ange anpassade slutpunkter** du ombeds ange specifika URL: er för blobbar, tabeller och köer. Anpassade slutpunkter kan du använda [anpassade domäner](storage/blobs/storage-custom-domain-name.md) och styra åtkomsten mer exakt. Se [konfigurerar Azure Storage-anslutningssträngar](./storage/common/storage-configure-connection-string.md).
1. Välj **OK**, sedan **fil > Spara** att uppdatera konfigurationen med den nya anslutningssträngen.

Igen, när du publicerar programmet till Azure, välja tjänstkonfigurationen som innehåller Azure storage-kontot för anslutningssträngen. När programmet har publicerats kan du kontrollera att programmet fungerar som förväntat mot Azure storage-tjänster.

Mer information om hur du uppdaterar tjänstkonfigurationer finns i avsnittet [hantera anslutningssträngar för storage-konton](vs-azure-tools-configure-roles-for-cloud-service.md#manage-connection-strings-for-storage-accounts).

## <a name="endpoints-page"></a>Sidan slutpunkter

En webbroll har vanligtvis en enda HTTP-slutpunkt på port 80. En arbetsroll, å andra sidan kan ha valfritt antal HTTP, HTTPS- eller TCP-slutpunkter. Slutpunkter kan vara inkommande slutpunkter som är tillgängliga för externa klienter, eller interna slutpunkter som är tillgängliga för andra roller som körs i tjänsten.

- Ändra slutpunktstyp för att ange om du vill göra en HTTP-slutpunkt ska vara tillgängliga för externa klienter och webbläsare och ange ett namn och en offentliga portnumret.
- Om du vill tillgängliggöra en HTTPS-slutpunkt för externa klienter och webbläsare, ändra typ av slutpunkt till **inkommande**, och ange ett namn, en offentliga portnumret och ett certifikatnamn för hantering. Du måste också definiera certifikatet för den **certifikat** egenskapssidan innan du kan ange ett hanteringscertifikat. 
- Ändra slutpunkten till internt för att tillgängliggöra en slutpunkt för intern åtkomst av andra roller i Molntjänsten, och ange ett namn och möjliga privata portar för den här slutpunkten.

## <a name="local-storage-page"></a>Sidan för lokal lagring

Du kan använda den **lokal lagring** egenskapssidan att reservera en eller flera lokala lagringsresurser för en roll. En resurs för lokal lagring är en reserverad katalog i filsystemet för virtuella Azure-datorer som kör en instans av en roll.

## <a name="certificates-page"></a>Sidan Certifikat

Den **certifikat** egenskapssidan lägger till information om dina certifikat i tjänstkonfigurationen av. Observera att dina certifikat inte är paketerade med din tjänst; Du måste överföra dina certifikat separat till Azure via den [Azure-portalen](http://portal.azure.com).

Lägga till ett certifikat här lägger till information om ditt certifikat i tjänstkonfigurationen av. Certifikat är inte paketerade med tjänsten; Du måste överföra dina certifikat separat via Azure portal.

Du associerar ett certifikat med din roll genom att ange ett namn för certifikatet. Du använder det här namnet för att referera till certifikatet när du konfigurerar en HTTPS-slutpunkt på den **slutpunkter** sidan. Ange om arkivet är **lokal dator** eller **aktuell användare** och namnet på arkivet. Slutligen ange certifikatets tumavtryck. Om certifikatet finns i den aktuella User\Personal (min) store, kan du ange certifikatets tumavtryck genom att välja certifikatet från en lista. Om det finns någon annanstans, ange tumavtrycket värdet manuellt.

När du lägger till ett certifikat från certifikatarkivet läggs automatiskt alla mellanliggande certifikat till konfigurationsinställningar för dig. Dessutom kan måste dessa mellanliggande certifikat laddas upp till Azure för att korrekt konfigurera tjänsten för SSL.

Alla certifikat som du associerar med din tjänst gäller för din tjänst endast när den körs i molnet. När din tjänst körs i den lokala utvecklingsmiljön, använder ett standardcertifikat som hanteras av compute-emulatorn.
