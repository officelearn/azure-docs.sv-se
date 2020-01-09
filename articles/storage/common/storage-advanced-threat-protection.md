---
title: Konfigurera Avancerat skydd
titleSuffix: Azure Storage
description: Konfigurera Avancerat skydd för Azure Storage för att identifiera avvikelser i konto aktivitet och få meddelanden om potentiellt skadliga försök att komma åt ditt konto.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 8efbf2107104f31bcfc5e4e5669dbed1835a034b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457406"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Konfigurera Avancerat skydd för Azure Storage

Avancerat skydd för Azure Storage ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Med det här skydds lagret kan du åtgärda hot utan att vara säkerhets expert eller hantera säkerhets övervaknings system.

Säkerhets varningar utlöses när avvikelser i aktivitet inträffar. Dessa säkerhets aviseringar är integrerade med [Azure Security Center](https://azure.microsoft.com/services/security-center/)och skickas också via e-post till prenumerations administratörer med information om misstänkt aktivitet och rekommendationer om hur du undersöker och åtgärdar hot.

> [!NOTE]
> Avancerat skydd för Azure Storage är för närvarande endast tillgängligt för Blob Storage. Den är inte tillgänglig i moln regioner i Azure myndigheter och suveräna regioner. För pris information, inklusive en kostnads fri 30-dagars utvärderings version, se [sidan Azure Security Center prissättning]( https://azure.microsoft.com/pricing/details/security-center/).

Avancerat skydd för Azure Storage matar in diagnostikloggar för Läs-, skriv-och borttagnings förfrågningar till Blob Storage för hot identifiering. Om du vill undersöka aviseringar från Avancerat skydd kan du Visa relaterad lagrings aktivitet med hjälp av Lagringsanalys loggning. Mer information finns i **Konfigurera loggning** i [övervaka ett lagrings konto i Azure Portal](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Konfigurera Avancerat skydd

Avancerat skydd är aktiverat för ditt lagrings konto som standard. Du kan konfigurera Avancerat skydd på flera sätt, som beskrivs i följande avsnitt.

### <a name="portaltabazure-portal"></a>[Portalen](#tab/azure-portal)

1. Starta [Azure Portal](https://portal.azure.com/).
1. Navigera till ditt Azure Storage-konto. Under **Inställningar**väljer du **avancerad säkerhet**.
1. Välj länken **Inställningar** på sidan Avancerad säkerhets konfiguration.
1. Ange **avancerad säkerhet** till **på**.
1. Klicka på **Spara** för att spara den nya eller uppdaterade principen.

    ![Aktivera Azure Storage Avancerat skydd](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-centertabazure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

När du prenumererar på standard nivån i Azure Security Center konfigureras Avancerat skydd automatiskt på alla dina lagrings konton. Du kan aktivera eller inaktivera Avancerat skydd för dina lagrings konton under en speciell prenumeration på följande sätt:

1. Starta **Azure Security Center** i [Azure Portal](https://portal.azure.com).
1. Klicka på **priser & inställningar**på huvud menyn.
1. Klicka på den prenumeration som du vill aktivera eller inaktivera hot skydd för dess lagrings konton.

    ![Välj prenumeration](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Klicka på **pris nivå**.
1. I avsnittet **Välj pris nivå efter resurs typ** klickar du på **aktive** rad eller **inaktive**rad på raden **lagrings konton** .

    ![Aktivera ATP i Security Center](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Klicka på **Spara**.

### <a name="templatetabtemplate"></a>[Mall](#tab/template)

Använd en Azure Resource Manager mall för att distribuera ett Azure Storage konto med avancerat skydd aktiverat. Mer information finns i [lagrings konto med avancerat skydd](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policytabazure-policy"></a>[Azure Policy](#tab/azure-policy)

Använd en Azure Policy för att aktivera avancerat skydd mellan lagrings konton under en speciell prenumeration eller resurs grupp.

1. Starta sidan Azure **policy-definitions** .

1. Sök efter principen **distribuera Avancerat skydd på lagrings konton** .

     ![Sök princip](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Välj en Azure-prenumeration eller resurs grupp.

    ![Välj prenumeration eller grupp](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Tilldela principen.

    ![Sidan princip definitioner](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-apitabrest-api"></a>[REST-API](#tab/rest-api)

Använd REST API-kommandon för att skapa, uppdatera eller Hämta inställningen för avancerat skydd för ett angivet lagrings konto.

* [Avancerat skydd – skapa](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Avancerat skydd – Hämta](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Använd följande PowerShell-cmdletar:

* [Aktivera avancerat skydd](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Hämta Avancerat skydd](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Inaktivera Avancerat skydd](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Utforska säkerhets avvikelser

När avvikelser i lagrings aktiviteter inträffar får du ett e-postmeddelande med information om den misstänkta säkerhets händelsen. Information om händelsen är:

* Avvikelsens karaktär
* Lagringskontots namn
* Händelse tiden
* Lagrings typ
* Möjliga orsaker
* Undersöknings stegen
* Reparations stegen

E-postmeddelandet innehåller också information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet.

![Azure Storage e-postadress för avancerat skydds avisering](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Du kan granska och hantera dina aktuella säkerhets aviseringar från Azure Security Center [rutan säkerhets aviseringar](../../security-center/security-center-managing-and-responding-alerts.md). Om du klickar på en enskild avisering visas information och åtgärder för att undersöka det aktuella hotet och åtgärda framtida hot.

![Azure Storage e-postadress för avancerat skydds avisering](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Skydds aviseringar

Aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. En lista över aviseringar för Azure Storage finns i avsnittet **lagring** i [hot identifiering för data tjänster i Azure Security Center](../../security-center/security-center-alerts-data-services.md#azure-storage) aviseringar

## <a name="next-steps"></a>Nästa steg

* Läs mer om [loggar i Azure Storage-konton](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Läs mer om [Azure Security Center](../../security-center/security-center-intro.md)
