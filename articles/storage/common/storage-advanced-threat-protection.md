---
title: Advanced Threat Protection för Azure Storage
description: Konfigurera Azure Storage Advanced Threat Protection för att identifiera avvikelser i kontoaktivitet och meddela dig om potentiellt skadliga försök att komma åt ditt konto.
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: monhaber
ms.manager: shaik
ms.openlocfilehash: c42867ff7aea2210f20a2cd2adb5c067b8f36c80
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926509"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Advanced Threat Protection för Azure Storage

Med Advanced Threat Protection för Azure Storage får du en ytterligare nivå med säkerhetsinsikter som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton. Du kan hantera hot utan att behöva vara säkerhetsexpert eller hantera säkerhetssystem för övervakning i den här skyddsnivå. 

Säkerhetsvarningar utlöses när avvikelser i aktivitet inträffar.  Dessa säkerhetsaviseringar som är integrerade med [Azure Security Center](https://azure.microsoft.com/services/security-center/), och också skickas via e-post till prenumerationens administratörer med information om misstänkt aktivitet och rekommendationer om hur du undersöka och åtgärda hot.

> [!NOTE]
> * Avancerat skydd för Azure Storage är endast tillgänglig för Blob-lagringen.
> * Prisinformationen, inklusive en kostnadsfria 30-dagars utvärderingsversion finns i den [sidan med priser för Azure Security Center]( https://azure.microsoft.com/pricing/details/security-center/).
> * ATP för Azure storage-funktionen är för närvarande inte tillgänglig i Azure government och nationellt molnregioner.

Avancerat skydd för Azure Storage matar in diagnostiska loggar för Läs-, Skriv- och delete-begäranden till Blob storage för identifiering av hot. Du kan visa relaterade storage-aktivitet som använder loggning av Storage Analytics för att undersöka aviseringar från Avancerat skydd. Läs mer om hur du [konfigurera Storage Analytics loggning](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Konfigurera Avancerat skydd 

### <a name="using-the-portal"></a>Använda portalen

1. Starta Azure-portalen på [ https://portal.azure.com ](https://portal.azure.com/).

2. Gå till konfigurationssidan för Azure Storage-konto som du vill skydda. I den **inställningar** väljer **Advanced Threat Protection**.

3. I den **Advanced Threat Protection** konfigurationsbladet
    * Aktivera **på** avancerade *skydd mot hot*
    * Klicka på **spara** att spara den nya eller uppdaterade Avancerat skydd-principen. (Priserna i avbildningen är till exempel endast.)

![Aktivera Azure Storage Avancerat skydd](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Använda Azure Security Center
När du prenumererar på Standard-nivån i Azure Security Center Advanced Threat Protection har ställts in på dina lagringskonton. Mer information finns i [uppgradera till standardnivån i Security Center för ökad säkerhet](https://docs.microsoft.com/azure/security-center/security-center-pricing). (Priserna i avbildningen är till exempel endast.)

![Standard-nivån i ASC](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing.png)

### <a name="using-azure-resource-manager-templates"></a>Med Azure Resource Manager-mallar

Använd en Azure Resource Manager-mall för att distribuera ett Azure Storage-konto med avancerat skydd aktiverat.
Mer information finns i [Storage-konto med Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Med Azure-princip

Använda en Azure för att aktivera Avancerat skydd på lagringskonton under en viss prenumeration eller resursgrupp grupp.

1. Lunch Azure **princip – definitioner** sidan.

1. Sök efter den **distribuera Avancerat skydd på Storage-konton** princip.

     ![Sök efter princip](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Välj en Azure-prenumeration eller resursgrupp grupp.

    ![Välj en prenumeration eller grupp](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Tilldela principen.

    ![Sidan definitioner](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>Använda REST-API:et
Använda Rest API-kommandon för att skapa, uppdatera eller få Avancerat skydd-inställningarna för ett specifikt lagringskonto.

* [Avancerat skydd - skapa](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Avancerat skydd - hämta](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

Använd följande PowerShell-cmdletar:

  * [Aktivera Avancerat skydd](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Få Avancerat skydd](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Inaktivera Avancerat skydd](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Utforska säkerhetsavvikelser

När storage aktivitet avvikelser inträffar, kan du få ett e-postmeddelande med information om den misstänkta händelsen. Information om händelsen är:

* Typen av avvikelsen
* Lagringskontonamn
* Tidpunkt för händelsen
* Lagringstyp
* Möjliga orsaker 
* Vilka undersökningssteg
* Anvisningarna för reparation


E-postmeddelandet också innehåller detaljer om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet.

![Azure advanced threat protection aviseringsmeddelande-lagring](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Du kan granska och hantera din aktuella säkerhetsaviseringar från Azure Security Center [panelen säkerhetsaviseringar](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts). När du klickar på en specifik avisering innehåller information och åtgärder för att undersöka den aktuella hot och framtida hot-adressering.

![Azure advanced threat protection aviseringsmeddelande-lagring](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Protection-aviseringar

Aviseringarna genereras av onormala och potentiellt skadliga försök att komma åt eller utnyttja storage-konton. Dessa händelser kan utlösa följande aviseringar:

### <a name="anomalous-access-pattern-alerts"></a>Avvikande åtkomst mönstret aviseringar

* **Åtkomst från ovanlig plats**: Den här aviseringen utlöses när det finns en ändring i åtkomstmönstret för till ett lagringskonto. Till exempel när någon har åtkomst till ett lagringskonto från en ovanlig geografisk plats.
Möjliga orsaker:
   * En angripare har åtkomst till ditt storage-konto
   * En legitim användare har åtkomst till ditt lagringskonto från en ny plats
 
* **Programmet Avvikelseidentifiering**: Den här varningen anger att ett ovanligt program har åtkomst till det här lagringskontot. Möjliga orsaker:
   * En angripare har åtkomst till ditt storage-konto med hjälp av ett nytt program.
   * En legitim användare har använt en ny application/webbläsare för att få åtkomst till ditt storage-konto.

* **Anonym åtkomst**: Den här varningen anger att det finns en ändring i åtkomstmönstret för till ett lagringskonto. Till exempel det här kontot har varit nås anonymt (d.v.s. utan någon autentisering), som är oväntad jämfört med de senaste åtkomstmönstret på det här kontot.
Möjliga orsaker:
   * En angripare har utnyttjat offentlig läsbehörighet till en behållare.
   * En legitim användare eller ett program har använt offentlig läsbehörighet till en behållare.

### <a name="anomalous-extractupload-alerts"></a>Avvikande extrahera/laddar upp aviseringar

* **Dataexfiltrering**: Den här varningen anger att ett ovanligt stora mängder data har extraherats jämfört med senaste aktivitet på denna lagringsbehållare. Möjliga orsaker:
   * En angripare har extraherats en stor mängd data från en behållare. (Till exempel: data exfiltrering/intrång, obehörig överföring av data)
   * En legitim användare eller ett program har extraherats en ovanlig datamängd från en behållare. (Till exempel: Underhåll)

* **Oväntat delete**: Den här varningen anger att en eller flera oväntade delete-åtgärder har inträffat i ett lagringskonto, jämfört med senaste aktiviteter för det här kontot. Möjliga orsaker:
   * En angripare har tagit bort data från ditt lagringskonto.
   * En legitim användare har utfört en ovanlig borttagning.

* **Ladda upp Azure Cloud Service-paketet**: Den här varningen anger att ett Azure Cloud Service-paket (.cspkg-fil) har överförts till ett lagringskonto på ett annorlunda sätt, jämfört med senaste aktiviteter för det här kontot. Möjliga orsaker: 
   * En angripare har förbereder att distribuera skadlig kod från ditt lagringskonto till ett Azure-molntjänst.
   * En legitim användare har förbereda för distribution av en legitim.

### <a name="suspicious-storage-activities-alerts"></a>Lagring av misstänkta aktiviteter aviseringar

* **Få åtkomst till behörigheten Ändra**: Den här varningen anger att åtkomstbehörigheterna för den här lagringsbehållaren har ändrats på ett annorlunda sätt. Möjliga orsaker: 
   * En angripare har ändrats behörigheter för behållaren att begränsa dess säkerhet.
   * En legitim användare har ändrat behörigheter för behållare.

* **Komma åt inspektion**: Den här varningen anger att åtkomstbehörigheterna för ett lagringskonto har kontrollerats på ett annorlunda sätt, jämfört med senaste aktiviteter för det här kontot. Möjliga orsaker: 
   * En angripare har utfört rekognosering för ett framtida angrepp.
   * En legitim användare har utfört Underhåll på lagringskontot.

* **Datagranskning**: Den här varningen anger att BLOB eller behållare i ett lagringskonto har räknats upp på ett annorlunda sätt, jämfört med senaste aktiviteter för det här kontot. Möjliga orsaker: 
   * En angripare har utfört rekognosering för ett framtida angrepp.
   * En legitim användare eller programlogiken har utforskat data i lagringskontot.






## <a name="next-steps"></a>Nästa steg

* Läs mer om [loggar i Azure Storage-konton](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Läs mer om [Azure Security Center](../../security-center/security-center-intro.md)
