---
title: Säkerhet och autentisering – Azure Event Grid IoT Edge | Microsoft-dokument
description: Säkerhet och autentisering i händelserutnät på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5dfa17fd702b76e2cfaa7a91066dbc6749c1069e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844521"
---
# <a name="security-and-authentication"></a>Säkerhet och autentisering

Säkerhet och autentisering är ett avancerat koncept och det kräver förtrogenhet med grunderna i händelseutnätet först. Börja [här](concepts.md) om du inte har något nytt för Event Grid på IoT Edge. Event Grid-modulen bygger på den befintliga säkerhetsinfrastrukturen på IoT Edge. Mer [information](../../iot-edge/security.md) och installation finns i den här dokumentationen.

I följande avsnitt beskrivs i detalj hur dessa inställningar skyddas och autentiseras:

* TLS-konfiguration
* Autentisering av inkommande klient
* Autentisering av utgående servrar
* Autentisering av utgående klient

>[!IMPORTANT]
>Säkerhet och autentisering av event grid-modul är den befintliga infrastrukturen som är tillgänglig på IoT Edge. Antagandet är att IoT Edge-delsystemet är säkert.

>[!IMPORTANT]
>Konfigurationen av Event Grid är **säker som standard**. I följande underavsnitt förklaras alla alternativ och möjliga värden som du kan använda för att åsidosätta aspekter av autentisering. Förstå effekten innan du gör några ändringar. För att ändringarna ska börja gälla måste modulen Event Grid distribueras om.

## <a name="tls-configuration-aka-server-authentication"></a>TLS-konfiguration (även känd som serverautentisering)

Event Grid-modulen är värd för både HTTP- och HTTPS-slutpunkter. Varje IoT Edge-modul tilldelas ett servercertifikat av IoT Edge säkerhetsdemon. Vi använder servercertifikatet för att skydda slutpunkten. Vid förfallodatum uppdateras modulen automatiskt med ett nytt certifikat från säkerhetsdemonen IoT Edge.

Som standard är endast HTTPS-kommunikation tillåten. Du kan åsidosätta det här beteendet via **inbound__serverAuth__tlsPolicy** konfiguration. I följande tabell fångas möjliga värden för den här egenskapen.

| Möjliga värden | Beskrivning |
| ---------------- | ------------ |
| Strikt | Standard. Aktiverar endast HTTPS
| Enabled | Aktiverar både HTTP och HTTPS
| Disabled | Aktiverar endast HTTP

## <a name="inbound-client-authentication"></a>Autentisering av inkommande klient

Klienter är entiteter som utför hanterings- och/eller körningsåtgärder. Klienter kan vara andra IoT Edge-moduler, icke-IoT-program.

Event Grid-modulen stöder två typer av klientautentisering:

* SAS-signatur (Shared Access Signature)
* certifikatbaserad

Som standard är modulen Event Grid konfigurerad för att endast acceptera certifikatbaserad autentisering. Vid start hämtar Event Grid-modulen "TrustBundle" från IoT Edge-säkerhetsdemon och använder den för att validera alla klientcertifikat. Klientcertifikat som inte löser till den här `UnAuthorized`kedjan kommer att avvisas med .

### <a name="certificate-based-client-authentication"></a>Certifikatbaserad klientautentisering

Certifikatbaserad autentisering är aktiverat som standard. Du kan välja att inaktivera certifikatbaserad autentisering via egenskapen **inbound__clientAuth__clientCert__enabled**. I följande tabell fångas möjliga värden.

| Möjliga värden | Beskrivning |
| ----------------  | ------------ |
| true | Standard. Kräver att alla begäranden i modulen Event Grid ska kunna presentera ett klientcertifikat. Dessutom måste du konfigurera **inbound__clientAuth__clientCert__source**.
| false | Tvinga inte en klient att visa certifikat.

I följande tabell fångas möjliga värden för **inbound__clientAuth__clientCert__source**

| Möjliga värden | Beskrivning |
| ---------------- | ------------ |
| IoT Edge | Standard. Använder IoT Edge's Trustbundle för att validera alla klientcertifikat.

Om en klient presenterar en självsignerad, som standard, kommer modulen Event Grid att avvisa sådana begäranden. Du kan välja att tillåta självsignerade klientcertifikat via **inbound__clientAuth__clientCert__allowUnknownCA** egenskap. I följande tabell fångas möjliga värden.

| Möjliga värden | Beskrivning |
| ----------------  | ------------|
| true | Standard. Gör att självsignerade certifikat kan presenteras.
| false | Misslyckas begäranden om självsignerade certifikat visas.

>[!IMPORTANT]
>I produktionsscenarier kanske du vill ange **inbound__clientAuth__clientCert__allowUnknownCA** till **false**.

### <a name="sas-key-based-client-authentication"></a>SAS nyckelbaserad klientautentisering

Förutom certifikatbaserad autentisering kan eventrutnätsmodulen även utföra SAS-nyckelbaserad autentisering. SAS-nyckeln är som en hemlighet som konfigurerats i modulen Event Grid som den ska använda för att validera alla inkommande samtal. Klienter måste ange hemligheten i HTTP-huvudet "aeg-sas-key". Begäran kommer att `UnAuthorized` avvisas med om den inte matchar.

