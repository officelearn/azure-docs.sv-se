---
title: Konfigurera avancerat hotskydd
titleSuffix: Azure Storage
description: Konfigurera avancerat skydd mot hot för Azure Storage för att identifiera avvikelser i kontoaktiviteten och meddelas om potentiellt skadliga försök att komma åt ditt konto.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 27860b8761c565c45a604253efdff5f77606606e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061305"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Konfigurera avancerat hotskydd för Azure Storage

Avancerat hotskydd för Azure Storage ger ett extra lager av säkerhetsinformation som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton. Med det här skyddslageret kan du hantera hot utan att vara säkerhetsexpert eller hantera säkerhetsövervakningssystem.

Säkerhetsaviseringar utlöses när avvikelser i aktivitet inträffar. Dessa säkerhetsaviseringar är integrerade med [Azure Security Center](https://azure.microsoft.com/services/security-center/)och skickas även via e-post till prenumerationsadministratörer, med information om misstänkt aktivitet och rekommendationer om hur du undersöker och åtgärdar hot.

> [!NOTE]
> Avancerat hotskydd för Azure Storage är för närvarande endast tillgängligt för Blob-lagring. Den är inte tillgänglig i Azure-myndigheter och suveräna molnregioner. Information om priser, inklusive en kostnadsfri utvärderingsversion på 30 dagar, finns på [prissidan för Azure Security Center]( https://azure.microsoft.com/pricing/details/security-center/).

Avancerat skydd mot hot för Azure Storage intar diagnostikloggar med läs-, skriv- och borttagningsbegäranden till Blob-lagring för hotidentifiering. Om du vill undersöka aviseringarna från avancerat hotskydd kan du visa relaterad lagringsaktivitet med Lagringsanalys. Mer information finns i **Konfigurera loggning** i [Övervaka ett lagringskonto i Azure-portalen](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Konfigurera avancerat hotskydd

Du kan konfigurera avancerat hotskydd på något av flera sätt, som beskrivs i följande avsnitt.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Starta [Azure-portalen](https://portal.azure.com/).
1. Navigera till ditt Azure Storage-konto. Under **Inställningar**väljer du **Avancerad säkerhet**.
1. Välj länken **Inställningar** på den avancerade säkerhetskonfigurationssidan.
1. Ställ in **avancerad säkerhet** **på PÅ**.
1. Klicka på **Spara** om du vill spara den nya eller uppdaterade principen.

    ![Aktivera avancerat hotskydd för Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

När du prenumererar på standardnivån i Azure Security Center konfigureras automatiskt avancerat hotskydd på alla dina lagringskonton. Du kan aktivera eller inaktivera avancerat hotskydd för dina lagringskonton under en viss prenumeration enligt följande:

1. Starta **Azure Security Center** i [Azure-portalen](https://portal.azure.com).
1. Klicka på Priser **& inställningar på**huvudmenyn .
1. Klicka på den prenumeration som du vill aktivera eller inaktivera hotskydd för sina lagringskonton.

    ![Välj en prenumeration](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Klicka på **Prisnivå**.
1. Klicka på Aktiverat eller **Inaktiverat**i raden **Lagringskonton** i avsnittet **Välj prisnivå efter resurstyp.** **Enabled**

    ![Aktivera ATP i Säkerhetscenter](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Klicka på **Spara**.

### <a name="template"></a>[Mall](#tab/template)

Använd en Azure Resource Manager-mall för att distribuera ett Azure Storage-konto med avancerat skydd mot hot. Mer information finns i [Lagringskonto med avancerat skydd mot hot](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Använd en Azure-princip för att aktivera avancerat skydd mot hot över lagringskonton under en viss prenumeration eller resursgrupp.

1. Starta sidan Azure **Policy - Definitioner.**

1. Sök efter principen **Distribuera avancerat skydd mot skydd för lagringskonton.**

     ![Sökpolicy](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Välj en Azure-prenumeration eller resursgrupp.

    ![Välj prenumeration eller grupp](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Tilldela principen.

    ![Sidan Principdefinitioner](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Använd Rest API-kommandon för att skapa, uppdatera eller hämta den avancerade inställningen för skydd mot hot för ett visst lagringskonto.

* [Avancerat hotskydd - Skapa](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Avancerat skydd mot hot - Få](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Använd följande PowerShell-cmdlets:

* [Aktivera avancerat hotskydd](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Få avancerat hotskydd](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Inaktivera avancerat hotskydd](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Utforska säkerhetsavvikelser

När avvikelser i lagringsaktiviteten inträffar får du ett e-postmeddelande med information om den misstänkta säkerhetshändelsen. Information om evenemanget inkluderar:

* Anomalins natur
* Lagringskontots namn
* Händelsetiden
* Lagringstypen
* De potentiella orsakerna
* Utredningen kliver
* Reparationsstegen

E-postmeddelandet innehåller också information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minska det potentiella hotet.

![E-postmeddelande med avancerad hotskyddsavisering i Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Du kan granska och hantera dina aktuella säkerhetsaviseringar från [panelen Säkerhetsaviseringar i](../../security-center/security-center-managing-and-responding-alerts.md)Azure Security Alert. Genom att klicka på en specifik avisering finns information och åtgärder för att undersöka det aktuella hotet och ta itu med framtida hot.

![E-postmeddelande med avancerad hotskyddsavisering i Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Säkerhetsaviseringar

Aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton. En lista över aviseringar för Azure Storage finns i avsnittet **Lagring** i [Skydd mot hot för datatjänster i Azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [loggar i Azure Storage-konton](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Läs mer om [Azure Security Center](../../security-center/security-center-intro.md)
