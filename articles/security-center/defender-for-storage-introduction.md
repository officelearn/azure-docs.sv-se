---
title: Azure Defender för lagring – fördelar och funktioner
description: Lär dig mer om fördelarna och funktionerna i Azure Defender för lagring.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 9b2855f82927b6f1707fd748f097dd357818ac4b
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341982"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Introduktion till Azure Defender för lagring

**Azure Defender för lagring** identifierar potentiellt skadlig aktivitet på dina Azure Storage-konton. Dina data kan skyddas oavsett om de lagras som BLOB-behållare, fil resurser eller data sjöar.

Det här skydds lagret gör att du kan åtgärda hot *utan* att du behöver vara säkerhets expert, och hjälper dig att hantera dina säkerhets övervaknings system.


## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmänt tillgänglig (GA)|
|Priset|**Azure Defender för lagring** faktureras så som visas på [sidan med priser](security-center-pricing.md)|
|Skyddade lagrings typer:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Nej](./media/icons/no-icon.png) Kina gov, andra gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Vilken typ av aviseringar tillhandahåller Azure Defender för lagring?

Säkerhets aviseringar utlöses när det finns:

- **Misstänkt aktivitet** – till exempel har lagrings kontot åtkomst till från en IP-adress som kallas en aktiv stängningsmodul för Tor
- **Avvikande beteende** – till exempel ändringar i åtkomst mönstret till ett lagrings konto
- **Potentiell överförd skadlig kod** – identifierad hash-rykte visar att en överförd fil innehåller skadlig kod

Aviseringar innehåller information om den incident som utlöste dem, samt rekommendationer om hur du undersöker och åtgärdar hot.

> [!TIP]
> Du kan simulera lagrings aviseringar genom att följa anvisningarna i [det här blogg inlägget](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Vad är hash-ryktes analys för skadlig kod?

För att avgöra om en uppladdad fil är misstänkt använder Azure Defender för lagring hash-ryktes analys som stöds av [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Skydds verktygen genomsöker inte de överförda filerna, i stället för att kontrol lera lagrings loggarna och jämföra hash-värdena för nyligen överförda filer med kända virus, trojanska hästar, spionprogram och utpressnings tro Jan. 

När en fil misstänks innehålla skadlig kod visar Security Center en avisering och kan välja att e-posta lagrings ägaren för godkännande för att ta bort den misstänkta filen. Om du vill ställa in automatisk borttagning av filer som visar att hash-ryktes analys innehåller skadlig kod kan du distribuera en [arbets flödes automatisering för att utlösa aviseringar som innehåller "potentiell skadlig kod som överförs till ett lagrings konto"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Om du vill aktivera Security Center hot skydds funktioner måste du aktivera Azure Defender på prenumerationen som innehåller de aktuella arbets belastningarna.
>
> Du kan aktivera **Azure Defender för lagring** på antingen prenumerations nivån eller resurs nivån.



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure Defender för lagring.

Information om relaterade material finns i följande artiklar: 

- Om en avisering genereras av Security Center eller tas emot av Security Center från en annan säkerhets produkt, kan du exportera den. Om du vill exportera aviseringar till Azure Sentinel, SIEM eller andra externa verktyg, följer du anvisningarna i [Exportera aviseringar till en Siem](continuous-export.md).
- [Så här aktiverar du avancerad Defender för lagring](../storage/common/azure-defender-storage-configure.md)
- [Listan över Azure Defender för lagrings aviseringar](alerts-reference.md#alerts-azurestorage)
- [Microsofts funktioner för hot information](https://go.microsoft.com/fwlink/?linkid=2128684)