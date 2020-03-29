---
title: Säkerhetsmodell för meddelandehubbar
description: Läs mer om säkerhetsmodellen för Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: b871775bc7a6d795e86147ae9cffa27bdd2f3348
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263769"
---
# <a name="notification-hubs-security"></a>Säkerhet för meddelandehubbar

## <a name="overview"></a>Översikt

I det här avsnittet beskrivs säkerhetsmodellen för Azure Notification Hubs.

## <a name="shared-access-signature-security"></a>Säkerhet för signature för delad åtkomst

Meddelandehubbar implementerar ett säkerhetsschema på en enhetsnivå som kallas *SAS (Shared Access Signature).* Varje regel innehåller ett namn, ett nyckelvärde (delad hemlighet) och en uppsättning rättigheter, vilket förklaras senare i [säkerhetsanspråk](#security-claims). 

När du skapar ett nav skapas två regler automatiskt: en med **lyssningsrättigheter** (som klientappen använder) och en med **alla** rättigheter (som appen används i backend):

- **DefaultListenSharedAccessSignature**: ger endast **lyssningsbehörighet.**
- **DefaultFullSharedAccessSignature**: ger **behörigheter lyssna,** **Hantera**och **Skicka.** Den här principen ska endast användas i appens backend. Använd den inte i klientprogram. använda en princip med endast **Lyssna** åtkomst. Om du vill skapa en ny anpassad åtkomstprincip med en ny SAS-token läser du [SAS-token för åtkomstprinciper](#sas-tokens-for-access-policies) senare i den här artikeln.

När registreringshantering utförs från klientappar, om den information som skickas via meddelanden inte är känslig (till exempel väderuppdateringar), är ett vanligt sätt att komma åt en meddelandehubb att ge nyckelvärdet för regeln Lyssna endast åtkomst till klientappen. och för att ge det viktigaste värdet för regeln full åtkomst till appens backend.

Appar bör inte bädda in nyckelvärdet i Windows Store-klientappar. I stället låta klientappen hämta den från appens backend vid start.

Nyckeln med **Lyssna** åtkomst gör att en klientapp kan registrera sig för alla taggar. Om appen måste begränsa registreringarna till specifika taggar till specifika klienter (till exempel när taggar representerar användar-ID:n) måste appens backend utföra registreringarna. Mer information finns i [Registreringshantering](notification-hubs-push-notification-registration-management.md). Observera att klientappen på detta sätt inte har direkt åtkomst till meddelandehubbar.

## <a name="security-claims"></a>Säkerhetskrav

I likhet med andra entiteter tillåts Notification Hub-åtgärder för tre säkerhetskrav: **Lyssna**, **Skicka**och **Hantera**.

| Begär   | Beskrivning                                          | Tillåtna åtgärder |
| ------- | ---------------------------------------------------- | ------------------ |
| Lyssna  | Skapa/uppdatera, läsa och ta bort enskilda registreringar | Skapa/uppdatera registrering<br><br>Läs registrering<br><br>Läs alla registreringar för en handtag<br><br>Ta bort registrering |
| Skicka    | Skicka meddelanden till meddelandehubben                | Skicka meddelande |
| Hantera  | CRUDs på Notification Hubs (inklusive uppdatering av PNS-autentiseringsuppgifter och säkerhetsnycklar) och läsa registreringar baserat på taggar |Skapa/uppdatera/läsa/ta bort hubbar<br><br>Läsa registreringar efter tagg |

Meddelandehubbar accepterar SAS-token som genereras med delade nycklar som konfigurerats direkt på hubben.

Det går inte att skicka ett meddelande till mer än ett namnområde. Namnområden är logiska behållare för meddelandehubbar och är inte involverade i att skicka meddelanden.

Använd åtkomstprinciper på namnområdesnivå (autentiseringsuppgifter) för åtgärder på namnområdesnivå. till exempel: lista nav, skapa eller ta bort nav, etc. Endast åtkomstprinciperna på navnivå kan du skicka meddelanden.

### <a name="sas-tokens-for-access-policies"></a>SAS-token för åtkomstprinciper

Så här skapar du ett nytt säkerhetsanspråk eller visar befintliga SAS-nycklar:

1. Logga in på Azure Portal.
2. Välj **Alla resurser**.
3. Välj namnet på den meddelandehubb som du vill skapa anspråket för eller visa SAS-tangenten för.
4. Välj **Åtkomstprinciper**på menyn till vänster .
5. Välj **Ny princip** om du vill skapa ett nytt säkerhetskrav. Ge principen ett namn och välj de behörigheter som du vill bevilja. Välj sedan **OK**.
6. Den fullständiga anslutningssträngen (inklusive den nya SAS-nyckeln) visas i fönstret Åtkomstprinciper. Du kan kopiera strängen till Urklipp för senare användning.

Om du vill extrahera SAS-nyckeln från en viss princip väljer du knappen **Kopiera** bredvid principen som innehåller den SAS-nyckel du vill använda. Klistra in det här värdet på en tillfällig plats och kopiera sedan SAS-nyckeldelen av anslutningssträngen. I det här exemplet används namnområdet Notification Hubs som kallas **mytestnamespace1**och en princip med namnet **policy2**. SAS-nyckeln är värdet nära slutet av strängen, som anges av **SharedAccessKey:**

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![Hämta SAS-nycklar](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Nästa steg

- [Översikt över meddelandehubbar](notification-hubs-push-notification-overview.md)
