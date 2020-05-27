---
title: Så här skyddar du din resurs-hierarki – Azure-styrning
description: Lär dig hur du skyddar din resurs-hierarki med hierarkiska inställningar som inkluderar inställning av standard hanterings gruppen.
ms.date: 05/21/2020
ms.topic: conceptual
ms.openlocfilehash: 60c184d176ae62c1af525db656c56a83422cb94a
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837418"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Så här skyddar du din resurs-hierarki

Dina resurser, resurs grupper, prenumerationer, hanterings grupper och klient organisation sammanfattar tillsammans din mappstruktur. Inställningar för rot hanterings gruppen, till exempel anpassade RBAC-roller eller Azure Policy princip tilldelningar, kan påverka varje resurs i din resurs-hierarki. Det är viktigt att skydda resurs-hierarkin från ändringar som kan påverka alla resurser negativt.

Hanterings grupper har nu hierarkiska inställningar som gör det möjligt för klient administratören att styra dessa beteenden. Den här artikeln beskriver var och en av de tillgängliga inställningarna för hierarkin och hur du ställer in dem.

## <a name="rbac-permissions-for-hierarchy-settings"></a>RBAC-behörigheter för hierarkiska inställningar

Om du konfigurerar någon av inställningarna för hierarkin krävs följande två RBAC-åtgärder för rot hanterings gruppen:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Dessa åtgärder låter bara en användare läsa och uppdatera inställningarna för hierarkin. Åtgärderna ger inte någon annan åtkomst till hanterings gruppens hierarki eller resurser i hierarkin. Båda dessa åtgärder är tillgängliga i den inbyggda **inställningarna administratör**för RBAC-rolltjänsten.

## <a name="setting---default-management-group"></a>Inställning-standard hanterings grupp

Som standard läggs en ny prenumeration i en klient till som medlem i rot hanterings gruppen. Om princip tilldelningar, rollbaserad åtkomst kontroll (RBAC) och andra styrnings konstruktioner tilldelas till rot hanterings gruppen, kommer de direkt att påverka dessa nya prenumerationer. Därför tillämpar många organisationer inte dessa konstruktioner i rot hanterings gruppen, även om det är den önskade platsen för att tilldela dem. I andra fall är en mer begränsande uppsättning kontroller önskade för nya prenumerationer, men ska inte tilldelas alla prenumerationer. Den här inställningen stöder båda användnings fall.

Genom att tillåta att standard hanterings gruppen för nya prenumerationer definieras kan styrnings konstruktioner för hela organisationen tillämpas på rot hanterings gruppen och en separat hanterings grupp med princip tilldelningar eller RBAC-tilldelningar som är mer anpassade till en ny prenumeration kan definieras.

Om du vill konfigurera den här inställningen anropas [inställningarna för hierarki](/rest/api/resources/hierarchysettings) REST API slut punkten. Det gör du genom att använda följande REST API URI-och Body-format. Ersätt `{rootMgID}` med ID för din rot hanterings grupp och `{defaultGroupID}` med ID för hanterings gruppen som ska vara standard hanterings grupp:

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Begärandetext

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Om du vill ange standard hanterings gruppen tillbaka till rot hanterings gruppen använder du samma slut punkt och anger **defaultManagementGroup** till värdet `/providers/Microsoft.Management/managementGroups/{rootMgID}` .

## <a name="setting---require-authorization"></a>Inställning-Kräv auktorisering

Alla användare kan som standard skapa nya hanterings grupper inom en klient organisation. Administratörer för en klient organisation kan vilja bara ge dessa behörigheter till vissa användare för att upprätthålla konsekvens och överensstämmelse i hierarkin för hanterings grupper. Om den här inställningen är aktive rad måste en användare `Microsoft.Management/managementGroups/write` utföra åtgärden på rot hanterings gruppen för att skapa nya underordnade hanterings grupper.

Om du vill konfigurera den här inställningen anropas [inställningarna för hierarki](/rest/api/resources/hierarchysettings) REST API slut punkten. Det gör du genom att använda följande REST API URI-och Body-format. Värdet är ett _booleskt_värde, så ange antingen **Sant** eller **falskt** för värdet. Värdet **True** aktiverar den här metoden för att skydda din hanterings grupp hierarki:

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Begärandetext

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Du inaktiverar inställningen genom att använda samma slut punkt och ange **requireAuthorizationForGroupCreation** till värdet **false**.

## <a name="next-steps"></a>Nästa steg

Läs mer om hanteringslösningar här:

- [Skapa hanteringsgrupper för att organisera Azure-resurser](../create.md)
- [Så här ändrar, raderar och hanterar du dina hanteringsgrupper](../manage.md)
