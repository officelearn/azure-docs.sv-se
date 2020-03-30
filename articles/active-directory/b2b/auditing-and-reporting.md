---
title: Granska och rapportera en B2B-samarbetsanvändare - Azure AD
description: Gästanvändaregenskaper kan konfigureras i Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74d2473d37dcf0d435ab61763e72a64111845956
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273298"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Granska och rapportera en B2B-samarbetsanvändare
Med gästanvändare har du granskningsfunktioner som liknar med medlemsanvändare. 

## <a name="access-reviews"></a>Åtkomstgranskningar
Du kan använda åtkomstgranskningar för att regelbundet kontrollera om gästanvändare fortfarande behöver åtkomst till dina resurser. Funktionen **Access-granskningar** är tillgänglig i **Azure Active Directory** under **Hantera** > **organisationsrelationer**. (Du kan också söka efter "åtkomstgranskningar" från **alla tjänster** i Azure-portalen.) Mer information om hur du använder åtkomstgranskningar finns i [Hantera gäståtkomst med Azure AD-åtkomstgranskningar](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Granskningsloggar

Azure AD-granskningsloggarna innehåller register över system- och användaraktiviteter, inklusive aktiviteter som initierats av gästanvändare. Om du vill komma åt granskningsloggar väljer du **Granskningsloggar**i **Azure Active Directory**. **Monitoring** Här är ett exempel på inbjudan och inlösen historia inbjudna Sam Oogle:

![Skärmbild som visar och exempel på granskningsloggutdata](./media/auditing-and-reporting/audit-log.png)

Du kan dyka in i var och en av dessa händelser för att få information. Låt oss till exempel titta på acceptansdetaljerna.

![Skärmbild som visar och exempel på aktivitetsinformationsutdata](./media/auditing-and-reporting/activity-details.png)

Du kan också exportera dessa loggar från Azure AD och använda det rapporteringsverktyg du väljer för att få anpassade rapporter.

### <a name="next-steps"></a>Nästa steg

- [Användaregenskaper för B2B-samarbete](user-properties.md)

