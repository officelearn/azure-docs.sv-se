---
title: Övervaka dina säkerhetskopior med Säkerhetskopieringsutforskaren
description: I den här artikeln beskrivs hur du använder Backup Explorer för att utföra realtidsövervakning av säkerhetskopior över valv, prenumerationer, regioner och klienter.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fa30a061dfe0d9f7721bd2405280f8a01bea87fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131798"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Övervaka dina säkerhetskopior med Säkerhetskopieringsutforskaren

När organisationer säkerhetskopierar fler och fler datorer till molnet blir det allt viktigare att övervaka dessa säkerhetskopior effektivt. Det bästa sättet att börja är att använda en central plats för att visa operativ information över en stor egendom.

Backup Explorer är en inbyggd Azure Monitor-arbetsbok som ger Azure Backup-kunder den här enda, centrala platsen. Backup Explorer hjälper dig att övervaka operativa aktiviteter över hela säkerhetskopieringsegenskapen på Azure, som spänner över klienter, platser, prenumerationer, resursgrupper och valv. I stort sett har Säkerhetskopieringsutforskaren följande funktioner:

* **I skala perspektiv:** Få en aggregerad vy över säkerhetskopieringsobjekt, jobb, aviseringar, principer och resurser som ännu inte är konfigurerade för säkerhetskopiering över hela dödsboet. 
* **Analys av detaljgranskning:** Visa detaljerad information om vart och ett av dina jobb, aviseringar, principer och säkerhetskopieringsobjekt, allt på ett ställe.
* **Användbara gränssnitt:** När du har identifierat ett problem kan du lösa det genom att gå sömlöst till det relevanta säkerhetskopieringsobjektet eller Azure-resursen.

Dessa funktioner tillhandahålls direkt från inbyggt integrering med Azure Resource Graph- och Azure Monitor-arbetsböcker.

> [!NOTE]
> * Säkerhetskopiering Explorer är för närvarande endast tillgängligt för Azure-virtuella datorer (VMs) data.
> * Backup Explorer är tänkt att vara en operativ instrumentpanel för att visa information om dina säkerhetskopior under de senaste 7 dagarna (max).
> * Backup Explorer stöds för närvarande inte i nationella moln.
> * Det går inte att anpassa mallen Säkerhetskopiering Explorer. 
> * Vi rekommenderar inte att du skriver anpassade automatiseringar på Azure Resource Graph-data.

## <a name="get-started"></a>Komma igång

Du kan komma åt Säkerhetskopieringsutforskaren genom att gå till något av dina Recovery Services-valv och välja länken **Backup Explorer** i **fönstret Översikt.**

