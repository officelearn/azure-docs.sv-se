---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 70e0d766fe3c9669912ce9f13f729fb1c757a26f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140512"
---
1. Högerklicka på Windows Store-projektet, klicka på **Set as StartUp Project**, tryck på F5 för att köra Windows Store-app.

    När appen startar är enheten registrerad för push-meddelanden.
2. Stoppa Windows Store-app och upprepa föregående steg för Windows Phone Store-app.

    Både enheter är nu registrerad för att ta emot push-meddelanden.

3. Kör Windows Store-appen igen och ange text i **infoga en TodoItem**, och klicka sedan på **spara**.

    Observera att när infogningen har slutförts, både Windows Store och Windows Phone-appar får ett push-meddelande från WNS. Meddelandet visas på Windows Phone även när appen inte körs.

    ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)
