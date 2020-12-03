---
title: 'Snabb start: skapa ett Azure avdelningens kontroll-konto i Azure Portal (för hands version)'
description: I den här snabb starten beskrivs hur du skapar ett Azure avdelningens kontroll-konto och konfigurerar behörigheter för att börja använda det.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 10/23/2020
ms.openlocfilehash: 00b504c7bcf51a69d03fb1294de4f52ef35ed163
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555996"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Snabb start: skapa ett Azure avdelningens kontroll-konto i Azure Portal

> [!IMPORTANT]
> Azure avdelningens kontroll är för närvarande en för hands version. Kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är beta, för hands version eller på annat sätt ännu inte har publicerats i allmän tillgänglighet.

I den här snabb starten skapar du ett Azure avdelningens kontroll-konto.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Din egen [Azure Active Directory klient](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

## <a name="configure-your-subscription"></a>Konfigurera din prenumeration

Om det behövs följer du de här stegen för att konfigurera din prenumeration så att Azure-avdelningens kontroll kan köras i din prenumeration:

   1. Sök efter och välj **prenumerationer** i Azure Portal.

   1. I listan över prenumerationer väljer du den prenumeration som du vill använda. Administrativ behörighet för prenumerationen krävs.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Skärm bild som visar hur du väljer en prenumeration i Azure Portal.":::

   1. För din prenumeration väljer du **resurs leverantörer**. I fönstret **resurs leverantörer** söker du efter och registrerar alla tre resurs leverantörer: 
       1. **Microsoft. avdelningens kontroll**
       1. **Microsoft.Storage**
       1. **Microsoft. EventHub** 
      
      Om de inte är registrerade registrerar du dem genom att välja **Registrera**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Skärm bild som visar hur du registrerar Microsoft dot Azure avdelningens kontroll Resource Provider i Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Skapa en instans av Azure avdelningens kontroll-konto

1. Gå till sidan med **avdelningens kontroll-konton** i Azure Portal och välj sedan **Lägg till** för att skapa ett nytt Azure avdelningens kontroll-konto. Alternativt kan du gå till Marketplace-sökning efter **avdelningens kontroll-konton** och välja **skapa**. Observera att du bara kan lägga till ett Azure avdelningens kontroll-konto i taget.

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="Skärm bild som visar hur du skapar en Azure avdelningens kontroll-konto instans i Azure Portal.":::

1. Gör följande på fliken **grundläggande** :
    1. Välj en **resurs grupp**.
    1. Ange ett **avdelningens kontroll-konto namn** för din katalog. Blank steg och symboler är inte tillåtna.
    1. Välj en  **plats** och välj sedan **Nästa: konfiguration**.
1. På fliken **konfiguration** väljer du önskad **plattforms storlek** . tillåtna värden är 4 kapacitets enheter (CU) och 16 cu. Välj **Nästa: Taggar**.
1. På fliken **taggar** kan du också lägga till en eller flera taggar. Taggarna används endast i Azure Portal, inte i Azure-avdelningens kontroll.
1. Välj **granska & skapa** och välj sedan **skapa**. Det tar några minuter att slutföra skapandet. Den nya konto instansen för Azure-avdelningens kontroll visas i listan på sidan med **avdelningens kontroll-konton** .
1. När den nya konto etableringen är klar väljer **du gå till resurs**.

1. Välj **Starta avdelningens kontroll-konto**.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Skärm bild av valet för att starta Azure avdelningens kontroll-konto katalogen.":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>Lägg till ett säkerhets objekt till en data Plans roll

Innan du eller teamet kan börja använda Azure-avdelningens kontroll måste du lägga till ett eller flera säkerhets objekt i en av de fördefinierade data Plans rollerna: **avdelningens kontroll data Reader**, **avdelningens kontroll data curator** eller **avdelningens kontroll Data Source Administrator**. Mer information om Azure avdelningens kontroll Data Catalog-behörigheter finns i [katalog behörigheter](catalog-permissions.md).

Så här lägger du till ett säkerhets objekt i rollen **avdelningens kontroll data curator** data plan i ett Azure avdelningens kontroll-konto:

1. Gå till sidan med [**avdelningens kontroll-konton**](https://aka.ms/purviewportal) i Azure Portal.

1. Välj det Azure avdelningens kontroll-konto som du vill ändra.

1. På sidan **avdelningens kontroll-konto** väljer du fliken **åtkomst kontroll (IAM)**

1. Klicka på **+ Lägg till**

Om du klickar på Lägg till visas två val som visar både markerade (inaktiverade). det innebär att du inte har rätt behörighet för att lägga till någon i en data Plans roll på Azure avdelningens kontroll-kontot. Du måste hitta en ägare, användar åtkomst administratör eller någon annan med roll tilldelnings utfärdaren på ditt Azure avdelningens kontroll-konto. Du kan söka efter rätt personer genom att välja fliken **roll tilldelningar** och sedan rulla nedåt för att leta efter ägare eller användar åtkomst administratör och kontakta dessa personer.

1. Välj **Lägg till rolltilldelning**.

1. För roll typen i **avdelningens kontroll data curator roll** eller **avdelningens kontroll rollen administratör för data källa** beroende på vad tjänstens huvud namn ska användas för (se [katalog behörigheter](catalog-permissions.md) för mer information).

1. För **tilldela behörighet att** lämna kvar standard-, **användar-, grupp-eller tjänstens huvud namn**.

1. För **väljer** du ange namnet på användaren, Azure Active Directory grupp eller tjänstens huvud namn som du vill tilldela och klickar sedan på namnet i resultat fönstret.

1. Klicka på **Spara**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver det här Azure avdelningens kontroll-kontot tar du bort det med följande steg:

1. Gå till sidan med **avdelningens kontroll-konton** i Azure Portal.

2. Välj det Azure avdelningens kontroll-konto som du skapade i början av den här snabb starten. Välj **ta bort**, ange namnet på kontot och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure avdelningens kontroll-konto.

Gå vidare till nästa artikel om du vill lära dig hur du ger användare åtkomst till ditt Azure avdelningens kontroll-konto. 

> [!div class="nextstepaction"]
> [Lägg till användare till ditt Azure avdelningens kontroll-konto](catalog-permissions.md)
