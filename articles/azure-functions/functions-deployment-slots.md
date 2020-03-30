---
title: Distributionsplatser för Azure Functions
description: Lär dig att skapa och använda distributionsplatser med Azure Functions
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 0e8c93ea6d5c2b525ccbea2af900f100afcc3d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769225"
---
# <a name="azure-functions-deployment-slots"></a>Distributionsplatser för Azure Functions

Azure Functions-distributionsplatser gör att din funktionsapp kan köra olika instanser som kallas "slots". Slots är olika miljöer som exponeras via en allmänt tillgänglig slutpunkt. En appinstans mappas alltid till produktionsplatsen och du kan byta instanser som tilldelats en plats på begäran. Funktionsappar som körs under Apptjänstplanen kan ha flera platser, medan endast en plats är tillåten under förbrukningsplanen.

Följande återspeglar hur funktioner påverkas av att byta platser:

- Trafikomdirigering är sömlös; inga begäranden tas bort på grund av en swap.
- Om en funktion körs under en växling fortsätter körningen och efterföljande utlösare dirigeras till den utbytta appinstansen.

> [!NOTE]
> Slots är för närvarande inte tillgängliga för Linux-förbrukningsplanen.

## <a name="why-use-slots"></a>Varför använda slots?

Det finns ett antal fördelar med att använda distributionsplatser. I följande scenarier beskrivs vanliga användningsområden för platser:

- **Olika miljöer för olika ändamål:** Med hjälp av olika platser ger dig möjlighet att skilja appinstanser innan du byter till produktion eller en mellanlagringsplats.
- **Förvärmning**: Om du distribuerar till en plats i stället för direkt till produktionen kan appen värmas upp innan den går live. Dessutom minskar användning av kortplatser svarstid för HTTP-utlösta arbetsbelastningar. Instanser värms upp före distributionen vilket minskar kallstarten för nyligen distribuerade funktioner.
- **Enkla reserver**: Efter ett byte med produktion har facket med en tidigare iscensatt app nu den tidigare produktionsappen. Om ändringarna som byts ut till produktionsplatsen inte är som du förväntar dig kan du omedelbart återföra bytet för att få tillbaka din "senast fungerande bra instans".

## <a name="swap-operations"></a>Swap-transaktioner

Under en swap anses en plats vara källan och den andra målet. Källplatsen har förekomsten av programmet som tillämpas på målplatsen. Följande steg säkerställer att målplatsen inte drabbas av driftstopp under en växling:

1. **Använd inställningar:** Inställningar från målplatsen tillämpas på alla instanser av källfacket. Produktionsinställningarna tillämpas till exempel på mellanlagringsinstansen. De tillämpade inställningarna omfattar följande kategorier:
    - [Kortplatsspecifika](#manage-settings) appinställningar och anslutningssträngar (om tillämpligt)
    - [Inställningar för kontinuerlig distribution](../app-service/deploy-continuous-deployment.md) (om aktiverat)
    - [Autentiseringsinställningar](../app-service/overview-authentication-authorization.md) för App Service (om aktiverat)

1. **Vänta på omstarter och tillgänglighet:** Växlingen väntar på att varje instans i källfacket ska slutföra omstarten och vara tillgänglig för begäranden. Om någon instans inte startar om återställs alla ändringar i källplatsen och åtgärden stoppas.

1. **Uppdatera routning:** Om alla instanser på källplatsen har värmts upp, slutför de två kortplatserna växlingen genom att växla routningsregler. Efter det här steget har målplatsen (till exempel produktionsplatsen) appen som tidigare har värmts upp i källfacket.

1. **Upprepad åtgärd:** Nu när källplatsen har app för bytesappen tidigare i målplatsen, utför samma åtgärd genom att tillämpa alla inställningar och starta om instanserna för källplatsen.

Tänk på följande punkter:

- När som helst i växlingsåtgärden sker initieringen av de utbytta apparna på källplatsen. Målplatsen förblir online medan källplatsen förbereds, oavsett om bytet lyckas eller misslyckas.

- Om du vill byta en mellanlagringsplats med produktionsplatsen, se till att produktionsplatsen *alltid* är målplatsen. På så sätt påverkar växlingsåtgärden inte din produktionsapp.

- Inställningar som är relaterade till händelsekällor och bindningar måste konfigureras som [inställningar för distributionsplats](#manage-settings) *innan du startar en växling*. Markera dem som "klibbiga" i förväg säkerställer händelser och utgångar riktas till rätt instans.

## <a name="manage-settings"></a>Hantera inställningar

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Skapa en distributionsinställning

Du kan markera inställningar som en distributionsinställning som gör den "klibbig". En klibbig inställning växlar inte med appinstansen.

Om du skapar en distributionsinställning i en plats, se till att skapa samma inställning med ett unikt värde i någon annan plats som är inblandad i en swap. På så sätt förblir inställningens namn konsekvent bland fackplatserna, medan inställningens värde inte ändras. Den här namnkonsekvensen säkerställer att koden inte försöker komma åt en inställning som definieras i en plats men inte en annan.

Så här skapar du en distributionsinställning:

- Navigera till *platser* i funktionsappen
- Klicka på platsplatsens namn
- Under *Plattformsfunktioner > Allmänna inställningar*klickar du på **Konfiguration**
- Klicka på inställningsnamnet som du vill hålla fast vid den aktuella kortplatsen
- Klicka på **kryssrutan Distributionsplats**
- Klicka på **OK**
- När inställningsbladet försvinner klickar du på **Spara** för att behålla ändringarna

![Inställning av distributionsplats](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>Distribution

Slots är tomma när du skapar en plats. Du kan använda någon av de [distributionstekniker som stöds](./functions-deployment-technologies.md) för att distribuera ditt program till en plats.

## <a name="scaling"></a>Skalning

Alla ankomst- och avgångstider skalas till samma antal arbetare som produktionsplatsen.

- För förbrukningsplaner skalas kortplatsen när funktionsappen skalas.
- För App Service-planer skalas appen till ett fast antal arbetare. Slots körs på samma antal arbetare som appplanen.

## <a name="add-a-slot"></a>Lägga till ett fack

Du kan lägga till en plats via [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) eller via portalen. Följande steg visar hur du skapar en ny plats i portalen:

1. Navigera till din funktionsapp och klicka på **plustecknet** bredvid *Slots*.

    ![Lägg till distributionsplats för Azure Functions](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. Ange ett namn i textrutan och tryck på knappen **Skapa.**

    ![Namnge distributionsplats för Azure Functions](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>Byt kortplatser

Du kan byta kortplatser via [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) eller via portalen. Följande steg visar hur du byter platser i portalen:

1. Navigera till funktionsappen
1. Klicka på källfacknamnet som du vill byta
1. Klicka på distributionsplats *för* ![ **Växlingsknappen Byt** Azure Functions på fliken Översikt](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. Verifiera konfigurationsinställningarna för din swap och klicka på **Distributionsplats för Swap** ![Swap-funktioner i Azure Functions](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

Åtgärden kan ta en stund medan växlingsåtgärden körs.

## <a name="roll-back-a-swap"></a>Rulla tillbaka en swap

Om en växling resulterar i ett fel eller om du helt enkelt vill "ångra" en växling kan du återställa till det ursprungliga tillståndet. Om du vill återgå till det förbytta tillståndet gör du en nyswapp för att återföra swappen.

## <a name="remove-a-slot"></a>Ta bort en kortplats

Du kan ta bort en plats via [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) eller via portalen. Följande steg visar hur du tar bort en plats i portalen:

1. Navigera till funktionsappöversikten

1. Klicka på knappen **Ta bort**

    ![Lägg till distributionsplats för Azure Functions](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>Automatisera hantering av kortplats

Med [Hjälp](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)av Azure CLI kan du automatisera följande åtgärder för en plats:

- [skapa](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [delete](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [Swap](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [automatisk växling](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Ändra apptjänstplan

Med en funktionsapp som körs under en App Service-plan har du möjlighet att ändra den underliggande App Service-planen för en plats.

> [!NOTE]
> Du kan inte ändra apptjänstplanen för en plats under förbrukningsplanen.

Gör så här för att ändra apptjänstplanen för en plats:

1. Navigera till en plats

1. Klicka på **Alla inställningar under** *Plattformsfunktioner*

    ![Ändra apptjänstplan](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. Klicka på **App Service plan**

1. Välj en ny apptjänstplan eller skapa en ny plan

1. Klicka på **OK**

    ![Ändra apptjänstplan](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>Begränsningar

Distributionsplatser för Azure Functions har följande begränsningar:

- Antalet platser som är tillgängliga för en app beror på planen. Förbrukningsplanen tillåts endast en distributionsplats. Ytterligare platser är tillgängliga för appar som körs under App Service-planen.
- Om du byter en plats återställs `AzureWebJobsSecretStorageType` tangenter `files`för appar som har en appinställning som är lika med .
- Slots är inte tillgängliga för Linux-förbrukningsplanen.

## <a name="support-levels"></a>Stödnivåer

Det finns två stödnivåer för distributionsplatser:

- **Allmän tillgänglighet (GA)**: Fullt stöd och godkänd för produktionsanvändning.
- **Preview**: Stöds ännu inte, men förväntas nå GA-status i framtiden.

| OS/Hosting-plan           | Stödnivå     |
| ------------------------- | -------------------- |
| Windows-förbrukning       | Allmän tillgänglighet |
| Windows Premium           | Allmän tillgänglighet  |
| Windows Dedikerad         | Allmän tillgänglighet |
| Linux-konsumtion         | Stöd saknas          |
| Linux Premium             | Allmän tillgänglighet  |
| Linux Dedikerad           | Allmän tillgänglighet |

## <a name="next-steps"></a>Nästa steg

- [Distributionstekniker i Azure-funktioner](./functions-deployment-technologies.md)