Konfigurationen för att styra SAS-nyckelbaserad autentisering är **inbound__clientAuth__sasKeys__enabled**.

| Möjliga värden | Beskrivning  |
| ----------------  | ------------ |
| true | Tillåter SAS-nyckelbaserad autentisering. Kräver **inbound__clientAuth__sasKeys__key1** eller **inbound__clientAuth__sasKeys__key2**
| false | Standard. SAS-nyckelbaserad autentisering är inaktiverad.

 **inbound__clientAuth__sasKeys__key1** och **inbound__clientAuth__sasKeys__key2** är nycklar som du konfigurerar modulen Event Grid för att kontrollera mot inkommande begäranden. Minst en av nycklarna måste konfigureras. Klienten som gör begäran måste presentera nyckeln som en del av begäranhuvudet "**aeg-sas-key**". Om båda nycklarna är konfigurerade kan klienten presentera någon av nycklarna.

> [!NOTE]
>Du kan konfigurera båda autentiseringsmetoderna. I sådana fall kontrolleras SAS-nyckeln först och endast om det misslyckas utförs den certifikatbaserade autentiseringen. För att en begäran ska lyckas behöver bara en av autentiseringsmetoderna lyckas.

## <a name="outbound-client-authentication"></a>Autentisering av utgående klient

Klient i utgående kontext refererar till modulen Event Grid. Den åtgärd som görs är att leverera händelser till abonnenter. Prenumerera moduler betraktas som servern.

Varje IoT Edge-modul tilldelas ett identitetscertifikat av IoT Edge säkerhetsdemon. Vi använder identitetscertifikatet för utgående samtal. Vid förfallodatum uppdateras modulen automatiskt med ett nytt certifikat från säkerhetsdemonen IoT Edge.

Konfigurationen för att styra utgående klientautentisering är **outbound__clientAuth__clientCert__enabled**.

| Möjliga värden | Beskrivning |
| ----------------  | ------------ |
| true | Standard. Kräver att alla utgående begäranden från modulen Event Grid ska kunna presentera ett certifikat. Måste konfigurera **outbound__clientAuth__clientCert__source**.
| false | Kräver inte event grid-modul för att visa sitt certifikat.

Konfigurationen som styr certifikatkällan är **outbound__clientAuth__clientCert__source**.

| Möjliga värden | Beskrivning |
| ---------------- | ------------ |
| IoT Edge | Standard. Använder modulens identitetscertifikat som konfigurerats av IoT Edge-säkerhetsdemon.

### <a name="outbound-server-authentication"></a>Autentisering av utgående server

En av måltyperna för en Event Grid-prenumerant är "Webhook". Som standard accepteras endast HTTPS-slutpunkter för sådana prenumeranter.

Konfigurationen för att styra webhook-målprincipen **outbound__webhook__httpsOnly**.

| Möjliga värden | Beskrivning |
| ----------------  | ------------ |
| true | Standard. Tillåter endast prenumeranter med HTTPS-slutpunkt.
| false | Tillåter prenumeranter med http- eller HTTPS-slutpunkt.

Som standard validerar Event Grid-modulen prenumerantens servercertifikat. Du kan hoppa över validering genom att åsidosätta **outbound__webhook__skipServerCertValidation**. Möjliga värden:

| Möjliga värden | Beskrivning |
| ----------------  | ------------ |
| true | Validera inte abonnentens servercertifikat.
| false | Standard. Verifiera abonnentens servercertifikat.

Om abonnentens certifikat är självsignerat kommer händelserutnätsmodulen som standard att avvisa sådana prenumeranter. Om du vill tillåta självsignerat certifikat kan du åsidosätta **outbound__webhook__allowUnknownCA**. I följande tabell fångas möjliga värden.

| Möjliga värden | Beskrivning |
| ----------------  | ------------ |
| true | Standard. Gör att självsignerade certifikat kan presenteras.
| false | Misslyckas begäranden om självsignerade certifikat visas.

>[!IMPORTANT]
>I produktionsscenarier vill du ange **outbound__webhook__allowUnknownCA** till **false**.

> [!NOTE]
>IoT Edge-miljön genererar självsignerade certifikat. Rekommendationen är att generera certifikat som utfärdats av auktoriserade certifikatutfärdare för produktionsarbetsbelastningar och ange **allowUnknownCA-egenskap** på både inkommande och utgående till **false**.

## <a name="summary"></a>Sammanfattning

En Event Grid-modul är **säker som standard**. Vi rekommenderar att du behåller dessa standardvärden för dina produktionsdistributioner.

Följande är de vägledande principer som ska användas när du konfigurerar:

* Tillåt endast HTTPS-begäranden i modulen.
* Tillåt endast certifikatbaserad klientautentisering. Tillåt endast de certifikat som utfärdas av välkända certifikatutfärdare. Tillåt inte självsignerade certifikat.
* Tillåt inte SASKey-baserad klientautentisering.
* Presentera alltid Event Grid-modulens identitetscertifikat för utgående samtal.
* Tillåt endast HTTPS-prenumeranter för Webhook-måltyper.
* Verifiera alltid prenumerantens servercertifikat för Webhook-måltyper. Tillåt endast certifikat som utfärdats av välkända certifikatutfärdare. Tillåt inte självsignerade certifikat.

Som standard distribueras Event Grid-modulen med följande konfiguration:

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
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
