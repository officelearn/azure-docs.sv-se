---
title: Konfigurera Avancerat skydd
titleSuffix: Azure Storage
description: Konfigurera Avancerat skydd för Azure Storage för att identifiera avvikelser i konto aktivitet och få meddelanden om potentiellt skadliga försök att komma åt ditt konto.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: e037607d1f86e6df4d3f5b12e29ba8fde447ebc9
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757939"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Konfigurera Avancerat skydd för Azure Storage

Avancerat skydd för Azure Storage ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Med det här skydds lagret kan du åtgärda hot utan att vara säkerhets expert eller hantera säkerhets övervaknings system.

Säkerhets varningar utlöses när avvikelser i aktivitet inträffar. Dessa säkerhets aviseringar är integrerade med [Azure Security Center](https://azure.microsoft.com/services/security-center/)och skickas också via e-post till prenumerations administratörer med information om misstänkt aktivitet och rekommendationer om hur du undersöker och åtgärdar hot.

Tjänsten matar in resurs loggar med Läs-, skriv-och borttagnings begär anden till Blob Storage och Azure Files (för hands version) för hot identifiering. Om du vill undersöka aviseringar från Avancerat skydd kan du Visa relaterad lagrings aktivitet med hjälp av Lagringsanalys loggning. Mer information finns i **Konfigurera loggning** i [övervaka ett lagrings konto i Azure Portal](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Tillgänglighet

Avancerat skydd för Azure Storage är för närvarande tillgängligt för Blob Storage, Azure Files (för hands version) och Azure Data Lake Storage Gen2 (för hands version). Konto typer som har stöd för avancerat skydd inkluderar General-Purpose v2, Block Blob och Blob Storage-konton. Avancerat skydd är tillgängligt i alla offentliga moln och i amerikanska myndigheter, men inte i andra suveräna eller Azure Government moln regioner.

Konton med hierarkiska namn rymder aktiverade för Data Lake Storage stöd transaktioner med både Azure Blob Storage-API: er och Data Lake Storage-API: er. Azure-filresurser stöder transaktioner över SMB.

För pris information, inklusive en kostnads fri 30-dagars utvärderings version, se [sidan Azure Security Center prissättning](https://azure.microsoft.com/pricing/details/security-center/).

I följande lista sammanfattas tillgänglighet för avancerat skydd för Azure Storage:

- Versions tillstånd:
  - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (allmän tillgänglighet)
  - [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (för hands version stöder SMB-och rest-transaktioner)
  - Azure Data Lake Storage Gen2 (för hands version)
- Moln<br>
    ✔ Kommersiella moln<br>
    ✔ US Gov<br>
    ✘ Kina gov, andra gov

## <a name="set-up-advanced-threat-protection"></a>Konfigurera Avancerat skydd

Du kan konfigurera Avancerat skydd på flera sätt, som beskrivs i följande avsnitt.

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

När du prenumererar på standard nivån i Azure Security Center konfigureras Avancerat skydd automatiskt på alla dina lagrings konton. Du kan aktivera eller inaktivera Avancerat skydd för dina lagrings konton under en speciell prenumeration på följande sätt:

1. Starta **Azure Security Center** i [Azure Portal](https://portal.azure.com).
1. Klicka på **priser & inställningar**på huvud menyn.
1. Klicka på den prenumeration som du vill aktivera eller inaktivera hot skydd för dess lagrings konton.

    ![Välj en prenumeration](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Klicka på **pris nivå**.
1. I avsnittet **Välj pris nivå efter resurs typ** klickar du på **aktive** rad eller **inaktive**rad på raden **lagrings konton** .

    ![Aktivera ATP i Security Center](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Klicka på **Spara**.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Starta [Azure Portal](https://portal.azure.com/).
1. Navigera till ditt Azure Storage-konto. Under **Inställningar**väljer du **avancerad säkerhet**.
1. Välj länken **Inställningar** på sidan Avancerad säkerhets konfiguration.
1. Ange **avancerad säkerhet** till **på**.
1. Klicka på **Spara** för att spara den nya eller uppdaterade principen.

    ![Aktivera Azure Storage Avancerat skydd](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="template"></a>[Mall](#tab/template)

Använd en Azure Resource Manager mall för att distribuera ett Azure Storage konto med avancerat skydd aktiverat. Mer information finns i [lagrings konto med avancerat skydd](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Använd en Azure Policy för att aktivera avancerat skydd mellan lagrings konton under en speciell prenumeration eller resurs grupp.

1. Starta sidan Azure **policy-definitions** .

1. Sök efter principen **distribuera Avancerat skydd på lagrings konton** .

     ![Sök princip](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Välj en Azure-prenumeration eller resurs grupp.

    ![Välj prenumeration eller grupp](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Tilldela principen.

    ![Sidan princip definitioner](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST-API](#tab/rest-api)

Använd REST API-kommandon för att skapa, uppdatera eller Hämta inställningen för avancerat skydd för ett angivet lagrings konto.

* [Avancerat skydd – skapa](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Avancerat skydd – Hämta](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

## <a name="security-alerts"></a>Säkerhetsaviseringar

Aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. En lista över aviseringar för Azure Storage finns i avsnittet **lagring** i [skydd mot data tjänster i Azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [loggar i Azure Storage-konton](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Läs mer om [Azure Security Center](../../security-center/security-center-intro.md)
