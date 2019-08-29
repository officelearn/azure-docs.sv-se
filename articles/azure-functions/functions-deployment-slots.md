---
title: Azure Functions distributions platser
description: Lär dig att skapa och använda distributions fack med Azure Functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, Functions
ms.service: azure-functions
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 50337745b008cdd38dd860a0329e44ee712e7acd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085672"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions distributions platser

Med Azure Functions distributions platser kan din Function-app köra olika instanser som kallas "platser". Platser är olika miljöer som exponeras via en offentligt tillgänglig slut punkt. En app-instans är alltid mappad till produktions platsen och du kan byta instanser som har tilldelats till en plats på begäran. Function-appar som körs under App Service-planen kan ha flera platser, medan endast en plats tillåts under konsumtion.

Följande visar hur funktioner påverkas av växlings platser:

- Trafik omdirigering är sömlös; inga förfrågningar ignoreras på grund av en växling.
- Om en funktion körs under växlingen fortsätter körningen och efterföljande utlösare dirigeras till den utbytta App-instansen.

> [!NOTE]
> Det finns inga tillgängliga platser för Linux-förbruknings planen.

## <a name="why-use-slots"></a>Varför ska jag använda platser?

Det finns ett antal fördelar med att använda distributions platser. I följande scenarier beskrivs vanliga användnings områden för fack:

- **Olika miljöer för olika syfte**: Genom att använda olika platser får du möjlighet att särskilja App-instanser innan du växlar till produktion eller mellanlagrings plats.
- Förvärmar: Att distribuera till en plats i stället för direkt till produktion gör att appen kan värmas innan Live. Dessutom minskar svars tiden för HTTP-utlösta arbets belastningar med hjälp av platser. Instanser värms upp före distributionen, vilket minskar kall starten för nyligen distribuerade funktioner.
- **Enkel återgång**: Efter en växling med produktion har platsen med en tidigare mellanlagrad app nu den tidigare produktions appen. Om ändringarna som utbyts till produktions platsen inte är som du förväntar dig, kan du omedelbart omvända växlingen för att få din "senast fungerande instans" tillbaka.

## <a name="swap-operations"></a>Växlings åtgärder

Under en växling anses en plats vara källan och den andra målet. Käll platsen har instansen av programmet som används på mål platsen. Följande steg säkerställer att mål platsen inte upplever drift stopp under en växling:

