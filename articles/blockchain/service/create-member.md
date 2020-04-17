---
title: Skapa en Azure Blockchain-tjänstmedlem - Azure-portal
description: Skapa en Azure Blockchain Service-medlem för ett blockchain-konsortium med Hjälp av Azure-portalen.
ms.date: 03/30/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 7d29382cdedf7e35d186c73c9d32547e10fb10d6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460281"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Snabbstart: Skapa en Blockchain-tjänstmedlem för Azure Blockchain-tjänst med Azure-portalen

I den här snabbstarten distribuerar du en ny blockchain-medlem och konsortium i Azure Blockchain Service med Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Skapa en blockchain-medlem

En Azure Blockchain Service-medlem är en blockchain-nod i ett privat konsortium blockchain-nätverk. När du etablerar en medlem kan du skapa eller gå med i ett konsortiumnätverk. Du behöver minst en medlem för ett konsortiumnätverk. Antalet blockchain-medlemmar som deltagarna behöver beror på ditt scenario. Konsortiedeltagare kan ha en eller flera blockchain-medlemmar eller så kan de dela medlemmar med andra deltagare. Mer information om konsortier finns i [Azure Blockchain Service consortium](consortium.md).

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. Välj **Blockchain** > **Azure Blockchain Service (förhandsversion)**.

    ![Skapa tjänst](./media/create-member/create-member.png)

    Inställning | Beskrivning
    --------|------------
    Prenumeration | Välj den Azure-prenumeration som du vill använda för din tjänst. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp | Skapa ett nytt resursgruppsnamn eller välj ett befintligt namn från din prenumeration.
    Region | Välj en region för att skapa medlemmen. Alla medlemmar i konsortiet måste vara på samma plats.
    Protokoll | För närvarande stöder Azure Blockchain Service Preview kvorumprotokollet.
    Konsortium | Ange ett unikt namn för ett nytt konsortium. Om du går med i ett konsortium via en inbjudan väljer du det konsortium du ansluter dig till. Mer information om konsortier finns i [Azure Blockchain Service consortium](consortium.md).
    Namn | Välj ett unikt namn för Azure Blockchain Service-medlemmen. Blockchain-medlemsnamnet kan bara innehålla gemener och siffror. Det första tecknet måste vara en bokstav. Värdet måste vara mellan 2 och 20 tecken långt.
    Lösenord för medlemskonto | Medlemskontots lösenord används för att kryptera den privata nyckeln för Ethereum-kontot som skapas för din medlem. Du använder medlemskontot och medlemskontots lösenord för konsortiehantering.
    Prissättning | Nodkonfigurationen och kostnaden för den nya tjänsten. Välj länken **Ändra** för att välja mellan **standard-** och **basic-nivåer.** Använd *basic-nivån* för utveckling, testning och bevis på begrepp. Använd *standardnivån* för distributioner av produktionsresultat. Du bör också använda *standardnivån* om du använder Blockchain Data Manager eller skickar en stor mängd privata transaktioner. Det går inte att ändra prisnivån mellan grundläggande och standard när medlemmen har skapats.
    Nodlösenord | Lösenordet för medlemmens standardtransaktionsnod. Använd lösenordet för grundläggande autentisering när du ansluter till blockchain-medlemmens offentliga slutpunkt för standardtransaktionsnod.

1. Välj **Granska + skapa** för att validera dina inställningar. Välj **Skapa** för att etablera tjänsten. Etablering tar cirka 10 minuter.
1. Välj **Meddelanden** i verktygsfältet för att övervaka distributionsprocessen.
1. Efter distributionen navigerar du till din blockchain-medlem.

Välj **Översikt**kan du visa grundläggande information om din tjänst, inklusive RootContract-adressen och medlemskontot.

![Översikt över Blockchain-medlemmar](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda medlemmen du skapade för nästa snabbstart eller självstudiekurs. När du inte längre behövs kan du `myResourceGroup` ta bort resurserna genom att ta bort resursgruppen som du skapade för snabbstarten.

Så här tar du bort resursgruppen:

1. I Azure-portalen navigerar du till **resursgruppen** i det vänstra navigeringsfönstret och väljer den resursgrupp som du vill ta bort.
2. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resursgruppsnamnet och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat en Azure Blockchain Service-medlem och ett nytt konsortium. Prova nästa snabbstart för att använda Azure Blockchain Development Kit för Ethereum för att ansluta till en Azure Blockchain Service-medlem.

> [!div class="nextstepaction"]
> [Använda Visual Studio-kod för att ansluta till Azure Blockchain Service](connect-vscode.md)
