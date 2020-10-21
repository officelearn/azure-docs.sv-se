---
title: Visa reservationer för Azure-resurser | Microsoft Docs
description: Lär dig hur du visar Azure-reservationer i Azure-portalen. Se reservationer och användning med hjälp av API:er, PowerShell, CLI och Power BI.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 926e0c8bc0fdff580b4d1e84be5940e0945d52ae
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146274"
---
# <a name="view-azure-reservations"></a>Visa Azure-reservationer

Du kan visa och hantera den köpta reservationen i Azure-portalen.

## <a name="permissions-to-view-a-reservation"></a>Behörighet att visa en reservation

Om du vill visa eller hantera en reservation måste du ha läsar- eller ägarbehörighet till den. När du köper en reservation har du och kontoadministratören som standard automatiskt rollen som ägare för reservationsbeställningen och reservationen. Om du vill att andra personer ska kunna visa reservationen måste du lägga till dem som **ägare** eller **läsare** för reservationsbeställningen eller reservationen. Om du lägger till någon i prenumerationen för fakturering av reservationen läggs de inte automatiskt till i reservationen. 

Du kan läsa mer i [Lägga till eller ändra användare som kan hantera en reservation](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Visa reservation och användning i Azure-portalen

Så här visar du en reservation som ägare eller läsare

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till [Reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. I listan visas alla reservationer där du har rollen ägare eller läsare. För varje reservation visas den senast kända utnyttjandegraden.
4. Klicka på användningsprocenten för att se användningshistorik och information. Se mer information i videon nedan.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-cli"></a>Få reservationer och användning med API:er, PowerShell, CLI

Hämta en lista över alla reservationer med följande resurser
- [API: Reservationsbeställning – Lista](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: Reservationsbeställning – Lista](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI: Reservationsbeställning – Lista](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

Du kan också hämta [reservationsanvändningen](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) med hjälp av användnings-API:et för reserverad instans. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Visa reservationer och användning i Power BI

Det finns två alternativ för Power BI-användare
- Innehållspaket: Reservationsköp och användningsdata är tillgängliga i [Power BI-innehållspaketet Consumption Insights](/power-bi/desktop-connect-azure-cost-management). Skapa önskade rapporter med det här innehållspaketet. 
- Cost Management-app: Använd [Cost Management-appen](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) för färdiga rapporter som du kan anpassa ytterligare.

## <a name="next-steps"></a>Nästa steg

- [Hantera Azure-reservationer](manage-reserved-vm-instance.md).
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
- [Förstå reservationsanvändning för CSP-prenumerationer](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).