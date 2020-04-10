---
title: Skapa ett Azure IoT Central-program | Microsoft Docs
description: Skapa ett nytt Azure IoT Central-program. Skapa programmet med hjälp av antingen den kostnadsfria prisplanen eller någon av standardprisplanerna.
author: viv-liu
ms.author: viviali
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 0ed32a4c1272c23c9500b35e05c383eac6dea185
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998953"
---
# <a name="create-an-azure-iot-central-application"></a>Skapa ett Azure IoT Central-program

Den här snabbstarten visar hur du skapar ett Azure IoT Central-program.

## <a name="create-an-application"></a>Skapa ett program

Navigera till Azure [IoT Central](https://aka.ms/iotcentral) Build-webbplatsen. Logga sedan in med ett personligt Microsoft-konto, ett arbets- eller skolkonto.

Du skapar ett nytt program antingen från listan över branschrelevanta IoT Central-mallar som hjälper dig att komma igång snabbt eller börja om från början med hjälp av en **anpassad appmall.** I den här snabbstarten använder du mallen **Anpassat program.**

Så här skapar du ett nytt Azure IoT Central-program från den **anpassade programmallen:**

1. Navigera till sidan **Bygg:**

    ![Skapa din IoT-programsida](media/quick-deploy-iot-central/iotcentralcreate-new-application.png)

1. Välj **Anpassade appar** och kontrollera att mallen Anpassat **program** är markerad.

1. Azure IoT Central föreslår automatiskt ett **programnamn** baserat på den programmall du har valt. Du kan använda det här namnet eller ange ditt eget programnamn.

1. Azure IoT Central genererar också ett unikt **program-URL-prefix** för dig, baserat på programnamnet. Du använder den här URL:en för att komma åt ditt program. Ändra det här URL-prefixet till något mer minnesvärt om du vill.

    ![Azure IoT Central Skapa en programsida](media/quick-deploy-iot-central/iotcentralcreate-custom.png)

    ![Faktureringsinformation för Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png)

    > [!NOTE]
    > Om du väljer **Anpassad app** på föregående sida visas en **listruta för programmall.** Härifrån kan du växla mellan anpassade och äldre mallar. Du kan också se andra mallar som har gjorts tillgängliga för din organisation.

1. Välj att skapa det här programmet med hjälp av 7-dagars kostnadsfri provprisplan eller någon av standardprisplanerna:

    - Program som du skapar med den *kostnadsfria* planen är gratis i sju dagar och har stöd för upp till fem enheter. Du kan konvertera dem för att använda en standardprisplan när som helst innan de upphör att gälla.
    - Program som du skapar med hjälp av en *standardplan* faktureras per enhet, du kan välja antingen **standard 1-** eller **standard 2-prisplan** när de två första enheterna är gratis. Läs mer om de kostnadsfria och standardiserade prisplanerna på [prissidan för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Om du skapar ett program med en standardprisplan måste du välja *din katalog,* *Azure-prenumeration*och *plats:*
        - *Katalog* är Den Azure Active Directory där du skapar ditt program. En Azure Active Directory innehåller användaridentiteter, autentiseringsuppgifter och annan organisationsinformation. Om du inte har en Azure Active Directory skapas en åt dig när du skapar en Azure-prenumeration.
        - Med en *Azure-prenumeration* kan du skapa instanser av Azure-tjänster. IoT Central avsättningar resurser i din prenumeration. Om du inte har en Azure-prenumeration kan du skapa en gratis på [sidan Azure-registrering](https://aka.ms/createazuresubscription). När du har skapat Azure-prenumerationen navigerar du tillbaka till sidan **Nytt program.** Din nya prenumeration visas nu i listrutan **Azure-prenumeration.**
        - *Plats* är den [geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill skapa ditt program. Vanligtvis bör du välja den plats som är fysiskt närmast dina enheter för att få optimal prestanda. När du har valt en plats kan du inte flytta programmet till en annan plats senare.

1. Gå igenom villkoren och välj **Skapa** längst ned på sidan. Efter några minuter är du IoT Central ansökan klar att använda:

    ![Azure IoT Central-program](media/quick-deploy-iot-central/iotcentral-application.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har skapat du ett IoT Central-program. Här är nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Lägga till en simulerad enhet i ditt IoT Central-program](./quick-create-simulated-device.md)
