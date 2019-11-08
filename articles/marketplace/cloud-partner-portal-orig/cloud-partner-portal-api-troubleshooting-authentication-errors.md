---
title: Felsöka vanliga autentiseringsfel | Azure Marketplace
description: 'Ger hjälp med vanliga autentiseringsfel när du använder Cloud Partner Portal API: er.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 0990e9aedf17f6d4ad01e4911e47efd60001f3d7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827373"
---
# <a name="troubleshooting-common-authentication-errors"></a>Felsöka vanliga autentiseringsfel

Den här artikeln ger hjälp med vanliga autentiseringsfel när du använder Cloud Partner Portal API: er.

## <a name="unauthorized-error"></a>Obehörigt fel

Om du konsekvent får `401 unauthorized` fel ska du kontrol lera att du har en giltig åtkomsttoken.  Om du inte redan har gjort det skapar du ett grundläggande Azure Active Directory (Azure AD)-program och ett tjänst huvud namn enligt beskrivningen i [använda portalen för att skapa ett Azure Active Directory program och tjänstens huvud namn som kan komma åt resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Använd sedan programmet eller en enkel HTTP POST-begäran för att verifiera din åtkomst.  Du kommer att inkludera klient-ID, program-ID, objekt-ID och den hemliga nyckeln för att hämta åtkomsttoken som visas i följande bild:

![Felsöka 401-felet](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Otillåtet fel

Om du får ett `403 forbidden` fel, se till att rätt huvud namn för tjänsten har lagts till i utgivar kontot i Cloud Partner Portal.
Följ stegen på sidan [förutsättningar](./cloud-partner-portal-api-prerequisites.md) för att lägga till tjänstens huvud namn i portalen.

Om rätt huvud namn för tjänsten har lagts till kontrollerar du all övrig information. Var noga med det objekt-ID som angavs i portalen. Det finns två objekt-ID på sidan Azure Active Directory app Registration och du måste använda det lokala objekt-ID: t. Du kan hitta rätt värde genom att gå till **Appregistreringar** sidan för appen och klicka på appens namn under **hanterat program i lokal katalog**. Då går du till appens lokala egenskaper, där du hittar rätt objekt-ID på sidan **Egenskaper** , som du ser i följande bild. Se också till att du använder rätt utgivare-ID när du lägger till tjänstens huvud namn och gör API-anropet.

![Felsöka 403-felet](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
