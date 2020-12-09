---
title: Fel sökning – Azure Monitor Application Insights Java
description: Felsöka Azure Monitor Application Insights Java
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855658"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>Felsöka Azure Monitor Application Insights Java

I den här artikeln har vi täckt några av de vanliga problem som en användare kanske möter vid instrumentering av Java-program med hjälp av Java-agenten tillsammans med stegen för att lösa problemen.

## <a name="self-diagnostic-log-file"></a>Logg filen för självdiagnostik

Som standard kommer Application Insights Java 3,0 att skapa en loggfil med namnet `applicationinsights.log` i samma katalog som filen finns i `applicationinsights-agent-3.0.0.jar` .

Den här logg filen är den första platsen där du kan söka efter tips till eventuella problem som kan uppstå.

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>Uppgradera från Application Insights Java 2. x SDK

Se [Uppgradera från 2. x SDK](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-30-preview"></a>Uppgradera från 3,0 för hands version

Om du uppgraderar från 3,0 för hands version kan du granska alla [konfigurations alternativ](./java-standalone-config.md) noggrant, eftersom JSON-strukturen har ändrats fullständigt i 3,0 ga-versionen.

Dessa ändringar omfattar:

1.  Konfigurations filens namn har ändrats från `ApplicationInsights.json` till `applicationinsights.json` .
2.  `instrumentationSettings`Noden finns inte längre. Allt innehåll i `instrumentationSettings` flyttas till rot nivån. 
3.  Konfigurations noder `sampling` som `jmxMetrics` , `instrumentation` och `heartbeat` flyttas ut från `preview` till rot nivån.

## <a name="ssl-certificate-issues"></a>Problem med SSL-certifikat

Om du använder standard-Java-standardarkivet har den redan alla certifikat utfärdarens rot certifikat och du behöver inte importera några ytterligare SSL-certifikat.

Om du använder en anpassad Java-nyckel lagring kan du behöva importera Application Insights slut punkt SSL-certifikat till den.

### <a name="some-key-terminology"></a>Vissa viktiga termer:
Nyckel *lagring* är en lagrings plats för certifikat, offentliga och privata nycklar. Vanligt vis har JDK-distributioner en körbar fil för att hantera dem – `keytool` .

Följande exempel är ett enkelt kommando för att importera ett SSL-certifikat till nyckel arkivet:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>Steg för att hämta och lägga till SSL-certifikatet:

1.  Öppna din favorit webbläsare och gå till `IngestionEndpoint` URL: en som finns i anslutnings strängen som används för att instrumentera ditt program som visas nedan

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Application Insights anslutnings sträng":::

2.  Klicka på ikonen Visa webbplats information (lås) i webbläsaren och klicka på alternativet certifikat som visas nedan

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Registrering av SSL-certifikat":::

3.  Gå till fliken information och klicka på Kopiera till fil.
4.  Klicka på knappen Nästa och välj "Base-64-kodad X. 509 (. CER) "format och välj Nästa.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="ExportWizard för SSL-certifikat":::

5.  Ange den fil som du vill spara SSL-certifikatet till. Klicka slutligen på Nästa och Slutför. Meddelandet "exporten lyckades" visas.
6.  När du har fått certifikatet att importera certifikatet till en Java-nyckel lagring. Använd [kommandot](#some-key-terminology) ovan för att importera certifikat.

> [!WARNING]
> Du måste upprepa de här stegen för att få det nya certifikatet innan det aktuella certifikatet upphör att gälla. Du kan hitta information om förfallo datum på fliken "information" i popup-fönstret för certifikat enligt nedan

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Information om SSL-certifikat":::
