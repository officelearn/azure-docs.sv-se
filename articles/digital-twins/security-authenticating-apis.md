---
title: Förstå API-autentisering – digitala Azure-dubbla nätverk | Microsoft Docs
description: 'Lär dig hur du ansluter till och autentiserar med API: er med hjälp av Azure Digitals dubbla.'
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 0aed84dc4d78f8e83f568ff3e73d1300aaedbcaa
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860970"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Ansluta till och autentisera med API: er

Azure Digitals dubbla använder Azure Active Directory (Azure AD) för att autentisera användare och skydda program. Azure AD stöder autentisering för olika moderna arkitekturer. Alla är baserade på bransch standard protokollen OAuth 2,0 eller OpenID Connect. Dessutom kan utvecklare använda Azure AD för att bygga program med en enda klient och branschspecifika program (LOB). Utvecklare kan också använda Azure AD för att utveckla program med flera [klienter](how-to-multitenant-applications.md).

En översikt över Azure AD finns på [sidan grundläggande](https://docs.microsoft.com/azure/active-directory/fundamentals/) för steg-för-steg-guider, koncept och snabb starter.

> [!TIP]
> Följ [själv studie kursen](tutorial-facilities-setup.md) för att konfigurera och köra en Azure Digitals-exempel-App.

För att kunna integrera ett program eller en tjänst med Azure AD måste en utvecklare först registrera programmet med Azure AD. Detaljerade anvisningar och skärm bilder finns i [den här snabb](../active-directory/develop/quickstart-register-app.md)starten.

[Fem primära program scenarier](../active-directory/develop/v2-app-types.md) stöds av Azure AD:

* SPA (Single-Side Application): en användare måste logga in på ett program med en enda sida som skyddas av Azure AD.
* Webbläsare till webb program: en användare måste logga in på ett webb program som skyddas av Azure AD.
* Inbyggt program till webb-API: ett internt program som körs på en telefon, surfplatta eller dator måste autentisera en användare för att få resurser från ett webb-API som skyddas av Azure AD.
* Webb program till webb-API: ett webb program måste hämta resurser från ett webb-API som skyddas av Azure AD.
* Daemon-eller serverprogram till webb-API: ett daemon-program eller ett serverprogram utan webb gränssnitt behöver hämta resurser från ett webb-API som skyddas av Azure AD.

> [!IMPORTANT]
> Azure Digitals dubbla har stöd för båda följande autentiseringspaket:
> * Det senaste [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [ADAL (Azure Active Directory Authentication Library)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Anropa digitala dubblare från ett webb-API på Mittens nivå

När utvecklare skapar lösningar för Digitals dubbla lösningar, skapar de vanligt vis ett program eller API på mellan nivå. Appen eller API: et anropar sedan den digitala dubbla API: n med underordnade. För att stödja den här standard arkitekturen för webb lösningar, se till att användarna först:

1. Autentisera med program på mellan nivå

1. En OAuth 2,0-token för token tas emot under autentisering

1. Den hämtade token används sedan för att autentisera med eller anropa API: er som senare kan användas för att använda flödet på uppdrag

Anvisningar om hur du dirigerar ett flöde på väg genom att läsa flöde för [OAuth 2,0 på uppdrags](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Du kan också Visa kod exempel i [anrop till ett underordnat webb-API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera och testa Azure Digitals dubblare med hjälp av OAuth-flödet för implicit beviljande av OAuth 2,0 läser du [Konfigurera PostMan](./how-to-configure-postman.md).

Läs [skapa och hantera roll tilldelningar](./security-create-manage-role-assignments.md)för att lära dig mer om Azure Digitals dubbla säkerheter.