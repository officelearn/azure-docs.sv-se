---
title: Bjud in användare till Video Indexer – Azure
titleSuffix: Azure Media Services
description: Den här artikeln visar hur du bjuder in användare till Video Indexer.
services: media-services
author: ReutAmior
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: 4023f8584e7b901a19906235d98982abbbfbaaf8
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92364156"
---
# <a name="quickstart-invite-users-to-video-indexer"></a>Snabb start: Bjud in användare till Video Indexer

Om du vill samar beta med dina kollegor kan du bjuda in dem till ditt Video Indexer-konto. 

> [!NOTE]
> Endast kontots administratör kan lägga till eller ta bort användare.

## <a name="invite-new-users"></a>Bjud in nya användare

1. Logga in på [Video Indexer](https://www.videoindexer.ai/)-webbplatsen. Se till att du är ansluten till ett administratörs konto.
1. Om du är administratör visas knappen **dela konto** i det övre högra hörnet. Klicka på knappen och du kan bjuda in användare. 

   ![Bjud in nya användare](./media/invite-users/share-account.png)
1. Lägg till e-postadresserna till de personer som du vill lägga till i ditt Video Indexer-konto:

    ![Bjud in användare till det här kontot](./media/invite-users/invite-to-account.png)
        
    >[!NOTE]
    > Alla användare som du bjuder in får Läs-och Skriv behörighet för alla videor i ditt konto.
1. De användare som du har bjudit in får ett e-postmeddelande med en länk och kommer att kunna komma åt kontot när de klickar på länken **Anslut till video Indexer** :

    ![Bekräftelse](./media/invite-users/invite-msg.png)

    Användaren måste klicka på länken för att ansluta för att få åtkomst till kontot. 

## <a name="removing-existing-users"></a>Befintliga användare tas bort

Om du vill ta bort användare som har åtkomst till ditt konto kan du klicka på **X** -tecknet bredvid namnet:

![Ta bort användare](./media/invite-users/remove-users.png)

Användare får inget meddelande vid borttagning. När de har tagits bort är användarna inte behöriga att logga in.

## <a name="next-steps"></a>Nästa steg

Du kan nu använda [video Indexer webbplats](video-indexer-view-edit.md) eller [video Indexer Developer-portalen](video-indexer-use-apis.md) för att se insikterna för videon.

## <a name="see-also"></a>Se även

- [Översikt över Video Indexer](video-indexer-overview.md)
- [Så registrerar du dig och laddar upp din första video](video-indexer-get-started.md)
- [Börja använda API: er](video-indexer-use-apis.md)
