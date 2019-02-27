---
title: Övervakning av hot i Azure Storage
description: Konfigurera Azure Storage Advanced Threat Protection för att identifiera avvikelser i kontoaktivitet och meddela dig om potentiellt skadliga försök att komma åt ditt konto.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 09/24/2018
ms.author: ronmat
ms.manager: shaik
ms.openlocfilehash: 00de38aa7309179b92ff65f009f8aa780f60c284
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883699"
---
# <a name="azure-storage-advanced-threat-protection"></a>Azure Storage Advanced Threat Protection

Azure Storage Advanced Threat Protection identifierar avvikelser i kontoaktivitet och meddelar dig om potentiellt skadliga försök att komma åt ditt konto. Du kan hantera hot utan att behöva vara säkerhetsexpert eller hantera säkerhetssystem för övervakning i den här skyddsnivå.

Hot exponeras genom att definiera säkerhetsaviseringar som utlöser när avvikelser i aktivitet inträffar. Dessa aviseringar integrera med [Azure Security Center](https://azure.microsoft.com/services/security-center/) som innehåller information om misstänkt aktivitet och rekommendationer om hur du undersöka och åtgärda hot. 

> [!NOTE]
> Azure Storage Advanced Threat Protection finns för närvarande endast för Blob-tjänsten. Säkerhetsaviseringar är integrerade med Azure Security Center och skickas via e-post till prenumerationens administratörer.

Azure Storage Advanced Threat Protection matar in diagnostiska loggar för läsning, skriva och ta bort begäranden till Blob-tjänsten för identifiering av hot. Om du vill undersöka aviseringar från Avancerat skydd, måste du [konfigurera diagnostikloggar](storage-monitor-storage-account.md#configure-logging) att aktivera alla nivåer av loggar för Blob-tjänsten.

## <a name="set-up-advanced-threat-protection-in-the-portal"></a>Konfigurera Avancerat skydd i portalen

1. Starta Azure-portalen på [ https://portal.azure.com ](https://portal.azure.com/).

2. Gå till konfigurationssidan för Azure Storage-konto som du vill skydda. I den **inställningar** väljer **Advanced Threat Protection**.

3. I den **Advanced Threat Protection** konfigurationsbladet
    * Aktivera **på** avancerade *skydd mot hot*
    * Klicka på **spara** att spara den nya eller uppdaterade Avancerat skydd-principen.

![Aktivera Azure Storage Avancerat skydd](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

## <a name="explore-anomalies"></a>Utforska avvikelser

När storage aktivitet avvikelser inträffar, kan du få ett e-postmeddelande med information om den misstänkta händelsen. Information om händelsen är:

* natur avvikelsen
* Lagringskontonamn
* Lagringstyp
* tidpunkt för händelsen

E-postmeddelandet också innehåller detaljer om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet.

![Azure advanced threat protection aviseringsmeddelande-lagring](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Du kan granska och hantera din aktuella säkerhetsaviseringar från Azure Security Center [panelen säkerhetsaviseringar](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts). När du klickar på en specifik avisering innehåller information och åtgärder för att undersöka den aktuella hot och framtida hot-adressering.

![Azure advanced threat protection aviseringsmeddelande-lagring](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Protection-aviseringar

Aviseringarna genereras av onormala och potentiellt skadliga försök att komma åt eller utnyttja storage-konton. Dessa händelser kan utlösa följande aviseringar:

* **Åtkomst från ovanlig plats**: Den här aviseringen utlöses när det finns en ändring i åtkomstmönstret för till ett lagringskonto. Till exempel när någon har åtkomst till ett lagringskonto från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller utvecklare underhållsåtgärden). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare osv.).

* **Ovanlig dataextrahering**: Den här aviseringen utlöses när det finns en ändring i mönstret för extrahering av data från ett lagringskonto. Till exempel om någon har använt en ovanlig mängden data i ett lagringskonto. I vissa fall identifierar aviseringen en giltig åtgärd (Underhåll aktivitet). I andra fall identifierar aviseringen en skadlig åtgärd (data exfiltrering/intrång, obehörig överföring av data).

* **Ovanlig anonym åtkomst:** Den här aviseringen utlöses när det finns en ändring i åtkomstmönstret för till ett lagringskonto. Anta exempelvis att någon anonymt har åtkomst till ett lagringskonto. I vissa fall identifierar aviseringen en giltig åtkomst med hjälp av offentlig läsbehörighet. I andra fall identifierar aviseringen obehörig åtkomst som utnyttjar offentlig läsbehörighet till en behållare och dess blobbar.

* **Oväntat delete:** Den här aviseringen utlöses när en eller flera oväntade delete-åtgärder sker i ett lagringskonto, baserat på historisk analys av storage-konto. Anta exempelvis att någon utförs en *DeleteBlob* igen med ett nytt program och från en ny IP-adress. I vissa fall identifierar aviseringen en giltig åtgärd (administratören används en annan webbläsare resande på företag). I andra fall identifierar aviseringen en skadlig åtgärd (en angripare som tar bort data). 
 
* **Åtkomst till behörigheten Ändra:** Den här aviseringen utlöses när det finns en oväntad ändring av åtkomstbehörighet till ett lagringskonto. Anta exempelvis att någon ändrat åtkomstbehörigheten till ett lagringskonto med ett nytt program och från en ny IP-adress. I vissa fall identifierar aviseringen en giltig åtgärd (administratören används en annan webbläsare resande på företag). I andra fall identifierar aviseringen en skadlig åtgärd (t.ex. en angripare öka rättigheter för ett konto som de har fått åtkomst till). 

* **Ladda upp Azure Cloud Service-paketet:** Den här aviseringen utlöses när det finns en oväntat överföring av ett Azure Cloud Service-paket (*.cspkg* filen) till ett lagringskonto. Anta exempelvis att en *.cspkg* filen laddades upp från en ny IP-adress. I vissa fall identifierar aviseringen en giltig åtgärd. I andra fall identifierar aviseringen en skadlig åtgärd (t.ex. en molntjänst som paketet har överförts inför en distribution av en skadlig service).    
   

## <a name="next-steps"></a>Nästa steg

* Läs mer om [loggar i Azure Storage-konton](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Läs mer om [Azure Security Center](../../security-center/security-center-intro.md)