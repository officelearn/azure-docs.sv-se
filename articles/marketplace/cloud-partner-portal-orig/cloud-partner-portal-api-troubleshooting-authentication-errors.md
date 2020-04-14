---
title: Felsöka vanliga autentiseringsfel | Azure Marketplace
description: Ger hjälp med vanliga autentiseringsfel när du använder API:erna för Cloud Partner Portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: d8fd1eb4bef987b4a8605e4be780512a914ec8b5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255999"
---
# <a name="troubleshooting-common-authentication-errors"></a>Felsöka vanliga autentiseringsfel

> [!NOTE]
> Api:erna för Cloud Partner Portal är integrerade med Partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. Integrationen medför små förändringar. Granska ändringarna i [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) för att säkerställa att koden fortsätter att fungera efter migreringen till Partner Center.

Den här artikeln hjälper dig med vanliga autentiseringsfel när du använder API:erna för Cloud Partner Portal.

## <a name="unauthorized-error"></a>Obehörigt fel

Om du konsekvent `401 unauthorized` får fel kontrollerar du att du har en giltig åtkomsttoken.  Om du inte redan har gjort det skapar du ett grundläggande Azure Active Directory-program (Azure AD) och ett tjänsthuvudnamn enligt beskrivningen i [Använd portal för att skapa ett Azure Active Directory-program och tjänsthuvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Använd sedan programmet eller en enkel HTTP POST-begäran för att verifiera din åtkomst.  Du kommer att inkludera klient-ID, application-ID, Object ID och den hemliga nyckeln för att hämta åtkomsttoken som visas i följande bild:

![Felsöka 401-felet](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Förbjudet fel

Om du `403 forbidden` får ett felmeddelande kontrollerar du att rätt tjänsthuvudnamn har lagts till i ditt utgivarkonto i Cloud Partner Portal.
Följ stegen på sidan Förutsättningar för att lägga till [tjänstens](./cloud-partner-portal-api-prerequisites.md) huvudnamn i portalen.

Om rätt tjänsthuvudnamn har lagts till kontrollerar du all annan information. Var uppmärksam på det objekt-ID som anges på portalen. Det finns två objekt-ID:er på registreringssidan för Azure Active Directory-appar och du måste använda det lokala objekt-ID:et. Du hittar rätt värde genom att gå till sidan **Appregistreringar** för din app och klicka på appnamnet under **Hanterat program i lokal katalog**. Detta tar dig till de lokala egenskaperna för appen, där du kan hitta rätt objekt-ID på sidan **Egenskaper,** som visas i följande bild. Se också till att du använder rätt utgivar-ID när du lägger till tjänstens huvudnamn och ringer API-anropet.

![Felsöka 403-felet](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
