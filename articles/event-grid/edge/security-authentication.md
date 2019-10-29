---
title: Säkerhet och autentisering – Azure Event Grid IoT Edge | Microsoft Docs
description: Säkerhet och autentisering i Event Grid på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 77b4b265b2e993ccdbc9e07fd2dab5a37ed22a6b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992163"
---
# <a name="security-and-authentication"></a>Säkerhet och autentisering

Säkerhet och autentisering är ett avancerat begrepp och det kräver att du först Event Grid grunderna. Börja [här](concepts.md) om du är nybörjare på Event Grid IoT Edge. Event Grid-modulen bygger på den befintliga säkerhets infrastrukturen på IoT Edge. Mer information och konfiguration finns i [den här dokumentationen](../../iot-edge/security.md) .

I följande avsnitt beskrivs hur de här inställningarna skyddas och autentiseras:-

* TLS-konfiguration
* Inkommande klientautentisering
* Utgående serverautentisering
* Utgående klientautentisering

>[!IMPORTANT]
>Event Grid modul säkerhet och autentisering utnyttjar den befintliga infrastrukturen som finns på IoT Edge. Antagande är att IoT Edge under systemet är säkert.

>[!IMPORTANT]
>Event Grid-konfigurationen är **säker som standard**. I följande underavsnitt beskrivs alla alternativ och möjliga värden som du kan använda för att åsidosätta aspekter av autentisering. Förstå påverkan innan du gör några ändringar. För att ändringarna ska börja gälla måste Event Grid-modulen omdistribueras.

## <a name="tls-configuration-aka-server-authentication"></a>TLS-konfiguration (a. k. a Server-autentisering)

Event Grid modul är värd för både HTTP-och HTTPS-slutpunkter. Varje IoT Edge modul tilldelas ett Server certifikat av IoT Edges säkerhets daemon. Vi använder Server certifikatet för att skydda slut punkten. Vid förfallo datum uppdateras modulen automatiskt med ett nytt certifikat från IoT Edge Security daemon.

Som standard tillåts endast HTTPS-kommunikation. Du kan åsidosätta det här beteendet via **inkommande: serverAuth: tlsPolicy** -konfiguration. Följande tabell innehåller de möjliga värdena för den här egenskapen.

| Möjliga värde (n) | Beskrivning |
| ---------------- | ------------ |
| Strikt | Standard. Aktiverar endast HTTPS
| Enabled | Aktiverar både HTTP och HTTPS
| Disabled | Aktiverar endast HTTP

## <a name="inbound-client-authentication"></a>Inkommande klientautentisering

Klienter är enheter som utför hanterings-och/eller körnings åtgärder. Klienter kan vara andra IoT Edge moduler, icke-IoT-program.

Event Grid-modulen stöder två typer av klientautentisering:-

* Signaturer för delad åtkomst (SAS)
* certifikatbaserad

Som standard är Event Grid-modulen konfigurerad att endast godkänna certifikatbaserad autentisering. Vid start hämtar Event Grid-modulen "TrustBundle" från IoT Edge Security daemon och använder den för att verifiera eventuella klient certifikat. Klient certifikat som inte matchar den här kedjan kommer att avvisas med `UnAuthorized`.

### <a name="certificate-based-client-authentication"></a>Certifikatbaserad klientautentisering

Certifikatbaserad autentisering är aktiverat som standard. Du kan välja att inaktivera certifikatbaserad autentisering via egenskapen **inkommande: clientAuth: clientCert: Enabled**. Följande tabell innehåller möjliga värden.

| Möjliga värde (n) | Beskrivning |
| ----------------  | ------------ |
| sant | Standard. Kräver att alla begär anden i Event Grid-modulen visar ett klient certifikat. Dessutom måste du konfigurera **inkommande: clientAuth: clientCert: Source**.
| false | Tvinga inte en klient att presentera certifikatet.

Följande tabell innehåller möjliga värden för **inkommande: clientAuth: clientCert: Source**

| Möjliga värde (n) | Beskrivning |
| ---------------- | ------------ |
| IoT Edge | Standard. Använder IoT Edgeens Trustbundle för att validera alla klient certifikat.

Om en klient presenterar en självsignerad, kommer Event Grid-modulen som standard att avvisa sådana begär Anden. Du kan välja att tillåta självsignerade klient certifikat via egenskapen **inkommande: clientAuth: clientCert: allowUnknownCA** . Följande tabell innehåller möjliga värden.

| Möjliga värde (n) | Beskrivning |
| ----------------  | ------------|
| sant | Standard. Tillåter att självsignerade certifikat visas korrekt.
| false | Får inga förfrågningar om självsignerade certifikat visas.

>[!IMPORTANT]
>I produktions scenarier kanske du vill ange **inkommande: clientAuth: clientCert: allowUnknownCA** till **false**.

### <a name="sas-key-based-client-authentication"></a>SAS-nyckelbaserad klientautentisering

Förutom certifikatbaserad autentisering kan Event Grid-modulen också utföra SAS-baserad autentisering. SAS-nyckeln är som en hemlighet som kon figurer ATS i Event Grid-modulen som den ska använda för att validera alla inkommande samtal. Klienter måste ange hemligheten i HTTP-huvudet "AEG-SAS-Key". Begäran kommer att avvisas med `UnAuthorized` om den inte matchar.

Konfigurationen för att kontrol lera SAS-nyckelbaserad autentisering är **inkommande: clientAuth: sasKeys: Enabled**.

