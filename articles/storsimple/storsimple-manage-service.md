---
title: "Distribuera StorSimple Manager-tjänsten | Microsoft Docs"
description: "Beskriver hur du skapar och tar bort StorSimple Manager-tjänsten i den klassiska Azure-portalen och beskriver hur du hanterar nyckeln för tjänstregistrering."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: bc1d5650-275c-42ed-bc77-cdb596f85943
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/14/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ba3637a3a8b15b45c16bf5a00c1f4225bcfc5af8
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="deploy-the-storsimple-manager-service-in-the-azure-classic-portal"></a>Distribuera StorSimple Manager-tjänsten i den klassiska Azure-portalen

## <a name="overview"></a>Översikt
StorSimple Manager-tjänsten körs i Microsoft Azure och ansluter till flera StorSimple-enheter. När du har skapat tjänsten kan du använda den för att hantera enheter från den klassiska portalen för Microsoft Azure körs i en webbläsare. På så sätt kan du övervaka alla enheter som är anslutna till StorSimple Manager-tjänsten från en enda central plats, vilket minimerar administrativa belastningen.

StorSimple Manager denna sida visar alla StorSimple Manager-tjänster som du kan använda för att hantera din StorSimple-lagringsenheter. För varje StorSimple Manager-tjänst visas följande information på sidan StorSimple Manager:

* **Namnet** – det namn som har tilldelats din StorSimple Manager-tjänsten när den skapades. **Tjänstens namn kan inte ändras efter att tjänsten har skapats. Detta gäller även för andra entiteter, till exempel enheter, volymer, volymbehållare och principer för säkerhetskopiering som inte kan i den klassiska Azure-portalen.**
* **Status för** – status för tjänsten, som kan vara **Active**, **skapa**, eller **Online**.
* **Plats** – den geografiska plats där StorSimple-enheten ska distribueras.
* **Prenumerationen** – faktureringsadministratör prenumerationen som är associerad med din tjänst.

Vanliga uppgifter som kan utföras via sidan StorSimple Manager är:

* Skapa en tjänst
* Ta bort en tjänst
* Hämta nyckel för tjänstregistrering
* Återskapa nyckeln för tjänstregistrering

Den här självstudiekursen beskriver hur du utför åtgärderna.

## <a name="create-a-service"></a>Skapa en tjänst
Använd den **Snabbregistrering** alternativet för att skapa en StorSimple Manager-tjänsten om du vill distribuera StorSimple-enheten. Om du vill skapa en tjänst måste du ha:

* En prenumeration med ett Enterprise-avtal
* Ett aktivt Microsoft Azure storage-konto
* Faktureringsinformation som används för hantering

Du kan också välja att generera en standardkontot för lagring när du skapar tjänsten.

En enskild tjänst kan hantera flera enheter. Men en enhet kan sträcka sig över flera tjänster. Stora företag kan ha flera instanser av tjänsten ska fungera med olika prenumerationer, organisationer eller även distribution platser. Observera att du behöver separata instanser av StorSimple Manager-tjänsten för att hantera StorSimple 8000-serien enheter och virtuella StorSimple-matriser.

> [!IMPORTANT] 
> Om du har en oanvända tjänst skapas (ingen enhet åtgärder som utförts på denna resurs) innan augusti 2016 kan inte hanteras via Azure-portalen eller klassiska Azure-portalen. Vi rekommenderar att du skapar en ny tjänst i Azure-portalen.

Utför följande steg för att skapa en tjänst.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

## <a name="delete-a-service"></a>Ta bort en tjänst
Innan du tar bort en tjänst måste du kontrollera att inga anslutna enheter använder den. Om tjänsten inte används, inaktivera anslutna enheter. Åtgärden inaktivera sever anslutningen mellan enheten och tjänsten, men behålla enhetsdata i molnet.

> [!IMPORTANT] 
> När en tjänst har tagits bort, kan åtgärden inte ångras. Alla enheter som använder tjänsten måste vara fabriksåterställning innan den kan användas med en annan tjänst. I det här scenariot går lokala data på enheten, samt konfigurationen, förlorade.

Utför följande steg för att ta bort en tjänst.

### <a name="to-delete-a-service"></a>Ta bort en tjänst
1. På den **StorSimple Manager-tjänsten** markerar du den tjänst som du vill ta bort.
2. Klicka på **ta bort** längst ned på sidan.
3. Klicka på **Ja** i meddelande om bekräftelse. Det kan ta några minuter för tjänsten som ska tas bort.

## <a name="get-the-service-registration-key"></a>Hämta nyckel för tjänstregistrering
När du har skapat en tjänst måste registrera din StorSimple-enhet med tjänsten. Om du vill registrera din första StorSimple-enhet, måste nyckeln för tjänstregistrering. Om du vill registrera ytterligare enheter med en befintlig StorSimple-tjänst, måste både Registreringsnyckeln och den krypteringsnyckel för tjänstdata (som genereras på den första enheten under registreringen). Läs mer om krypteringsnyckeln för tjänstdata [StorSimple-säkerhet](storsimple-security.md). Du kan hämta nyckel för tjänstregistrering genom att öppna **registreringsnyckel** på den **Services** sidan.

Utför följande steg för att hämta nyckel för tjänstregistrering.

[!INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

Behåll nyckel för tjänstregistrering på en säker plats. Du behöver den här nyckeln, samt krypteringsnyckeln för tjänstdata, att registrera ytterligare enheter med den här tjänsten. När du har fått nyckeln för tjänstregistrering, behöver du konfigurera din enhet via Windows PowerShell för StorSimple-gränssnittet.

Mer information om hur du använder den här nyckeln för tjänstregistrering finns [steg3: konfigurera och registrera enheten via Windows PowerShell för StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Återskapa nyckeln för tjänstregistrering
Du måste återskapa en nyckel för tjänstregistrering om du behöver utföra viktiga rotation eller om listan över tjänstadministratörer har ändrats. När du återskapar nyckeln används den nya nyckeln bara för att registrera följande enheter. De enheter som redan har registrerats påverkas inte av den här processen.

Utför följande steg för att återskapa en nyckel för tjänstregistrering.

### <a name="to-regenerate-the-service-registration-key"></a>Återskapa nyckeln för tjänstregistrering
1. På den **StorSimple Manager-tjänsten** klickar du på **registreringsnyckel**.
2. I den **nyckel för tjänstregistrering** dialogrutan klickar du på **återskapa**.
3. Visas ett bekräftelsemeddelande. Klicka på **OK** att fortsätta med omgenerering.
4. En ny nyckel för tjänstregistrering visas.
5. Kopiera den här nyckeln och spara den för att registrera nya enheter med den här tjänsten.
6. Klicka på kryssikonen ![Kryssikon](./media/storsimple-manage-service/HCS_CheckIcon.png) Stäng den här dialogrutan.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om den [StorSimple distributionsprocessen](storsimple-deployment-walkthrough-u2.md).
* Lär dig mer om [hantera ditt lagringskonto i StorSimple](storsimple-manage-storage-accounts.md).
* Mer information om hur du [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).
