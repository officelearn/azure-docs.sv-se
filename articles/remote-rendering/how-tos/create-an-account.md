---
title: Skapa ett Azure Remote Rendering-konto
description: Beskriver stegen för att skapa ett konto för Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681303"
---
# <a name="create-an-azure-remote-rendering-account"></a>Skapa ett Azure Remote Rendering-konto

Det här kapitlet vägleder dig genom stegen för att skapa ett konto för **Azure Remote Rendering-tjänsten.** Ett giltigt konto är obligatoriskt för att slutföra någon av snabbstarter eller självstudier.

## <a name="create-an-account"></a>Skapa ett konto

Följande steg behövs för att skapa ett konto för Azure Remote Rendering-tjänsten:

1. Gå till [sidan Förhandsgranskning av mixad verklighet](https://aka.ms/MixedRealityPrivatePreview)
1. Klicka på knappen Skapa en resurs
1. Skriv in "Fjärr rendering" i sökfältet ("Sök på marketplace".
1. Klicka på panelen "Fjärrrendering" i resultatlistan
1. På nästa skärm klickar du på knappen "Skapa". Ett formulär öppnas för att skapa ett nytt fjärrrenderingskonto:
    1. Ange "Resursnamn" till namnet på kontot
    1. Uppdatera "Prenumeration" om det behövs
    1. Ange "Resursgrupp" till en resursgrupp som du väljer
1. När kontot har skapats navigerar du till det och:
    1. På fliken *Översikt* noterar du "Konto-ID"
    1. På fliken *Inställningar > åtkomstnycklar* tecknar du "Primärnyckeln" – det här är kontots hemliga kontonyckel

### <a name="retrieve-the-account-information"></a>Hämta kontoinformationen

Exemplen och självstudierna kräver att du anger konto-ID och en nyckel. Till exempel i **filen arrconfig.json** som används för PowerShell-exempelskripten:

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

Se [listan över tillgängliga regioner](../reference/regions.md) för att fylla i *regionalternativet.*

Värdena **`arrAccountId`** för **`arrAccountKey`** och finns i portalen enligt beskrivningen i följande steg:

* Gå till [Azure Portal](https://www.portal.azure.com)
* Hitta ditt **"Fjärrrenderingskonto"** – det ska finnas i listan **"Senaste resurser".** Du kan också söka efter den i sökfältet högst upp. I så fall kontrollerar du att den prenumeration du vill använda är markerad i standardprenumerationsfiltret (filterikon bredvid sökfältet):

![Abonnemangsfilter](./media/azure-subscription-filter.png)

Om du klickar på ditt konto kommer du till den här skärmen, som visar **konto-ID:et** direkt:

![Id för Azure-konto](./media/azure-account-id.png)

För nyckeln väljer du **Snabbtangenter** på panelen till vänster. På nästa sida visas en primär och en sekundär nyckel:

![Azure-åtkomstnycklar](./media/azure-account-primary-key.png)

Värdet för **`arrAccountKey`** kan antingen vara primär eller sekundär nyckel.

## <a name="link-storage-accounts"></a>Länka lagringskonton

I det här stycket beskrivs hur du länkar lagringskonton till ditt fjärrrenderingskonto. När ett lagringskonto är länkat är det inte nödvändigt att generera en SAS URI varje gång du vill interagera med data i ditt konto, till exempel när du läser in en modell. I stället kan du använda lagringskontonamnen direkt enligt beskrivningen i [avsnittet läsa in en modell](../concepts/models.md#loading-models).

Stegen i den här punkten måste utföras för varje lagringskonto som ska använda den här alternativa åtkomstmetoden. Om du inte har skapat lagringskonton ännu kan du gå igenom respektive steg i [konvertera en modell för rendering snabbstart](../quickstarts/convert-model.md#storage-account-creation).

Nu antas det att du har ett lagringskonto. Navigera till lagringskontot i portalen och gå till fliken **Åtkomstkontroll (IAM)** för det lagringskontot:

![Lagringskonto IAM](./media/azure-storage-account.png)

 Se till att du har ägarbehörigheter över det här lagringskontot för att säkerställa att du kan lägga till rolltilldelningar. Om du inte har åtkomst inaktiveras alternativet **Lägg till en rolltilldelning.**

 Du måste lägga till tre olika roller enligt beskrivningen i nästa steg. Om du inte anger alla tre åtkomstnivåerna kommer det att finnas behörighetsproblem när du försöker komma åt lagringskontot.

 Klicka på knappen **Lägg till** i panelen Lägg till en rolltilldelning för att lägga till den första rollen:

![Lagringskonto IAM](./media/azure-add-role-assignment.png)

* Den första rollen att tilldela är **Ägare** som visas i skärmdumpen ovan. 
* Välj **Fjärrrenderingskonto** i listrutan ***Tilldela åtkomst.**
* Välj din prenumeration och fjärrrenderingskonto i de senaste listrarna.

Upprepa att lägga till nya roller ytterligare två gånger för respektive val från **listrutan Roll:**
* **Lagringskontodeltagare**
* **Storage Blob Data Contributor**

De andra listrarna väljs som i det första steget.

Om du har lagt till alla tre rollerna har ditt Azure Remote Rendering-konto åtkomst till ditt lagringskonto med hjälp av systemtilldelade hanterade tjänstidentiteter.

## <a name="next-steps"></a>Nästa steg

* [Autentisering](authentication.md)
* [Använda Azure Frontend API:er för autentisering](frontend-apis.md)
* [Exempel på PowerShell-skript](../samples/powershell-example-scripts.md)