| Möjliga värde (n) | Beskrivning  |
| ----------------  | ------------ |
| sant | Tillåter SAS-nyckelbaserad autentisering. Kräver **inkommande: clientAuth: sasKeys: KEY1** eller **inkommande: clientAuth: sasKeys: key2**
| false | Standard. SAS-nyckelbaserad autentisering har inaktiverats.

 **inkommande: clientAuth: sasKeys: KEY1** och **inkommande: clientAuth: sasKeys: key2** är nycklar som du konfigurerar Event Grid-modulen för att kontrol lera mot inkommande begär Anden. Minst en av nycklarna måste konfigureras. Klienten som gör begäran måste presentera nyckeln som en del av begär ande rubriken "**AEG-SAS-Key**". Om båda nycklarna har kon figurer ATS kan klienten antingen visa en av nycklarna.

> [!NOTE]
>Du kan konfigurera båda metoderna för autentisering. I sådana fall är SAS-nyckeln först markerad och endast om det Miss lyckas utförs den certifikatbaserad autentiseringen. För att en begäran ska lyckas måste bara en av autentiseringsmetoderna lyckas.

## <a name="outbound-client-authentication"></a>Utgående klientautentisering

Klienten i utgående kontext refererar till Event Grid modul. Åtgärden som utförs levererar händelser till prenumeranter. Att underbeskriva moduler betraktas som-servern.

Varje IoT Edge modul tilldelas ett identitets certifikat av IoT Edges säkerhets daemon. Vi använder identitets certifikatet för utgående samtal. Vid förfallo datum uppdateras modulen automatiskt med ett nytt certifikat från IoT Edge Security daemon.

Konfigurationen för att kontrol lera utgående klientautentisering är **utgående: clientAuth: clientCert: Enabled**.

| Möjliga värde (n) | Beskrivning |
| ----------------  | ------------ |
| sant | Standard. Kräver alla utgående begär Anden från Event Grid-modulen för att presentera ett certifikat. Måste konfigurera **utgående: clientAuth: clientCert: Source**.
| false | Kräv inte Event Grid modul för att presentera sitt certifikat.

Konfigurationen som styr certifikatets källa är **utgående: clientAuth: clientCert: Source**.

| Möjliga värde (n) | Beskrivning |
| ---------------- | ------------ |
| IoT Edge | Standard. Använder modulens identitets certifikat som kon figurer ATS av IoT Edge Security daemon.

### <a name="outbound-server-authentication"></a>Utgående serverautentisering

En av mål typerna för en Event Grid-prenumerant är "webhook". Som standard godkänns bara HTTPS-slutpunkter för sådana prenumeranter.

Konfigurationen för att kontrol lera att webhookens mål princip har **utgående: webhook: httpsOnly**.

| Möjliga värde (n) | Beskrivning |
| ----------------  | ------------ |
| sant | Standard. Tillåter endast prenumeranter med HTTPS-slutpunkt.
| false | Tillåter prenumeranter med antingen HTTP-eller HTTPS-slutpunkt.

Som standard kommer Event Grid-modulen validera prenumerantens Server certifikat. Du kan hoppa över verifieringen genom att åsidosätta **utgående: webhook: skipServerCertValidation**. Möjliga värden är:-

| Möjliga värde (n) | Beskrivning |
| ----------------  | ------------ |
| sant | Verifiera inte prenumerantens Server certifikat.
| false | Standard. Verifiera prenumerantens Server certifikat.

Om prenumerantens certifikat är självsignerat avvisar som standard Event Grid-modulen sådana prenumeranter. Om du vill tillåta självsignerade certifikat kan du åsidosätta **utgående: webhook: allowUnknownCA**. Följande tabell innehåller möjliga värden.

| Möjliga värde (n) | Beskrivning |
| ----------------  | ------------ |
| sant | Standard. Tillåter att självsignerade certifikat visas korrekt.
| false | Får inga förfrågningar om självsignerade certifikat visas.

>[!IMPORTANT]
>I produktions scenarier ska du ange **utgående: webhook: allowUnknownCA** till **false**.

> [!NOTE]
>IoT Edge-miljön genererar självsignerade certifikat. Rekommendation är att generera certifikat som utfärdats av auktoriserade certifikat utfärdare för produktions arbets belastningar och ange egenskapen **allowUnknownCA** på både inkommande och utgående till **false**.

## <a name="summary"></a>Sammanfattning

En Event Grid modul är **säker som standard**. Vi rekommenderar att du behåller dessa standardinställningar för dina produktions distributioner.

Följande är de GUID-principer som ska användas när du konfigurerar:-

* Tillåt endast HTTPS-begäranden till modulen.
* Tillåt endast certifikatbaserad klientautentisering. Tillåt endast de certifikat som utfärdas av välkända certifikat utfärdare. Tillåt inte självsignerade certifikat.
* Tillåt inte SASKey-baserad klientautentisering.
* Presentera alltid Event Grid modulens identitets certifikat vid utgående samtal.
* Tillåt endast HTTPS-prenumeranter för webhook-destinations typer.
* Verifiera alltid prenumerantens Server certifikat för mål typerna för webhook. Tillåt endast certifikat som utfärdats av välkända certifikat utfärdare. Tillåt inte självsignerade certifikat.

Som standard distribueras Event Grid-modulen med följande konfiguration:-

 ```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