1. **Tillämpa inställningar:** Inställningar från mål platsen tillämpas på alla instanser av käll platsen. Produktions inställningarna tillämpas till exempel på mellanlagringsplatsen. De tillämpade inställningarna omfattar följande kategorier:
    - [](#manage-settings) Platsspecifika appinställningar och anslutnings strängar (om tillämpligt)
    - Inställningar för [kontinuerlig distribution](../app-service/deploy-continuous-deployment.md) (om aktive rad)
    - [App Service autentiseringsinställningar](../app-service/overview-authentication-authorization.md) (om den är aktive rad)

1. **Vänta tills omstarter och tillgänglighet:** Växlingen väntar på att varje instans på käll platsen ska kunna slutföra sin omstart och är tillgänglig för förfrågningar. Om någon instans inte kan starta om återställer växlings åtgärden alla ändringar till käll platsen och stoppar åtgärden.

1. **Uppdaterings dirigering:** Om alla instanser på käll platsen har förvärmts korrekt, slutför de två platserna växlingen genom att växla regler för routning. Efter det här steget har mål platsen (till exempel produktions platsen) den app som tidigare har förvärmts på käll platsen.

1. **Upprepa åtgärd:** Nu när käll platsen har för hands växlings appen tidigare på mål platsen, utför du samma åtgärd genom att tillämpa alla inställningar och starta om instanserna för käll platsen.

Tänk på följande:

- Vid alla tidpunkter på växlings åtgärden sker initieringen av de växlade apparna på käll platsen. Mål platsen är online medan käll platsen förbereds, om växlingen lyckas eller inte.

- Om du vill byta mellan en mellanlagringsplats med produktions platsen kontrollerar du att produktions platsen *alltid* är mål platsen. På så sätt kommer växlings åtgärden inte att påverka din produktions program.

- Inställningar som rör händelse källor och bindningar måste konfigureras som [distributions plats inställningar](#manage-settings) *innan du påbörjar en växling*. Genom att markera dem som "trög" i förväg säkerställer du att händelser och utdata dirigeras till rätt instans.

## <a name="manage-settings"></a>Hantera inställningar

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Skapa en distributions inställning

Du kan markera inställningar som en distributions inställning som gör den "trög". En inställning för tröghet växlar inte med App-instansen.

Om du skapar en distributions inställning på en plats, se till att skapa samma inställning med ett unikt värde på andra platser som ingår i en växling. På så sätt, medan värdet för en inställning inte ändras, förblir inställnings namnen konsekventa mellan platser. Detta namn konsekvens säkerställer att koden inte försöker komma åt en inställning som har definierats på en plats, men inte en annan.

Använd följande steg för att skapa en distributions inställning:

- Navigera till *platser* i Function-appen
- Klicka på plats namnet
- Under *plattforms funktioner > allmänna inställningar*klickar du på **konfiguration**
- Klicka på det inställnings namn som du vill använda för den aktuella platsen
- Klicka i kryss rutan **distributions plats inställning**
- Klicka på **OK**
- När bladet har angetts försvinner klickar du på **Spara** för att behålla ändringarna

![Distributions plats inställning](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>Distribution

Platser är tomma när du skapar en plats. Du kan använda någon av de [distributions tekniker som stöds](./functions-deployment-technologies.md) för att distribuera programmet till en plats.

## <a name="scaling"></a>Skalning

Alla platser skalas till samma antal anställda som produktions platsen.

- För förbruknings planer skalas-facket som Function-appen skalar.
- För App Service planer skalas appen till ett fast antal arbetare. Platser körs på samma antal arbets tagare som app-planen.

## <a name="add-a-slot"></a>Lägg till en plats

Du kan lägga till en plats via [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) eller via portalen. Följande steg visar hur du skapar en ny plats i portalen:

1. Navigera till din Function-app och klicka på **plus tecknet** bredvid *platser*.

    ![Lägg till Azure Functions distributions plats](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. Ange ett namn i text rutan och tryck på knappen **skapa** .

    ![Namn Azure Functions distributions fack](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>Växla platser

Du kan växla mellan platser via [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) eller via portalen. Följande steg visar hur du byter platser i portalen:

1. Navigera till Function-appen
1. Klicka på det käll plats namn som du vill byta
1. På fliken *Översikt* klickar du på växlings knappen ![växling Azure Functions distributions plats](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. Verifiera konfigurations inställningarna för din växling och klicka ![på växlings växling Azure Functions distributions plats](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

Åtgärden kan ta en stund medan växlings åtgärden körs.

## <a name="roll-back-a-swap"></a>Återställa en växling

Om en växling resulterar i ett fel eller om du bara vill "ångra" en växling kan du återställa den ursprungliga statusen. Om du vill återgå till förväxlat tillstånd, gör du en annan växling för att omvända växlingen.

## <a name="remove-a-slot"></a>Ta bort en plats

Du kan ta bort en plats via [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) eller via portalen. Följande steg visar hur du tar bort en plats i portalen:

1. Navigera till Function-appens översikt

1. Klicka på knappen **ta bort**

    ![Lägg till Azure Functions distributions plats](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>Automatisera plats hantering

Med [Azure CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)kan du automatisera följande åtgärder för en plats:

- [skapa](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [ta bort](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [skärm](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [automatisk växling](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Ändra App Service-plan

Med en Function-app som körs under en App Service plan har du möjlighet att ändra den underliggande App Service-planen för en plats.

> [!NOTE]
> Du kan inte ändra en platss App Service plan under förbruknings planen.

Använd följande steg för att ändra en platss App Service-plan:

1. Navigera till en plats

1. Under *plattforms funktioner*klickar du på **alla inställningar**

    ![Ändra App Service-plan](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. Klicka på **App Service plan**

1. Välj en ny App Service plan eller skapa en ny plan

1. Klicka på **OK**

    ![Ändra App Service-plan](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>Begränsningar

Azure Functions distributions fack har följande begränsningar:

- Antalet tillgängliga fack för en app beror på planen. Förbruknings planen tillåts bara en distributions plats. Det finns ytterligare platser för appar som körs under App Service plan.
- När en plats byts ut återställs nycklar för appar som har `AzureWebJobsSecretStorageType` en app-inställning `files`som är lika med.
- Det finns inga tillgängliga platser för Linux-förbruknings planen.

## <a name="support-levels"></a>Supportnivåer

Det finns två nivåer av stöd för distributions platser:

- **Allmän tillgänglighet (ga)** : Fullständigt stöd och godkänts för produktions användning.
- För **hands version**: Stöds inte ännu, men förväntas komma att uppnå GA-status i framtiden.

| Operativ system/värd plan           | Support nivå     |
| ------------------------- | -------------------- |
| Windows-förbrukning       | Allmän tillgänglighet |
| Windows Premium (för hands version) | Förhandsversion              |
| Windows-dedikerad         | Allmän tillgänglighet |
| Linux-förbrukning         | Stöds inte          |
| Linux Premium (för hands version)   | Förhandsversion              |
| Linux-dedikerad           | Allmän tillgänglighet |

## <a name="next-steps"></a>Nästa steg

- [Distributions tekniker i Azure Functions](./functions-deployment-technologies.md)