![Snabblänk för Arkiv](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Om du väljer länken öppnas Säkerhetskopieringsutforskaren, som ger en samlad vy över alla valv och prenumerationer som du har åtkomst till. Om du använder ett Azure Lighthouse-konto kan du visa data för alla klienter som du har åtkomst till. Mer information finns i avsnittet "Vy över flera innehavare" i slutet av den här artikeln.

![Målsida för Säkerhetskopieringsutforskaren](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Användningsfall för säkerhetskopian

Säkerhetskopieringsutforskaren visar flera flikar, som var och en ger detaljerad information om en viss säkerhetskopia (till exempel ett säkerhetskopieringsobjekt, jobb eller princip). Det här avsnittet innehåller en kort översikt över var och en av flikarna. Videorna innehåller exempel användningsfall för varje säkerhetskopia artefakt, tillsammans med beskrivningar av tillgängliga kontroller.

### <a name="the-summary-tab"></a>Fliken Sammanfattning

Fliken **Sammanfattning** ger en snabb överblick över det allmänna tillståndet för din säkerhetskopia. Du kan till exempel visa antalet objekt som skyddas, antalet objekt för vilka skydd inte har aktiverats eller hur många jobb som har lyckats under de senaste 24 timmarna.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>Fliken Säkerhetskopieringsobjekt

Du kan filtrera och visa var och en av dina säkerhetskopior efter prenumeration, valv och andra egenskaper. Genom att välja namnet på ett säkerhetskopieringsobjekt kan du öppna Azure-fönstret för det objektet. Från tabellen kan du till exempel konstatera att den senaste säkerhetskopian misslyckades för objekt *X*. Genom att välja *X*kan du öppna objektets **säkerhetskopieringsfönster,** där du kan utlösa en säkerhetskopiering på begäran.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>Fliken Jobb

Välj fliken **Jobb** om du vill visa information om alla jobb som har utlösts under de senaste sju dagarna. Här kan du filtrera efter *projektoperation,* *jobbstatus*och *felkod* (för misslyckade jobb).


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>Fliken Aviseringar

Välj fliken **Aviseringar** om du vill visa information om alla aviseringar som har genererats i valven under de senaste sju dagarna. Du kan filtrera aviseringar efter typ (*Säkerhetskopieringsfel* eller *Återställningsfel*), aktuell status (*Aktiv* eller *Löst*) och allvarlighetsgrad (*Kritisk*, *Varning*eller *Information*). Du kan också välja en länk för att gå till Azure VM och vidta nödvändiga åtgärder.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>Fliken Principer

Du kan välja fliken **Principer** om du vill visa viktig information om alla principer för säkerhetskopiering som har skapats i din säkerhetskopia. Du kan visa antalet objekt som är associerade med varje princip, tillsammans med kvarhållningsintervallet och säkerhetskopieringsfrekvensen som anges av principen.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>Fliken Säkerhetskopiering är inte aktiverad

Säkerhetskopiering bör aktiveras för alla datorer som behöver skydd. Med Backup Explorer kan säkerhetskopieringsadministratörer snabbt identifiera vilka datorer i en organisation som ännu inte skyddas av säkerhetskopiering. Om du vill visa den här informationen väljer du fliken **Säkerhetskopiering inte aktiverad.**

I fönstret **Säkerhetskopiering visas** en tabell med en lista över oskyddade datorer. Din organisation kan tilldela olika taggar till produktionsdatorer och testdatorer, eller till datorer som betjänar en mängd olika funktioner. Eftersom varje klass av datorer behöver en separat princip för säkerhetskopiering, hjälper filtrering efter taggar dig att visa information som är specifik för varje. Om du väljer namnet på en dator omdirigeras du till den datorns **fönstret Konfigurera säkerhetskopiering,** där du kan välja att tillämpa en lämplig princip för säkerhetskopiering.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Exportera till Excel

Du kan exportera innehållet i en tabell eller ett diagram som ett Excel-kalkylblad. Innehållet exporteras som det är, med dina befintliga filter tillämpade. Om du vill exportera ytterligare tabellrader kan du öka antalet rader som ska visas på sidan med hjälp av **listrutan Rader per sida** högst upp på varje flik.

## <a name="pin-to-the-dashboard"></a>Fäst på instrumentpanelen

Du kan välja "pin"-ikonen högst upp i varje tabell eller diagram för att fästa den på instrumentpanelen för Azure-portalen. Genom att fästa den här informationen kan du skapa en anpassad instrumentpanel som är skräddarsydd för att visa den information som är viktigast för dig.

## <a name="cross-tenant-views"></a>Visningar mellan klienter

Om du är en Azure Lighthouse-användare med delegerad åtkomst till prenumerationer i flera klientmiljöer kan du använda standardprenumerationsfiltret. Du visar de prenumerationer som du vill visa data för genom att välja "filter"-ikonen längst upp till höger i Azure-portalen. När du använder den här funktionen sammanställer Säkerhetskopieringsutforskaren information om alla valv i dina valda prenumerationer. Mer information finns i [Vad är Azure Lighthouse?](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du använder Azure Monitor för att få insikter om dina säkerhetskopierade data](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
