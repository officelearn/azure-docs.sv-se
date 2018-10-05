---
title: Felsök vanliga autentiseringsfel | Microsoft Docs
description: 'Ger hjälp med vanliga autentiseringsfel när du använder Cloud Partner Portal-API: er.'
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 82a5ef86d1ca35cddb05cb4e126e64cc3759bcc0
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811313"
---
<a name="troubleshooting-common-authentication-errors"></a>Felsök vanliga autentiseringsfel
------------------------------------------

Den här artikeln innehåller hjälp med vanliga autentiseringsfel när du använder Cloud Partner Portal-API: er.

### <a name="unauthorized-error"></a>Behörighetsfel

Om du konsekvent får `401 unauthorized` fel, kontrollera att du har en giltig åtkomsttoken.  Om du inte redan har gjort det, skapa ett program med grundläggande Azure Active Directory (Azure AD) och ett huvudnamn för tjänsten enligt beskrivningen i [Använd portalen för att skapa en Azure Active Directory-program och tjänstens huvudnamn som kan komma åt resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Använd sedan programmet eller en enkel HTTP POST-begäran för att verifiera din åtkomst.  Du kan inkludera klient-ID, program-ID, objekt-ID och den hemliga nyckeln för att erhålla åtkomsttoken som du ser i följande bild:

![Felsöka 401-fel](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


### <a name="forbidden-error"></a>Förbjudet fel

Om du får en `403 forbidden` fel, kontrollera att rätt tjänstens huvudnamn har lagts till ditt utgivarkonto i partnerportalen i molnet.
Följ stegen i den [krav](./cloud-partner-portal-api-prerequisites.md) sidan för att lägga till tjänstens huvudnamn på portalen.

Om rätt tjänstens huvudnamn har lagts till, kontrollerar du all annan information. Stäng närmare på objekt-ID anges på portalen. Det finns två objekt-ID i Azure Active Directory app-registreringssidan du måste använda lokala objekt-ID. Du hittar det korrekta värdet genom att gå till den **appregistreringar** för din app och klicka på namnet på under **hanterat program i den lokala katalogen**. Detta tar dig till de lokala egenskaperna för appen, där du hittar rätt objekt-ID i den **egenskaper** sidan, enligt följande bild. Kontrollera också att du använder rätt Publicerings-ID när du lägger till tjänstens huvudnamn och API-anropet.

![Felsöka 403-fel](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
