---
title: "Ansluter till en datakälla i Power BI arbetsytan samlingar | Microsoft Docs"
description: "Lär dig hur du ansluter till en datakälla i Power BI arbetsytan samlingar."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 2a4caeb3-255d-4215-9554-0ca8e3568c13
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 24600c4343e3bfebe14f25020c5a7ba02d15af64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-data-source"></a>Anslut till en datakälla

Med **Power BI arbetsytan samlingar**, du kan bädda in rapporter i din egen app. När du bäddar in en Power BI-rapport i din app rapporten ansluter till underliggande data för **importera** en kopia av data eller av **ansluta direkt** till datakällan med **DirectQuery** .

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Detta är skillnaderna mellan **Importera** och **DirectQuery**.

| Importera | DirectQuery |
| --- | --- |
| Tabeller, kolumner *och data* importeras eller kopieras till rapportens dataset. Om du vill se ändringar som gjorts i underliggande data måste du uppdatera eller importera en fullständig, aktuell datauppsättning igen. |Endast *tabeller och kolumner* importeras eller kopieras till rapportens dataset. Du kan alltid visa aktuella data. |

Med Power BI arbetsytan samlingar, du kan använda DirectQuery med datakällor i molnet men inte lokala datakällor just nu.

> [!NOTE]
> Lokala Data Gateway stöds inte med Power BI arbetsytan samlingar just nu. Det innebär att du inte kan använda DirectQuery med lokala datakällor.

## <a name="supported-data-sources"></a>Datakällor som stöds

**DirectQuery**
* Azure SQL-databas
* Azure SQL Data Warehouse

**Importera**

Du kan importera med hjälp av alla tillgängliga datakällor i Power BI Desktop. Du kommer **inte** att kunna uppdatera data i Power BI arbetsytan samlingar. Du måste överföra ändringarna till PBIX-fil till Power BI arbetsytan samlingar. Detta beror på någon tillgänglig gateway. 

## <a name="benefits-of-using-directquery"></a>Fördelarna med att använda DirectQuery

Det finns två primära fördelar när du använder **DirectQuery**:

* **DirectQuery** kan du skapa visualiseringar över stora datamängder, om det annars skulle vara unfeasible på första importera alla data.
* Underliggande data ändras kan kräva en uppdatering av data och för vissa rapporter behöver visar aktuella data kan kräva stora dataöverföringar, vilket gör återinförande data unfeasible. Däremot **DirectQuery** rapporter alltid använda aktuella data.

## <a name="limitations-of-directquery"></a>Begränsningar av DirectQuery

Det finns några begränsningar med att använda **DirectQuery**:

* Alla tabeller måste komma från en enskild databas.
* Om frågan är alltför komplex, uppstår ett fel. Du kan åtgärda felet måste du refactor frågan så att den är mindre komplex. Om frågan måste vara komplexa, måste du importera data istället för att använda **DirectQuery**.
* Filtrering av relationen är begränsad till en riktning i stället för i båda riktningarna.
* Du kan inte ändra datatypen för en kolumn.
* Som standard begränsningar DAX-uttryck som tillåts i mått. Se [DirectQuery åtgärder och](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery och åtgärder
För att säkerställa frågor skickas till den underliggande datakällan har acceptabel prestanda, gäller begränsningar för åtgärder. När du använder **Power BI Desktop**, avancerad användare kan du kringgå den här begränsningen genom att välja **fil > Alternativ och inställningar > alternativ**. I den **alternativ** dialogrutan Välj **DirectQuery**, och markera alternativet **tillåta obegränsad åtgärder i DirectQuery-läge**. När alternativet väljs, kan du använda DAX-uttryck som gäller för ett mått. Användare måste vara medvetna om; men att vissa uttryck som utför även när data importeras kan resultera i långsamt frågor till serverdelen datakälla när i **DirectQuery** läge. 

## <a name="see-also"></a>Se även

* [Kom igång med Microsoft Power BI arbetsytan samlingar](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Fler frågor? [Försök med Power BI Community](http://community.powerbi.com/)

