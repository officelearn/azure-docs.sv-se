---
title: Skapa en Azure blockchain service-medlem – Azure Portal
description: Skapa en Azure blockchain service-medlem för ett blockchain-konsortium med hjälp av Azure Portal.
ms.date: 03/12/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 3c468633a193d78fb1c017a756ee372c6feefb12
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203669"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Snabb start: skapa en Azure blockchain-tjänst blockchain-medlem med hjälp av Azure Portal

I den här snabb starten distribuerar du en ny blockchain-medlem och konsortiet i Azure blockchain-tjänsten med hjälp av Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Skapa en blockchain-medlem

En Azure blockchain-tjänst medlem är en blockchain-nod i ett privat konsortium blockchain-nätverk. När du konfigurerar en medlem kan du skapa eller ansluta till ett konsortiums nätverk. Du behöver minst en medlem för ett konsortiums nätverk. Antalet blockchain-medlemmar som krävs av deltagarna beror på ditt scenario. Konsortiet deltagare kan ha en eller flera blockchain-medlemmar eller de kan dela medlemmar med andra deltagare. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md).

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. Välj **Blockchain** > **Azure blockchain service (för hands version)** .

    ![Skapa tjänst](./media/create-member/create-member.png)

    Inställning | Beskrivning
    --------|------------
    Prenumeration | Välj den Azure-prenumeration som du vill använda för din tjänst. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp | Skapa ett nytt resurs grupp namn eller Välj en befintlig från din prenumeration.
    Region | Välj en region för att skapa medlemmen. Alla medlemmar i konsortiet måste finnas på samma plats.
    Protokoll | För närvarande stöder för hands versionen av Azure blockchain service protokollet kvorum.
    Konsortium | Ange ett unikt namn för ett nytt konsortium. Om du ansluter till ett konsortium via en inbjudan väljer du det konsortium som du ansluter till. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md).
    Namn | Välj ett unikt namn för Azure blockchain service-medlemmen. Blockchain-medlems namnet får bara innehålla gemena bokstäver och siffror. Det första tecknet måste vara en bokstav. Värdet måste vara mellan 2 och 20 tecken långt.
    Konto lösen ord för medlem | Medlems kontots lösen ord används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Du använder medlems kontot och medlems kontots lösen ord för hantering av konsortier.
    Priser | Nodens konfiguration och kostnad för den nya tjänsten. Välj länken **ändra** om du vill välja mellan nivåerna **standard** och **Basic** . Använd *Basic* -nivån för utveckling, testning och bevis på koncept. Använd *standard* nivån för distributioner av produktions nivåer.
    Node-lösenord | Lösen ordet för medlemmens standard transaktions nod. Använd lösen ordet för grundläggande autentisering vid anslutning till blockchain-medlemmens offentliga standard transaktions nod.

1. Välj **Granska + skapa** för att validera inställningarna. Välj **skapa** för att etablera tjänsten. Etableringen tar cirka 10 minuter.
1. Välj **meddelanden** i verktygsfältet för att övervaka distributions processen.
1. Efter distributionen navigerar du till din blockchain-medlem.

Välj **Översikt**. du kan visa grundläggande information om tjänsten inklusive RootContract-adress och medlems konto.

![Översikt över blockchain-medlem](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda den medlem som du skapade för nästa snabb start eller självstudier. När de inte längre behövs kan du ta bort resurserna genom att ta bort `myResourceGroup` resurs grupp som du skapade för snabb starten.

Ta bort resurs gruppen:

1. I Azure Portal navigerar du till **resurs grupp** i det vänstra navigerings fönstret och väljer den resurs grupp som du vill ta bort.
2. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resurs gruppens namn och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en Azure blockchain service-medlem och ett nytt konsortium. Prova nästa snabb start för att använda Azure blockchain Development Kit för Ethereum för att ansluta till en Azure blockchain-tjänstemedlem.

> [!div class="nextstepaction"]
> [Använd Visual Studio Code för att ansluta till Azure blockchain-tjänsten](connect-vscode.md)
