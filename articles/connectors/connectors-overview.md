---
title: Översikt över Logic Apps kopplingar | Microsoft Docs
description: Översikt över kopplingar som kan användas i en logikapp
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: ca8dab2e-9b69-4b1e-865d-1facd9f0cdac
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan
ms.openlocfilehash: 6f7d8f99bfa09847c01831a06efa8b94c1c0a89a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
ms.locfileid: "27961660"
---
# <a name="using-connectors-in-a-logic-app"></a>Med hjälp av anslutningar i en logikapp
Kopplingar ger snabb åtkomst till händelser, data och åtgärder över tjänster, protokoll och plattformar.  En fullständig lista över kopplingar som har stöd för Logic Apps kan [finns här](apis-list.md).  Kopplingar kan användas som en utlösare eller en åtgärd i en logikapp och kan kräva en konfigurerad *anslutning* att använda (till exempel: auktorisera ett Twitter-konto för att komma åt eller efter åt dig).

## <a name="basics"></a>Grundläggande inställningar
Kopplingar är värdbaserade tjänster som du kan komma åt som en del av en logikapp kan integreras med andra tjänster som Dynamics, Azure, Salesforce, [och mer](apis-list.md).  De distribueras och hanteras av Microsoft, så att du kan bygga arbetsflöden integrering med skala, dataflöde och säkerhet som tagit hand om.  Du kan lägga till en koppling till en logikapp genom att söka och välja en koppling åtgärd eller utlösa **visa Microsoft hanterade API: er**.

![Åtgärd-menyn för att välja utlösare][1]

Varje koppling åtgärd eller utlösare har en uppsättning egenskaper att konfigurera.  Du kan klicka på knapparna info för mer information om åtgärden eller referera till dokumentationen [mer](apis-list.md).

Om du vill integrera med en tjänst eller API som inte är ännu en koppling, du kan också utöka logikappar via en [anpassad koppling](../logic-apps/logic-apps-create-api-app.md) eller bara anropa direkt till tjänsten via ett protokoll som HTTP.

## <a name="triggers"></a>Utlösare
Vissa kopplingar har en utlösare, vilket innebär att en händelse från kopplingen ska utlösa en logikapp som skickas i alla data som en del av utlösaren.  En utlösare är alltid det första steget i en logikapp.  Populära utlösare är åtgärder som:

* Återkommande - köras varje timme
* När en HTTP-begäran tas emot
* När ett objekt läggs till en kö
* När ett e-postmeddelande tas emot

Vissa utlösare utlöses det ögonblick en händelse sker via ett meddelande till logikappen och andra måste ett intervall som konfigurerats på hur ofta logikappen kontrollerar tjänsten för en händelse (upp till var 15: e sekund).  

När en händelse har tagits emot, kör logikappen ska utlösas och åtgärder i arbetsflödet startar.  Du kan också komma åt alla data från utlösare i hela arbetsflödet (till exempel utlösaren 'på en ny tweet' Skicka tweet till kör).

## <a name="actions"></a>Åtgärder
De flesta kopplingar har en eller flera åtgärder som kan köras som en del av arbetsflödet.  Åtgärder är de steg som kan inträffar när körningen har utlösts från en utlösare.  Att lägga till en åtgärd klickar du på den **nytt steg** knappen och Sök efter den koppling du vill använda.  När markerat (och när du har konfigurerat någon [anslutningar](#connections) som kan krävas) visas kortet åtgärd som du kan konfigurera.  Du kan välja data från föregående steg genom att klicka på någon av token som utdata eller ange i någon annan konfiguration efter behov.

![Konfigurera en koppling åtgärd][2]

## <a name="connections"></a>Anslutningar
De flesta kopplingar måste du konfigurera en *anslutning* innan du kan använda anslutningstjänsten.  En *anslutning* finns någon inloggning eller anslutning konfiguration som krävs för att få åtkomst till anslutningen.  För kopplingar som använder OAuth, skapa en anslutning innebär att du loggar in på tjänsten (till exempel Office 365, Salesforce eller GitHub) där åtkomst-token kan krypteras och lagras på ett säkert sätt i en Azure hemliga butik.  Övriga kopplingar (till exempel FTP och SQL) kräver en anslutning som innehåller konfiguration som serveradress, användarnamn och lösenord.  Konfigurationsdetaljerna anslutningen också krypteras och lagras på ett säkert sätt.  Kommer att kunna använda tjänsten för som gör att tjänsten.  Vi kan fortsätta att uppdatera den åtkomst-token under obestämd tid för Azure Active Directory OAuth-anslutningar (till exempel Office 365 och Dynamics).  Andra tjänster kan ange begränsningar för hur länge vi kan använda en token utan den håller på att uppdateras.  I allmänhet vissa åtgärder som att ändra ett lösenord kommer att upphäva alla åtkomsttoken.  

Anslutningar kan visas och hanteras i Azure genom att klicka på **Bläddra** och välja **API anslutningar**.  Från resursen för API-anslutningar kan du visa, redigera, uppdatera eller att återauktorisera alla anslutningar som du har skapat.

## <a name="next-steps"></a>Nästa steg
* [Skapa din första logiska app](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Läs vanliga användningsområden och exempel på logikappar](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Kom igång med enterprise integration-utlösare och åtgärder](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png
