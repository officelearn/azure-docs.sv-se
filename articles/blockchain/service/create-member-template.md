---
title: Skapa en Azure Blockchain Service-medlem med hjälp av Azure Resource Manager-mall
description: Lär dig hur du skapar en Azure Blockchain Service-medlem med hjälp av Azure Resource Manager-mallen.
services: azure-resource-manager
author: PatAltimore
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: patricka
ms.date: 04/22/2020
ms.openlocfilehash: 8fd5a1775a6a217ce9dd8c80439dd2c54f9cd727
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087599"
---
# <a name="create-an-azure-blockchain-service-member-using-an-azure-resource-manager-template"></a>Skapa en Azure Blockchain Service-medlem med en Azure Resource Manager-mall

I den här snabbstarten distribuerar du en ny blockchain-medlem och ett konsortium i Azure Blockchain-tjänsten med hjälp av en Azure Resource Manager-mall.

En Azure Blockchain Service-medlem är en blockchain-nod i ett privat konsortium blockchain-nätverk. När du etablerar en medlem kan du skapa eller gå med i ett konsortiumnätverk. Du behöver minst en medlem för ett konsortiumnätverk. Antalet blockchain-medlemmar som deltagarna behöver beror på ditt scenario. Konsortiedeltagare kan ha en eller flera blockchain-medlemmar eller så kan de dela medlemmar med andra deltagare. Mer information om konsortier finns i [Azure Blockchain Service consortium](consortium.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Inga.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/201-blockchain-asaservice/).

[!code-json[<Azure Resource Manager template create blockchain member>](~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json)]

Azure-resurser som definierats i mallen:

* [**Microsoft.Blockchain/blockchainMedlem**](https://docs.microsoft.com/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande länk för att logga in på Azure och öppna en mall.

    [![Distribuera till Azure](./media/create-member-template/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Ange inställningarna för Azure Blockchain Service-medlemmen.

    Inställning | Beskrivning
    --------|------------
    Prenumeration | Välj den Azure-prenumeration som du vill använda för din tjänst. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp | Skapa ett nytt resursgruppsnamn eller välj ett befintligt namn från din prenumeration.
    Location | Välj en plats för att skapa medlemmen. Alla medlemmar i konsortiet måste vara på samma plats. Tillgängliga platser för utplaceringen är *westeurope, eastus, southeastasia, westeurope, northeurope, westus2*och *japaneast*.
    Medlemsnamn | Välj ett unikt namn för Azure Blockchain Service-medlemmen. Blockchain-medlemsnamnet kan bara innehålla gemener och siffror. Det första tecknet måste vara en bokstav. Värdet måste vara mellan 2 och 20 tecken långt.
    Konsortiets namn | Ange ett unikt namn. Mer information om konsortier finns i [Azure Blockchain Service consortium](consortium.md).
    Medlemslösenord | Medlemskontots lösenord används för att kryptera den privata nyckeln för Ethereum-kontot som skapas för din medlem. Du använder medlemskontot och medlemskontots lösenord för konsortiehantering.
    Sku-nivå | Prisnivån för din nya tjänst. Välj mellan **standard-** och basic-nivåer. **Standard** Använd *basic-nivån* för utveckling, testning och bevis på begrepp. Använd *standardnivån* för distributioner av produktionsresultat. Du bör också använda *standardnivån* om du använder Blockchain Data Manager eller skickar en stor mängd privata transaktioner. Det går inte att ändra prisnivån mellan grundläggande och standard när medlemmen har skapats.
    Sku namn | Nodkonfigurationen och kostnaden för den nya tjänsten.

1. Välj **Inköp om** du vill distribuera mallen.

  Azure-portalen används här för att distribuera mallen. Du kan också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda blockchain-medlemmen som du skapade för nästa snabbstart eller självstudiekurs. När du inte längre behövs kan du ta bort resurserna genom att ta bort resursgruppen som du skapade för snabbstarten.

Så här tar du bort resursgruppen:

1. I Azure-portalen navigerar du till **resursgruppen** i det vänstra navigeringsfönstret och väljer den resursgrupp som du vill ta bort.
2. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resursgruppsnamnet och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat en Azure Blockchain Service-medlem och ett nytt konsortium. Prova nästa snabbstart för att använda Azure Blockchain Development Kit för Ethereum för att ansluta till en Azure Blockchain Service-medlem.

> [!div class="nextstepaction"]
> [Använda Visual Studio-kod för att ansluta till Azure Blockchain Service](connect-vscode.md)