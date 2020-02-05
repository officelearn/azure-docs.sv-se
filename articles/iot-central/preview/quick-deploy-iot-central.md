---
title: Skapa ett Azure IoT Central-program | Microsoft Docs
description: Skapa ett nytt Azure IoT Central-program. Skapa en utvärderings-eller standard program med hjälp av en program-mall.
author: lmasieri
ms.author: lmasieri
ms.date: 12/18/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 1b19909f005fa11b842fccc0497cb49960e32a3b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989743"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Skapa ett Azure IoT Central-program (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här snabb starten visar hur du skapar ett Azure IoT Central-program som innehåller för hands versions funktioner som IoT Plug and Play.

> [!WARNING]
> IoT Plug and Play-funktionerna i Azure IoT Central finns för närvarande i en offentlig för hands version. Använd inte ett IoT-Plug and Play aktiverat IoT Central program för produktions arbets belastningar. För produktions miljöer används ett IoT Central-program som skapats från en aktuell, allmänt tillgänglig program mal len.

## <a name="create-an-application"></a>Skapa ett program

Gå till bygg webbplatsen för [Azure IoT Central](https://aka.ms/iotcentral) . Logga sedan in med ett Microsoft personal-, arbets-eller skol konto.

Du skapar ett nytt program antingen från listan över bransch relevanta IoT Central mallar som hjälper dig att komma igång snabbt eller börja från början med hjälp av mallen för **anpassad app** .

![Sidan skapa en program-Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-templates-pnp.png)

Så här skapar du ett nytt Azure IoT Central-program:

1. Om du vill skapa ett nytt Azure IoT Central-program från en *bransch mall*väljer du en Programmall i listan över tillgängliga mallar under en av branscherna. Du kan också starta från början genom att välja *anpassad app*.
1. Azure IoT Central föreslår automatiskt ett **program namn** baserat på den program mall som du har valt. Du kan använda det här namnet eller ange ett eget vänligt program namn.
1. Azure IoT Central genererar också ett unikt **program-URL** -prefix åt dig, baserat på program namnet. Du använder den här URL: en för att få åtkomst till ditt program. Du är kostnads fri att ändra detta URL-prefix till något mer minnes värt om du vill.

    ![Sidan skapa en program-Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-industry-pnp.png)

    ![Sidan skapa en program-Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-billinginfo-pnp.png)
    > [!NOTE]
    > Om du använder mallen för anpassade appar visas ett List Rute fält för **programmallen** . Härifrån kan du växla mellan förhands granskning och allmänt tillgängliga mallar. Du kan också se andra mallar som har gjorts tillgängliga för din organisation.

1. Välj om du vill skapa det här programmet med en kostnads fri utvärderings version på 7 dagar eller Använd en standard prenumeration.
    - Program som du skapar med **standard** planen debiteras per enhet. du kan välja standard pris avtal för **standard 1** eller **Standard 2** med de två första enheterna som är kostnads fria. Läs mer om kostnads fria priser och standard priser på [sidan med priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Om du skapar ett standard program måste du välja *katalog*, *Azure-prenumeration*och *plats*:
      - *Directory* är den Azure Active Directory (AAD) som du ska använda för att skapa programmet. En Azure AD innehåller användar identiteter, autentiseringsuppgifter och annan organisations information. Om du inte har en Azure AD skapas en åt dig när du skapar en Azure-prenumeration.
      - Med en *Azure-prenumeration* kan du skapa instanser av Azure-tjänster. IoT Central etablerar resurser i din prenumeration. Om du inte har någon Azure-prenumeration kan du skapa en på [Azures registreringssida](https://aka.ms/createazuresubscription). När du har skapat Azure-prenumerationen går du tillbaka till sidan **skapa ett program** . Den nya prenumerationen visas i list rutan för **Azure-prenumerationen** .
      - *Plats* är den [geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill skapa ditt program. Normalt bör du välja den plats som är fysiskt närmast dina enheter för att få optimala prestanda. Azure IoT Central offentlig för hands version är för närvarande tillgänglig i **USA**eller i **Europa**. När du har valt en plats kan du inte flytta programmet till en annan plats senare.
        > [!NOTE]
        > Under den offentliga för hands versionen är de enda tillgängliga platserna för för **hands versions program** **Europa** och **USA**.

1. Granska de allmänna villkoren och välj **skapa** längst ned på sidan.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har skapat du ett IoT Central-program. Här är nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Lägg till en simulerad enhet till ditt IoT Central program](./quick-create-pnp-device.md)
