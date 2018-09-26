---
title: Migrera storage-konton, prenumerationer för din StorSimple Device Manager-tjänsten | Microsoft Docs
description: Lär dig hur du migrerar prenumerationer, storage-konton för din StorSimple Device Manager-service8000.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/08/2018
ms.author: alkohli
ms.openlocfilehash: 5a0da47b854e625f2f4a2fcf4c95ec566ba63093
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095686"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrera prenumerationer och lagringskonton som är associerade med StorSimple Device Manager-tjänsten

Du kan behöva flytta din StorSimple-tjänsten till en ny registrering eller till en ny prenumeration. Dessa Migreringsscenarier är kontoändringar eller datacenter-ändringar. Använd följande tabell för att förstå vilka av dessa scenarier stöds inklusive detaljerade steg för att flytta.

## <a name="account-changes"></a>Kontoändringar

| Du kan flytta...| Stöds| Stilleståndstid| Azure supportprocessen| Metoden|
|-----|-----|-----|-----|-----|
| En hela prenumerationen (inklusive StorSimple-tjänsten och storage-konton) till en annan registrering? | Ja       | Nej       | **Överföring av registrering**<br>Användning:<li>När du köper en ny Azure utfästelse under ett nytt avtal.</li><li>Du vill migrera alla konton och prenumerationer från den gamla registreringen till den nya. Detta inkluderar alla Azure-tjänster under den gamla prenumerationen.</li> | **Steg 1: Öppna ett supportärende för Azure Enterprise igen.**<li>Gå till [http://aka.ms/AzureEntSupport](http://aka.ms/AzureEntSupport).</li><li> Välj **registrering Administration** och välj sedan **överföra från en registrering till en ny registrering**.<br>**Steg 2: Ange informationen som efterfrågas**<br>Omfatta:<li>registreringsnummer för källa</li><li> mål-registreringsnummer</li><li>ikraftträdandedatum för överföring|
| StorSimple-tjänster från ett befintligt konto till en ny registrering?    | Ja       | Nej       | **Kontoöverföring**<br>Användning:<li>När du inte vill att en fullständig registrering-överföring.</li><li>Du bara vill flytta specifika konton till en ny registrering.</li>| **Steg 1: Öppna ett supportärende för Azure Enterprise igen.**<li>Gå till [http://aka.ms/AzureEntSupport](http://aka.ms/AzureEntSupport).</li><li>Välj **registrering Administration** och välj sedan **överföra ett EA-konto till en ny registrering**.<br>**Steg 2: Ange informationen som efterfrågas**<br>Omfatta:<li>registreringsnummer för källa</li><li> mål-registreringsnummer</li><li>ikraftträdandedatum för överföring|
| StorSimple-tjänsten från en prenumeration till en annan prenumeration?      | Nej        |    Ja         | Ingen, manuell process|<li>Migrera data från StorSimple-enhet.</li><li>Utföra en fabriksåterställning av enheten, detta tar bort eventuella lokala data på enheten.</li><li>Registrera enheten med den nya prenumerationen till en StorSimple Device Manager-tjänst.</li><li>Migrera data till enheten.|
  |Kan jag överföra ägarskap för en Azure-prenumeration till en annan katalog? | Ja       | Nej       | Koppla en befintlig prenumeration till din Azure AD-katalog | Se [att koppla en befintlig prenumeration i Azure AD-katalogen](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Det kan ta upp till 10 minuter innan allt visas korrekt.|
| StorSimple-enhet från en StorSimple Device Manager-tjänst till en annan tjänst i en annan region?      | Nej        | Ja            | Ingen, manuell process |Samma som ovan.|
| Storage-konto till en ny prenumeration eller resursgrupp grupp?     | Ja        | Nej             |Flytta storage-konto till en annan prenumeration eller resursgrupp grupp |Efter överflyttningen om åtkomst till lagringskontonycklarna har uppdaterats, behöver användaren du konfigurera åtkomstnycklarna för det migrerade storage-kontot via StorSimple Device Manager-tjänsten manuellt.|
| Klassiskt lagringskonto till ett Azure Resource Manager-lagringskonto      | Ja        | Nej             |Migrera från klassisk till Azure Resource Manager |<li>Detaljerade anvisningar om hur du migrerar ett storage-konto från klassisk till Azure Resource Manager finns i [Migrera ett klassiskt lagringskonto](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-62-migrate-a-storage-account).</li><li> Om åtkomstnycklarna för lagringskontot har uppdaterats efter migreringen, måste användaren att synkronisera åtkomstnycklarna för det migrerade storage-kontot via StorSimple Device Manager-tjänsten. Det här är att se till att StorSimple-enheter fungerar normalt och kan nivån primär-/ säkerhetskopieringsdata till Azure. Detaljerad information om synkronisera åtkomstnycklarna går du till [Rotation arbetsflöde](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> När det gäller en StorSimple-Molninstallationen om klassiskt lagringskonto har migrerats, men den underliggande virtuella datorn fortfarande kvar i klassisk, ska installationen fungera korrekt. Om den underliggande virtuella datorn för molninstallationen har migrerats, fungerar inte inaktivera och ta bort funktioner.</li><li> Du måste skapa en ny StorSimple Cloud Appliances i Azure portal och sedan växla över från äldre molninstallationer. Du kan inte skapa en StorSimple-Molninstallation i nya Azure-portalen med ett klassiskt lagringskonto, de måste ha ett Azure Resource Manager-lagringskonto. Mer information går du till [distribuera och hantera en StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).</li>|Samma som ovan.|

## <a name="datacenter-changes"></a>Ändringar av Datacenter

| Du kan flytta...| Stöds|Stilleståndstid| Azure supportprocessen| Metoden|
|-----|-----|-----|-----|-----|
| En StorSimple-tjänsten från en Azure-datacenter till en annan? | Nej | Ja |Ingen, manuell process  |<li>Migrera data från StorSimple-enhet.</li><li>Utföra en fabriksåterställning av enheten, detta tar bort eventuella lokala data på enheten.</li><li>Registrera enheten med den nya prenumerationen till en ny StorSimple Device Manager-tjänst.</li><li>Migrera data till enheten.|
| Ett lagringskonto från en Azure-datacenter till en annan? | Nej |Ja  |Ingen, manuell process  | Samma som ovan.|

## <a name="next-steps"></a>Nästa steg

* [Distribuera StorSimple Device Manager-tjänsten](storsimple-8000-manage-service.md)
* [Distribuera enhet i StorSimple 8000-serien i Azure-portalen](storsimple-8000-deployment-walkthrough-u2.md)
