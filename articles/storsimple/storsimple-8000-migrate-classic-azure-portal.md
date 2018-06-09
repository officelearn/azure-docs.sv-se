---
title: Migrera storage-konton, prenumerationer för din StorSimple Device Manager-tjänst | Microsoft Docs
description: Lär dig hur du migrerar prenumerationer, storage-konton för din StorSimple Enhetshanteraren service8000.
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
ms.openlocfilehash: 299029e10ac8f0235bb11876aa00c98a21183c0f
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248800"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrera prenumerationer och storage-konton som är kopplade till StorSimple enheten Manager-tjänsten

Du kan behöva flytta din StorSimple-tjänst till en ny registrering eller till en ny prenumeration. Dessa scenarier med migrering är kontoändringar eller datacenter ändringar. Använd följande tabell för att förstå vilka av dessa scenarier stöds inklusive detaljerade steg för att flytta.

## <a name="account-changes"></a>Kontoändringar

| Du kan flytta...| Stöds| Stilleståndstid| Azure supportprocessen| Metoden|
|-----|-----|-----|-----|-----|
| En hela prenumerationen (inklusive StorSimple-tjänsten och storage-konton) till en annan registrering? | Ja       | Nej       | **Registrering av överföring**<br>Användning:<li>När du köper ett nytt Azure åtagande under ett nytt avtal.</li><li>Du vill migrera alla konton och prenumerationer från gamla registreringen till den nya. Detta inkluderar alla Azure-tjänster under gamla prenumerationen.</li> | **Steg 1: Öppna ett supportärende för Azure Enterprise igen.**<li>Gå till [http://aka.ms/AzureEntSupport](http://aka.ms/AzureEntSupport).</li><li> Välj **registrering Administration** och välj sedan **dataöverföring från en registrering till en ny registrering**.<br>**Steg 2: Ange den begärda informationen**<br>Inkludera:<li>registreringsnummer för källa</li><li> mål registreringsnummer</li><li>giltighetsdatum för överföring|
| StorSimple-tjänsten från ett befintligt konto till en ny registrering?    | Ja       | Nej       | **Kontoöverföring**<br>Användning:<li>När du inte vill att en fullständig registrering-överföring.</li><li>Du bara vill flytta specifika konton till en ny registrering.</li>| **Steg 1: Öppna ett supportärende för Azure Enterprise igen.**<li>Gå till [http://aka.ms/AzureEntSupport](http://aka.ms/AzureEntSupport).</li><li>Välj **registrering Administration** och välj sedan **överföra ett EA-konto till en ny registrering**.<br>**Steg 2: Ange den begärda informationen**<br>Inkludera:<li>registreringsnummer för källa</li><li> mål registreringsnummer</li><li>giltighetsdatum för överföring|
| StorSimple-tjänsten från en prenumeration till en annan prenumeration?      | Nej        |    Ja         | Ingen manuell process|<li>Migrera data från StorSimple-enhet.</li><li>Utför en fabriksåterställning på enheten kan detta tar bort alla lokala data på enheten.</li><li>Registrera enheten med den nya prenumerationen till en StorSimple Device Manager-tjänst.</li><li>Migrera data till enheten.|
  |Kan jag överföra ägarskap för en Azure-prenumeration till en annan katalog? | Ja       | Nej       | Koppla en befintlig prenumeration till din Azure AD-katalog | Se [att koppla en befintlig prenumeration till Azure AD-katalogen](../active-directory/active-directory-how-subscriptions-associated-directory.md). Det kan ta upp till 10 minuter innan allt visas korrekt.|
| StorSimple-enhet från en StorSimple Device Manager-tjänsten till en annan tjänst i en annan region?      | Nej        | Ja            | Ingen manuell process |Samma som ovan.|
| Storage-konto till en ny grupp för prenumerationen eller resursen?     | Ja        | Nej             |Flytta lagringskonto till annan prenumerationen eller resursen grupp |Efter överflyttningen, om åtkomstnycklar för lagring konto uppdateras behöver användaren du konfigurera snabbtangenter manuellt för migrerade storage-konto via tjänsten StorSimple Enhetshanteraren.|
| Klassiska storage-konto till en Azure Resource Manager storage-konto      | Ja        | Nej             |Migrera från klassiska till Azure Resource Manager |<li>För detaljerade anvisningar om hur du migrerar en lagringskontot från classic till Azure Resource Manager går du till [migrera klassiska lagringskontot](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-62-migrate-a-storage-account).</li><li> Om åtkomstnycklar för lagring konto uppdateras efter migreringen, måste användaren att synkronisera åtkomstnycklarna för migrerade storage-konto via tjänsten StorSimple Enhetshanteraren. Detta är att säkerställa StorSimple-enheter fungerar normalt och kunna tjänstnivån primära/säkerhetskopierade data till Azure. Detaljerad information om synkronisering av snabbtangenter går du till [Rotation arbetsflöde](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> När det gäller en molnet StorSimple-enhet om klassiska lagringskontot migreras men den underliggande virtuella datorn fortfarande är alltid i klassiskt, ska anordningen fungera korrekt. Om den underliggande virtuella för molnet anordningen migreras, fungerar inte inaktivera och ta bort funktioner.</li><li> Du måste skapa en ny StorSimple moln installationer i Azure-portalen och sedan växla över från äldre moln-installationer. Du kan inte skapa en StorSimple-enhet för molnet i den nya Azure-portalen med ett klassiska storage-konto, de måste ha ett lagringskonto i Azure Resource Manager. Mer information finns på [distribuera och hantera en StorSimple-enhet för molnet](storsimple-8000-cloud-appliance-u2.md).</li>|Samma som ovan.|

## <a name="datacenter-changes"></a>Datacenter ändringar

| Du kan flytta...| Stöds|Stilleståndstid| Azure supportprocessen| Metoden|
|-----|-----|-----|-----|-----|
| En StorSimple-tjänsten från en Azure-datacenter till en annan? | Nej | Ja |Ingen manuell process  |<li>Migrera data från StorSimple-enhet.</li><li>Utför en fabriksåterställning på enheten kan detta tar bort alla lokala data på enheten.</li><li>Registrera enheten med den nya prenumerationen till en ny StorSimple Device Manager-tjänst.</li><li>Migrera data till enheten.|
| Ett lagringskonto från en Azure-datacenter till en annan? | Nej |Ja  |Ingen manuell process  | Samma som ovan.|

## <a name="next-steps"></a>Nästa steg

* [Distribuera StorSimple enheten Manager-tjänsten](storsimple-8000-manage-service.md)
* [Distribuera StorSimple 8000-serien enheten i Azure-portalen](storsimple-8000-deployment-walkthrough-u2.md)
