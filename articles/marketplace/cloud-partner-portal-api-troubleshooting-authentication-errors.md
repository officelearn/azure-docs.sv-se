---
title: Felsöka vanliga autentiseringsfel | Azure Marketplace
description: 'Ger hjälp med vanliga autentiseringsfel när du använder Cloud Partner Portal API: er.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 8c9bd6dbe54f797090c5f7405ad709db954f8fb9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115476"
---
# <a name="troubleshooting-common-authentication-errors"></a>Felsöka vanliga autentiseringsfel

> [!NOTE]
> Cloud Partner Portal API: er är integrerade med partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. I integrationen presenteras små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](./cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter migreringen till Partner Center.

Den här artikeln ger hjälp med vanliga autentiseringsfel när du använder Cloud Partner Portal API: er.

## <a name="unauthorized-error"></a>Obehörigt fel

Om du får `401 unauthorized` fel meddelanden måste du kontrol lera att du har en giltig åtkomsttoken.  Om du inte redan har gjort det skapar du ett grundläggande Azure Active Directory (Azure AD)-program och ett tjänst huvud namn enligt beskrivningen i [använda portalen för att skapa ett Azure Active Directory program och tjänstens huvud namn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md). Använd sedan programmet eller en enkel HTTP POST-begäran för att verifiera din åtkomst.  Du kommer att inkludera klient-ID, program-ID, objekt-ID och den hemliga nyckeln för att hämta åtkomsttoken som visas i följande bild:

![Felsöka 401-felet](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Förbjudet fel

Om du får ett `403 forbidden` fel meddelande, se till att rätt tjänst huvud namn har lagts till i utgivar kontot i Cloud Partner Portal.
Följ stegen på sidan [förutsättningar](./cloud-partner-portal-api-prerequisites.md) för att lägga till tjänstens huvud namn i portalen.

Om rätt huvud namn för tjänsten har lagts till kontrollerar du all övrig information. Var noga med det objekt-ID som angavs i portalen. Det finns två objekt-ID på sidan Azure Active Directory app Registration och du måste använda det lokala objekt-ID: t. Du kan hitta rätt värde genom att gå till **Appregistreringar** sidan för appen och klicka på appens namn under **hanterat program i lokal katalog**. Då går du till appens lokala egenskaper, där du hittar rätt objekt-ID på sidan **Egenskaper** , som du ser i följande bild. Se också till att du använder rätt utgivare-ID när du lägger till tjänstens huvud namn och gör API-anropet.

![Felsöka 403-felet](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
