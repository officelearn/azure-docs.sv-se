---
title: Flytta Azure App Service-resurser
description: Använd Azure Resource Manager för att flytta App Service-resurser till en ny resursgrupp eller prenumeration.
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 338b0559a5de9468ff60024b88d0f676a9fc3e8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479805"
---
# <a name="move-guidance-for-app-service-resources"></a>Flytta vägledning för App Service-resurser

I den här artikeln beskrivs stegen för att flytta App Service-resurser. Det finns särskilda krav för att flytta App Service-resurser till en ny prenumeration.

## <a name="move-across-subscriptions"></a>Flytta mellan prenumerationer

När du flyttar en webbapp över prenumerationer gäller följande vägledning:

- Målresursgruppen får inte ha några befintliga App Service-resurser. App Service-resurser omfattar:
    - Web Apps
    - App Service-planer
    - Uppladdade eller importerade SSL-certifikat
    - Apptjänstmiljöer
- Alla App Service-resurser i resursgruppen måste flyttas tillsammans. Observera att App Service-miljöer inte kan flyttas till en ny resursgrupp eller till en ny prenumeration.
- Du kan flytta ett certifikat bundet till en webbplats utan att ta bort SSL-bindningarna, så länge certifikatet flyttas med alla andra resurser i resursgruppen.
- AppTjänstresurser kan bara flyttas från resursgruppen där de ursprungligen skapades. Om en App Service-resurs inte längre finns i den ursprungliga resursgruppen flyttar du tillbaka den till den ursprungliga resursgruppen. Flytta sedan resursen över prenumerationer.

Om du inte kommer ihåg den ursprungliga resursgruppen kan du hitta den via diagnostik. För din webbapp väljer du **Diagnostisera och lösa problem**. Välj sedan **Konfiguration och hantering**.

![Välj diagnostik](./media/app-service-move-limitations/select-diagnostics.png)

Välj **Migreringsalternativ**.

![Välj migreringsalternativ](./media/app-service-move-limitations/select-migration.png)

Välj alternativet för rekommenderade steg för att flytta webbappen.

![Välj rekommenderade steg](./media/app-service-move-limitations/recommended-steps.png)

Du ser de rekommenderade åtgärderna som ska vidtas innan du flyttar resurserna. Informationen innehåller den ursprungliga resursgruppen för webbappen.

![Rekommendationer](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Stöd för att flytta

Information om vilka App Service-resurser som kan flyttas finns i flytta supportstatus för:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Nästa steg

Kommandon som ska flyttas finns i [Flytta resurser till ny resursgrupp eller prenumeration](../move-resource-group-and-subscription.md).
