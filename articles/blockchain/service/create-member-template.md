---
title: Skapa en Azure blockchain service-medlem med hjälp av Azure Resource Manager mall
description: Lär dig hur du skapar en Azure blockchain service-medlem med hjälp av Azure Resource Manager mall.
services: azure-resource-manager
author: PatAltimore
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: patricka
ms.date: 04/22/2020
ms.openlocfilehash: db5cabd82ebfed3b1de7659e4b9450ccee0ff001
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115303"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-azure-resource-manager-template"></a>Snabb start: skapa en Azure blockchain service-medlem med en Azure Resource Manager-mall

I den här snabb starten distribuerar du en ny blockchain-medlem och konsortiet i Azure blockchain-tjänsten med hjälp av en Azure Resource Manager-mall.

En Azure blockchain-tjänst medlem är en blockchain-nod i ett privat konsortium blockchain-nätverk. När du konfigurerar en medlem kan du skapa eller ansluta till ett konsortiums nätverk. Du behöver minst en medlem för ett konsortiums nätverk. Antalet blockchain-medlemmar som krävs av deltagarna beror på ditt scenario. Konsortiet deltagare kan ha en eller flera blockchain-medlemmar eller de kan dela medlemmar med andra deltagare. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Inga.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/201-blockchain-asaservice/).

[!code-json[<Azure Resource Manager template create blockchain member>](~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json)]

Azure-resurser som definierats i mallen:

* [**Microsoft. blockchain/blockchainMembers**](https://docs.microsoft.com/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande länk för att logga in på Azure och öppna en mall.

    [![Distribuera till Azure](./media/create-member-template/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Ange inställningarna för Azure blockchain service-medlemmen.

    Inställning | Beskrivning
    --------|------------
    Prenumeration | Välj den Azure-prenumeration som du vill använda för din tjänst. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp | Skapa ett nytt resurs grupp namn eller Välj en befintlig från din prenumeration.
    Location | Välj en plats där du vill skapa medlemmen. Alla medlemmar i konsortiet måste finnas på samma plats. Tillgängliga platser för distributionen är *westeurope, öster, Sydostasien, westeurope, europanorra, westus2*och *japanöstra*.
    Medlemsnamn | Välj ett unikt namn för Azure blockchain service-medlemmen. Blockchain-medlems namnet får bara innehålla gemena bokstäver och siffror. Det första tecknet måste vara en bokstav. Värdet måste vara mellan 2 och 20 tecken långt.
    Namn på konsortium | Ange ett unikt namn. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md).
    Medlems lösen ord | Medlems kontots lösen ord används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Du använder medlems kontot och medlems kontots lösen ord för hantering av konsortier.
    SKU-nivå | Pris nivån för den nya tjänsten. Välj mellan **standard** -och **Basic** -nivåer. Använd *Basic* -nivån för utveckling, testning och bevis på koncept. Använd *standard* nivån för distributioner av produktions nivåer. Du bör också använda *standard* nivån om du använder blockchain Data Manager eller när du skickar en stor mängd privata transaktioner. Det finns inte stöd för att ändra pris nivån mellan Basic och standard när medlems skapande har skapats.
    SKU-namn | Nodens konfiguration och kostnad för den nya tjänsten.

1. Välj **köp** för att distribuera mallen.

  Azure Portal används här för att distribuera mallen. Du kan också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda den blockchain-medlem som du skapade för nästa snabb start eller självstudier. När de inte längre behövs kan du ta bort resurserna genom att ta bort resurs gruppen som du skapade för snabb starten.

Så här tar du bort resursgruppen:

1. I Azure Portal navigerar du till **resurs grupp** i det vänstra navigerings fönstret och väljer den resurs grupp som du vill ta bort.
2. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resurs gruppens namn och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en Azure blockchain service-medlem och ett nytt konsortium. Prova nästa snabb start för att använda Azure blockchain Development Kit för Ethereum för att ansluta till en Azure blockchain-tjänstemedlem.

> [!div class="nextstepaction"]
> [Använd Visual Studio Code för att ansluta till Azure blockchain-tjänsten](connect-vscode.md)