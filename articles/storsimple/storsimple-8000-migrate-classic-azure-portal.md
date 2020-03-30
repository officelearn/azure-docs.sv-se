---
title: Migrera Lagringskonton för StorSimple-enhetshanteraren, prenumerationer
description: Läs om hur du migrerar prenumerationer, lagringskonton för tjänsten StorSimple Device Manager.
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
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 428c336d98e278910b229e9c0d877a9ae6268c96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169710"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrera prenumerationer och lagringskonton som är associerade med Tjänsten StorSimple Device Manager

Du kan behöva flytta din StorSimple-tjänst till en ny registrering eller till en ny prenumeration. Dessa migreringsscenarier är antingen kontoändringar eller datacenterändringar. Använd följande tabell för att förstå vilka av dessa scenarier som stöds, inklusive de detaljerade stegen för att flytta.

## <a name="account-changes"></a>Kontoändringar

| Kan du flytta ...| Stöds| Driftstopp| Azure-supportprocess| Metod|
|-----|-----|-----|-----|-----|
| En hel prenumeration (inkluderar StorSimple-tjänst- och lagringskonton) till en annan registrering? | Ja       | Inga       | **Överföring av registrering**<br>Använda:<li>När du köper ett nytt Azure-åtagande enligt ett nytt avtal.</li><li>Du vill migrera alla konton och prenumerationer från den gamla registreringen till den nya. Detta inkluderar alla Azure-tjänster under den gamla prenumerationen.</li> | **Steg 1: Öppna en Azure Enterprise Operation Support-biljett.**<li>Gå [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)till .</li><li> Välj **Registreringsadministration** och välj sedan **Överför från en registrering till en ny registrering**.<br>**Steg 2: Ange begärd information**<br>Inkluderar:<li>registreringsnummer för källa</li><li> destinationsregistreringsnummer</li><li>överföringsdatum|
| StorSimple-tjänst från ett befintligt konto till en ny registrering?    | Ja       | Inga       | **Överföring av konto**<br>Använda:<li>När du inte vill ha en fullständig registreringsöverföring.</li><li>Du vill bara flytta specifika konton till en ny registrering.</li>| **Steg 1: Öppna en Azure Enterprise Operation Support-biljett.**<li>Gå [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)till .</li><li>Välj **Registreringsadministration** och välj sedan **Överför ett EA-konto till en ny registrering**.<br>**Steg 2: Ange begärd information**<br>Inkluderar:<li>registreringsnummer för källa</li><li> destinationsregistreringsnummer</li><li>överföringsdatum|
| StorSimple-tjänst från en prenumeration på en annan prenumeration?      | Inga        |    Ja         | Ingen, manuell process|<li>Migrera data från StorSimple-enheten.</li><li>Utför en fabriksåterställning av enheten, detta tar bort alla lokala data på enheten.</li><li>Registrera enheten med den nya prenumerationen på en StorSimple Device Manager-tjänst.</li><li>Migrera data tillbaka till enheten.|
|Kan jag överföra ägarskapet för en Azure-prenumeration till en annan katalog? | Ja       | Inga       | Associera en befintlig prenumeration till din Azure AD-katalog | Referera [till Associera en befintlig prenumeration till din Azure AD-katalog](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Det kan ta upp till 10 minuter innan allt visas korrekt.|
| StorSimple-enhet från en StorSimple Enhetshanterarens tjänst till en annan tjänst i en annan region?      | Inga        | Ja            | Ingen, manuell process |Samma som ovan.|
| Lagringskonto till en ny prenumeration eller resursgrupp?     | Ja        | Inga             |Flytta lagringskonto till en annan prenumeration eller resursgrupp |Om åtkomstnycklarna för lagringskontot uppdateras måste användaren konfigurera åtkomstnycklarna manuellt för det migrerade lagringskontot via Tjänsten StorSimple Device Manager.|
| Klassiskt lagringskonto till ett Azure Resource Manager-lagringskonto      | Ja        | Inga             |Migrera från klassisk till Azure Resource Manager |<li>Detaljerade instruktioner om hur du migrerar ett lagringskonto från klassiskt till Azure Resource Manager finns i [Migrera ett klassiskt lagringskonto](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account).</li><li> Om åtkomstnycklarna för lagringskontot uppdateras efter migreringen måste användaren synkronisera åtkomstnycklarna för det migrerade lagringskontot via Tjänsten StorSimple Device Manager. Detta för att säkerställa att StorSimple-enheterna fortsätter att fungera normalt och kan nivåiella primära/säkerhetskopierade data till Azure. Detaljerade instruktioner om hur du synkroniserar åtkomstnycklar finns i [arbetsflödet Rotation](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> När det gäller en StorSimple Cloud Appliance, om det klassiska lagringskontot migreras men den underliggande virtuella datorn fortfarande förblir i klassisk, bör installationen fungera korrekt. Om den underliggande virtuella datorn för molninstallationen migreras fungerar inte funktionen inaktivera och ta bort.</li><li> Du måste skapa en ny StorSimple Cloud Appliances i Azure-portalen och sedan växla över från de äldre molninstallationerna. Du kan inte skapa en StorSimple Cloud Appliance i den nya Azure-portalen med ett klassiskt lagringskonto, de måste ha ett Azure Resource Manager-lagringskonto. Mer information finns i [Distribuera och hantera en StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).</li>|

## <a name="datacenter-changes"></a>Datacenter ändringar

| Kan du flytta ...| Stöds|Driftstopp| Azure-supportprocess| Metod|
|-----|-----|-----|-----|-----|
| En StorSimple-tjänst från ett Azure-datacenter till ett annat? | Inga | Ja |Ingen, manuell process  |<li>Migrera data från StorSimple-enheten.</li><li>Utför en fabriksåterställning av enheten, detta tar bort alla lokala data på enheten.</li><li>Registrera enheten med den nya prenumerationen på en ny StorSimple Device Manager-tjänst.</li><li>Migrera data tillbaka till enheten.|
| Ett lagringskonto från ett Azure-datacenter till ett annat? | Inga |Ja  |Ingen, manuell process  | Samma som ovan.|

## <a name="next-steps"></a>Nästa steg

* [Distribuera Tjänsten StorSimple Device Manager](storsimple-8000-manage-service.md)
* [Distribuera StorSimple 8000-serien enhet i Azure Portal](storsimple-8000-deployment-walkthrough-u2.md)
