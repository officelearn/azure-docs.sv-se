---
title: Registrera ensidiga appar – Microsofts identitetsplattform | Azure
description: Lär dig hur du skapar ett ensidigt program (appregistrering)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 6f690a8b3436a45d434ccad2bbaa7d2a1b0b76aa
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882156"
---
# <a name="single-page-application-app-registration"></a>Enkelsidig applikation: Appregistrering

På den här sidan beskrivs appregistreringsdetyderna för ett spa.This page explains the app registration specifics for a single-page application (SPA).

Följ stegen för att [registrera ett nytt program med Microsofts identitetsplattform](quickstart-register-app.md)och välj de konton som stöds för ditt program. SPA-scenariot kan stödja autentisering med konton i din organisation eller alla organisationer och personliga Microsoft-konton.

Läs sedan om de specifika aspekterna av programregistrering som gäller för ensidiga program.

## <a name="register-a-redirect-uri"></a>Registrera en uri för omdirigering

Det implicita flödet skickar token i en omdirigering till det ensidiga programmet som körs i en webbläsare. Så det är viktigt att registrera en omdirigera URI där ditt program kan ta emot tokens. Kontrollera att omdirigerings-URI:n exakt matchar URI:n för ditt program.

Gå till ditt registrerade program i [Azure-portalen.](https://go.microsoft.com/fwlink/?linkid=2083908) Välj **webbplattformen** på sidan **Autentisering** i programmet. Ange värdet för omdirigerings-URI:n för ditt program i fältet **Omdirigera URI.**

## <a name="enable-the-implicit-flow"></a>Aktivera det implicita flödet

På samma **autentiseringssida,** under **Avancerade inställningar,** måste du också aktivera **implicit beviljande**. Om ditt program bara loggar in användare och hämtar ID-token räcker det med att markera kryssrutan **ID-token.**

Om ditt program också behöver få åtkomsttoken för att anropa API:er, se till att markera kryssrutan **Access-token** också. Mer information finns i [ID-token](./id-tokens.md) och [access-token](./access-tokens.md).

## <a name="api-permissions"></a>API-behörigheter

Ensidiga program kan anropa API:er för den inloggade användarens räkning. De måste begära delegerade behörigheter. Mer information finns i [Lägga till behörigheter för åtkomst till webb-API:er](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appens kodkonfiguration](scenario-spa-app-configuration.md)
