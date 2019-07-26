---
title: Advanced Threat Protection för Azure Storage
description: Konfigurera Azure Storage Avancerat skydd för att identifiera avvikelser i konto aktivitet och meddela dig om potentiellt skadliga försök att komma åt ditt konto.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 5d0b41344f4a77f3205799be7c30526e2a150523
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479937"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Advanced Threat Protection för Azure Storage

Med Advanced Threat Protection för Azure Storage får du en ytterligare nivå med säkerhetsinsikter som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton. Det här skydds lagret gör att du kan åtgärda hot utan att behöva vara säkerhets expert eller hantera säkerhets övervaknings system. 

Säkerhets varningar utlöses när avvikelser i aktivitet inträffar.  Dessa säkerhets aviseringar är integrerade med [Azure Security Center](https://azure.microsoft.com/services/security-center/)och skickas också via e-post till prenumerations administratörer med information om misstänkt aktivitet och rekommendationer om hur du undersöker och åtgärdar hot.

> [!NOTE]
> * Avancerat skydd för Azure Storage är för närvarande endast tillgängligt för Blob Storage.
> * För pris information, inklusive en kostnads fri 30-dagars utvärderings version, se [sidan Azure Security Center prissättning]( https://azure.microsoft.com/pricing/details/security-center/).
> * ATP för Azure Storage-funktionen är för närvarande inte tillgänglig i moln regionerna Azure myndigheter och suveräna myndigheter.

Avancerat skydd för Azure Storage matar in diagnostikloggar för Läs-, skriv-och borttagnings förfrågningar till Blob Storage för hot identifiering. Om du vill undersöka aviseringar från Avancerat skydd kan du Visa relaterad lagrings aktivitet med hjälp av Lagringsanalys loggning. Mer information finns i så här [konfigurerar du Lagringsanalys loggning](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Konfigurera Avancerat skydd 

### <a name="using-the-portal"></a>Använda portalen

1. Starta Azure Portal på [https://portal.azure.com](https://portal.azure.com/).

2. Gå till konfigurations sidan för det Azure Storage konto som du vill skydda. På sidan **Inställningar** väljer du **Avancerat skydd**.

3. I bladet **Avancerat skydds** konfiguration
    * Aktivera **Avancerat** *skydd*
    * Klicka på **Spara** för att spara den nya eller uppdaterade Advanced Threat Protection-principen. (Priserna i bilden är till exempel endast avsedda.)

![Aktivera Azure Storage Avancerat skydd](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Använda Azure Security Center

När du prenumererar på standard nivån i Azure Security Center konfigureras Avancerat skydd automatiskt på alla dina lagrings konton. Du kan aktivera eller inaktivera Avancerat skydd för dina lagrings konton under en speciell prenumeration på följande sätt:

1. Starta **Azure Security Center** i [Azure Portal]([https://portal.azure.com).
1. Klicka på **priser & inställningar**på huvud menyn.
1. Klicka på den prenumeration som du vill aktivera eller inaktivera hot skydd för dess lagrings konton.

    ![Välj en prenumeration](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Klicka på **pris nivå**.
1. I avsnittet **Välj pris nivå efter resurs typ** klickar du på aktive rad eller inaktive rad på raden **lagrings konton** .

    ![Aktivera ATP i Security Center](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Klicka på **Spara**.

### <a name="using-azure-resource-manager-templates"></a>Använda Azure Resource Manager mallar

Använd en Azure Resource Manager mall för att distribuera ett Azure Storage konto med avancerat skydd aktiverat. Mer information finns i [lagrings konto med avancerat skydd](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Använda Azure Policy

Använd en Azure Policy för att aktivera avancerat skydd mellan lagrings konton under en speciell prenumeration eller resurs grupp.

1. Starta sidan Azure **policy-definitions** .

1. Sök efter principen **distribuera Avancerat skydd på lagrings konton** .

     ![Sök princip](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Välj en Azure-prenumeration eller resurs grupp.

    ![Välj prenumeration eller grupp](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Tilldela principen.

    ![Sidan princip definitioner](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>Använda REST-API:et
Använd REST API-kommandon för att skapa, uppdatera eller Hämta inställningen för avancerat skydd för ett angivet lagrings konto.

* [Avancerat skydd – skapa](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Avancerat skydd – Hämta](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

Använd följande PowerShell-cmdletar:

  * [Aktivera avancerat skydd](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Hämta Avancerat skydd](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Inaktivera Avancerat skydd](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Utforska säkerhets avvikelser

När avvikelser i lagrings aktiviteter inträffar får du ett e-postmeddelande med information om den misstänkta säkerhets händelsen. Information om händelsen är:

* Avvikelsens karaktär
* Lagrings kontots namn
* Händelse tiden
* Lagrings typ
* Möjliga orsaker 
* Undersöknings stegen
* Reparations stegen


E-postmeddelandet innehåller också information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet.

![Azure Storage e-postadress för avancerat skydds avisering](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Du kan granska och hantera dina aktuella säkerhets aviseringar från Azure Security Center [rutan säkerhets aviseringar](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts). Om du klickar på en enskild avisering visas information och åtgärder för att undersöka det aktuella hotet och åtgärda framtida hot.

![Azure Storage e-postadress för avancerat skydds avisering](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Skydds aviseringar

Aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Dessa händelser kan utlösa följande aviseringar:

### <a name="anomalous-access-pattern-alerts"></a>Aviseringar om avvikande åtkomst mönster

* **Åtkomst från ovanlig plats**: Den här aviseringen utlöses när någon har fått åtkomst till ett lagrings konto från en ovanlig geografisk plats.
Möjliga orsaker:
   * En angripare har åtkomst till ditt lagrings konto
   * En legitim användare har åtkomst till ditt lagrings konto från en ny plats
 
* **Program avvikelse**: Den här varningen anger att ett ovanligt program har åtkomst till det här lagrings kontot. Möjliga orsaker:
   * En angripare har åtkomst till ditt lagrings konto med hjälp av ett nytt program.
   * En legitim användare har använt ett nytt program/en ny webbläsare för att komma åt ditt lagrings konto.

* **Anonym åtkomst**: Den här varningen anger att det här kontot har öppnats anonymt (dvs. utan någon autentisering), vilket är oväntat jämfört med det senaste åtkomst mönstret för det här kontot.
Möjliga orsaker:
   * En angripare har utnyttjat offentlig Läs behörighet till en behållare.
   * En legitim användare eller ett program har använt offentlig Läs behörighet till en behållare.

* **Tor-avvikelse**: Den här varningen anger att det här kontot har kunnat nås från en IP-adress som kallas för en aktiv nod i Tor (en maskera proxy). Allvarlighets graden för den här aviseringen tar hänsyn till den autentiseringstyp som används (om det finns någon) och om detta är första fallet för sådan åtkomst.
Möjliga orsaker:
   * En angripare har åtkomst till ditt lagrings konto med Tor.
   * En legitim användare har åtkomst till ditt lagrings konto med Tor.


### <a name="anomalous-extractupload-alerts"></a>Avvikande utdrag/uppladdning-aviseringar

* **Data exfiltrering**: Den här varningen anger att en ovanligt stor mängd data har extraherats jämfört med senaste aktivitet på den här lagrings behållaren. Möjliga orsaker:
   * En angripare har extraherat en stor mängd data från en behållare. (Till exempel: data exfiltrering/intrång, obehörig överföring av data)
   * En legitim användare eller ett program har extraherat en ovanlig mängd data från en behållare. (Till exempel: underhålls aktivitet)

* **Oväntad borttagning**: Den här varningen anger att en eller flera oväntade borttagnings åtgärder har inträffat i ett lagrings konto, jämfört med senaste aktivitet på det här kontot. Möjliga orsaker:
   * En angripare har tagit bort data från ditt lagrings konto.
   * En legitim användare har utfört en ovanlig borttagning.

* **Ladda upp Azure Cloud Service-paket**: Den här varningen anger att ett Azure Cloud Service-paket (. cspkg-fil) har överförts till ett lagrings konto på ett ovanligt sätt jämfört med senaste aktivitet på det här kontot. Möjliga orsaker: 
   * En angripare har förberett sig för att distribuera skadlig kod från ditt lagrings konto till en moln tjänst i Azure.
   * En legitim användare har för berett en legitim tjänst distribution.

### <a name="suspicious-storage-activities-alerts"></a>Aviseringar om misstänkta lagrings aktiviteter

* **Ändring av åtkomst behörighet**: Den här varningen anger att åtkomst behörigheterna för den här lagrings behållaren har ändrats på ett onormalt sätt. Möjliga orsaker: 
   * En angripare har ändrat behållar behörigheten för att begränsa säkerheten.
   * En legitim användare har ändrat behållar behörigheter.

* **Åtkomst kontroll**: Den här varningen anger att åtkomst behörigheterna för ett lagrings konto har inspekterats på ett onormalt sätt, jämfört med senaste aktivitet på det här kontot. Möjliga orsaker: 
   * En angripare har utfört rekognosering för framtida angrepp.
   * En legitim användare har utfört underhåll av lagrings kontot.

* **Data utforskning**: Den här varningen anger att blobbar eller behållare i ett lagrings konto har räknats upp på ett onormalt sätt, jämfört med senaste aktivitet på det här kontot. Möjliga orsaker: 
   * En angripare har utfört rekognosering för framtida angrepp.
   * En legitim användare eller program logik har utforskat data i lagrings kontot.






## <a name="next-steps"></a>Nästa steg

* Läs mer om [loggar i Azure Storage-konton](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Läs mer om [Azure Security Center](../../security-center/security-center-intro.md)
