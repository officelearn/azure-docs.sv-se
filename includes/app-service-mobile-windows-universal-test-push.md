---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: c86210208b9f747cbef1d9231528fa6cedbdb5d2
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42814521"
---
1. Högerklicka på Windows Store-projektet, klicka på **Set as StartUp Project**, tryck på F5 för att köra Windows Store-app.

    När appen startar är enheten registrerad för push-meddelanden.
2. Stoppa Windows Store-app och upprepa föregående steg för Windows Phone Store-app.

    Både enheter är nu registrerad för att ta emot push-meddelanden.

3. Kör Windows Store-appen igen och ange text i **infoga en TodoItem**, och klicka sedan på **spara**.

    Observera att när infogningen har slutförts, både Windows Store och Windows Phone-appar får ett push-meddelande från WNS. Meddelandet visas på Windows Phone även när appen inte körs.

    ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)
