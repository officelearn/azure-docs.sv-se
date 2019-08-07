---
title: Förstå Azure Digitals dubbla API-autentisering | Microsoft Docs
description: 'Använd Azure Digitals sammanflätade för att ansluta och autentisera till API: er'
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: lyhughes
ms.openlocfilehash: c0b4b6a13143f613bec64c8507f1726e2450be44
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815546"
---
# <a name="connect-and-authenticate-to-apis"></a>Anslut och autentisera till API: er

Azure Digitals dubbla använder Azure Active Directory (Azure AD) för att autentisera användare och skydda program. Azure AD stöder autentisering för olika moderna arkitekturer. Alla är baserade på bransch standard protokollen OAuth 2,0 eller OpenID Connect. Dessutom kan utvecklare använda Azure AD för att bygga program med en enda klient och branschspecifika program (LOB). Utvecklare kan också använda Azure AD för att utveckla program med flera klienter.

En översikt över Azure AD finns på [sidan grundläggande](https://docs.microsoft.com/azure/active-directory/fundamentals/index) för steg-för-steg-guider, koncept och snabb starter.

För att kunna integrera ett program eller en tjänst med Azure AD måste en utvecklare först registrera programmet med Azure AD. Detaljerade anvisningar och skärm dum par finns i [den här snabb](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)starten.

[Fem primära program scenarier](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) stöds av Azure AD:

* SPA (Single-Side Application): En användare måste logga in på ett program med en enda sida som skyddas av Azure AD.
* Webbläsare till webb program: En användare måste logga in på ett webb program som skyddas av Azure AD.
* Inbyggt program till webb-API: Ett internt program som körs på en telefon, surfplatta eller dator måste autentisera en användare för att få resurser från ett webb-API som skyddas av Azure AD.
* Webb program till webb-API: Ett webb program måste hämta resurser från ett webb-API som skyddas av Azure AD.
* Daemon-eller Server program till webb-API: Ett daemon-program eller ett serverprogram utan webb gränssnitt behöver hämta resurser från ett webb-API som skyddas av Azure AD.

Windows Azure Authentication Library erbjuder många sätt att förvärva Active Directory-token. Mer information om biblioteket och kod exemplen finns i [den här artikeln](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Anropa digitala dubblare från ett webb-API på Mittens nivå

När utvecklare skapar lösningar för Digitals dubbla lösningar, skapar de vanligt vis ett program eller API på mellan nivå. Appen eller API: et anropar sedan den digitala dubbla API: n med underordnade. För att stödja den här standard arkitekturen för webb lösningar, se till att användarna först:

1. Autentisera med program på mellan nivå

1. En OAuth 2,0-token för token tas emot under autentisering

1. Den hämtade token används sedan för att autentisera med eller anropa API: er som senare kan användas för att använda flödet på uppdrag

Anvisningar om hur du dirigerar ett flöde på väg finns i [OAuth 2,0 på uppdrag av Flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Du kan också Visa kod exempel i [anrop till ett underordnat webb-API](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera och testa Azure Digitals dubblare med hjälp av OAuth-flödet för implicit beviljande av OAuth 2,0 läser du [Konfigurera PostMan](./how-to-configure-postman.md).

Läs [skapa och hantera roll tilldelningar](./security-create-manage-role-assignments.md)för att lära dig mer om Azure Digitals dubbla säkerheter.