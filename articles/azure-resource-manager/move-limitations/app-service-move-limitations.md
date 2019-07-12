---
title: Flytta Azure App Service-resurser till ny prenumeration eller resursgrupp grupp
description: Använd Azure Resource Manager för att flytta App Service-resurser till en ny resursgrupp eller prenumeration.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: c1a09ff4c29a2fedfea2c165a95c042985b3c83a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723576"
---
# <a name="move-guidance-for-app-service-resources"></a>Flytta vägledning för App Service-resurser

Stegen för att flytta skiljer sig App Service-resurser baserat på om du flyttar resurser inom en prenumeration eller till en ny prenumeration.

## <a name="move-in-same-subscription"></a>Flytta i samma prenumeration

När du flyttar en Webbapp _inom samma prenumeration_, du kan inte flytta från tredje part SSL-certifikat. Men du kan flytta en Webbapp till den nya resursgruppen utan att flytta dess certifikat från tredje part och appens SSL fortfarande fungerar.

Om du vill flytta SSL-certifikat med Webbappen gör du följande:

1. Ta bort certifikatet från tredje part från Web App, men behålla en kopia av certifikatet
2. Flytta Webbappen.
3. Ladda upp certifikatet från tredje part till flyttade Webbappen.

## <a name="move-across-subscriptions"></a>Flytta mellan prenumerationer

När du flyttar en Webbapp _mellan prenumerationer_, gäller följande begränsningar:

- Målresursgruppen får inte ha några befintliga App Service-resurser. App Service-resurser är:
    - Web Apps
    - App Service-planer
    - Överförda eller importerade SSL-certifikat
    - Apptjänstmiljöer
- Alla App Service-resurser i resursgruppen måste flyttas tillsammans.
- App Service-resurser kan bara flyttas från resursgruppen där de skapades. Om en App Service-resursen är inte längre i dess ursprungliga resursgruppen, flytta tillbaka till dess ursprungliga resursgruppen. Flytta sedan resursen mellan prenumerationer.

Om du inte kommer ihåg ursprungliga resursgruppen, kan du hitta den diagnostiken. Din webbapp, Välj **diagnostisera och lösa problem**. Välj **konfiguration och hantering av**.

![Välj diagnostik](./media/app-service-move-limitations/select-diagnostics.png)

Välj **migreringsalternativ**.

![Välj migreringsalternativ](./media/app-service-move-limitations/select-migration.png)

Välj alternativet för rekommenderade steg för att flytta webbappen.

![Välj rekommenderade åtgärder](./media/app-service-move-limitations/recommended-steps.png)

Du ser de rekommenderade åtgärderna som ska vidtas innan du flyttar resurser. Informationen omfattar ursprungliga resursgruppen för webbappen.

![Rekommendationer](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Flytta App Service Certificate

Du kan flytta din App Service-certifikat till en ny resursgrupp eller prenumeration. Om din App Service-certifikat är bundet till en webbapp, måste du vidta vissa åtgärder innan du flyttar resurser till en ny prenumeration. Ta bort SSL-bindning och privata certifikat från webbapp innan du flyttar resurser. App Service Certificate behöver inte tas bort, bara privata certifikat i webbapp.

## <a name="move-support"></a>Stöd för att flytta

För att avgöra vilka App Service-resurser kan flyttas, se flytta supportstatus för:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Nästa steg

Kommandon att flytta resurser finns i [flytta resurser till ny resursgrupp eller prenumeration](../resource-group-move-resources.md).
