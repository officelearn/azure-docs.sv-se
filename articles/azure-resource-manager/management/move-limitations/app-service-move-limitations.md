---
title: Flytta Azure App Service resurser
description: Använd Azure Resource Manager för att flytta App Service resurser till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: d0ecd117bdcda9238e310a3020dba19a6871a3fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80655773"
---
# <a name="move-guidance-for-app-service-resources"></a>Flytta vägledning för App Service resurser

I den här artikeln beskrivs stegen för att flytta App Service-resurser. Det finns särskilda krav för att flytta App Service resurser till en ny prenumeration.

## <a name="move-across-subscriptions"></a>Flytta över prenumerationer

När du flyttar en webbapp över prenumerationer gäller följande rikt linjer:

- Mål resurs gruppen får inte ha några befintliga App Service-resurser. App Service resurser är:
    - Web Apps
    - App Service-planer
    - Överförda eller importerade TLS/SSL-certifikat
    - Apptjänstmiljöer
- Alla App Service resurser i resurs gruppen måste flyttas tillsammans. Observera att App Service miljöer inte kan flyttas till en ny resurs grupp eller till en ny prenumeration.
- Du kan flytta ett certifikat som är bundet till en webbplats utan att ta bort TLS-bindningarna, förutsatt att certifikatet flyttas med alla andra resurser i resurs gruppen.
- App Service resurser kan bara flyttas från resurs gruppen där de ursprungligen skapades. Om en App Service resurs inte längre finns i den ursprungliga resurs gruppen flyttar du tillbaka den till den ursprungliga resurs gruppen. Flytta sedan resursen över prenumerationer.

Om du inte kommer ihåg den ursprungliga resurs gruppen kan du hitta den via diagnostik. För din webbapp väljer du **diagnostisera och lösa problem**. Välj sedan **konfiguration och hantering**.

![Välj diagnostik](./media/app-service-move-limitations/select-diagnostics.png)

Välj **alternativ för migrering**.

![Välj alternativ för migrering](./media/app-service-move-limitations/select-migration.png)

Välj alternativet för rekommenderade steg för att flytta webb programmet.

![Välj rekommenderade steg](./media/app-service-move-limitations/recommended-steps.png)

Du ser de rekommenderade åtgärderna innan du flyttar resurserna. Informationen omfattar den ursprungliga resurs gruppen för webbappen.

![Rekommendationer](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Stöd för att flytta

Information om hur du tar reda på vilka App Service resurser som kan flyttas finns i flytta support status för:

- [Microsoft. AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft. CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft. DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft. Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Nästa steg

För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../move-resource-group-and-subscription.md).
