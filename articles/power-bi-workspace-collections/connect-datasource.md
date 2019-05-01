---
title: Ansluta till en datakälla i Power BI-Arbetsytesamlingar | Microsoft Docs
description: Lär dig hur du ansluter till en datakälla i Power BI-Arbetsytesamlingar.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 721458c5725e912d801b307ac05f3fde0776580e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708759"
---
# <a name="connect-to-a-data-source"></a>Anslut till en datakälla

Med **Power BI-Arbetsytesamlingar**, du kan bädda in rapporter i dina egna appar. När du bäddar in en Power BI-rapport i din app rapporten ansluter till underliggande data för **importera** en kopia av data eller av **ansluta direkt** till datakällan med **DirectQuery** .

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Detta är skillnaderna mellan **Importera** och **DirectQuery**.

| Importera | DirectQuery |
| --- | --- |
| Tabeller, kolumner *och* importeras eller kopieras till rapportens datauppsättning. Om du vill se ändringar som gjorts i underliggande data, måste du uppdatera eller importera en fullständig, aktuell datauppsättning igen. |Endast *tabeller och kolumner* importeras eller kopieras till rapportens datauppsättning. Du kan alltid visa aktuella data. |

Med Power BI-Arbetsytesamlingar, du kan använda DirectQuery med molndatakällor men inte lokala datakällor just nu.

> [!NOTE]
> Den lokala Datagatewayen stöds inte med Power BI-Arbetsytesamlingar just nu. Det innebär att du inte kan använda DirectQuery med lokala datakällor.

## <a name="supported-data-sources"></a>Datakällor som stöds

**DirectQuery**
* Azure SQL-databas
* Azure SQL Data Warehouse

**Importera**

Du kan importera med hjälp av alla tillgängliga datakällor i Power BI Desktop. Kommer du att **inte** att kunna uppdatera dessa data i Power BI-Arbetsytesamlingar. Du måste ladda upp ändringar till din PBIX-fil till Power BI-Arbetsytesamlingar. Detta beror inte någon tillgänglig gateway.

## <a name="benefits-of-using-directquery"></a>Fördelarna med att använda DirectQuery

Det finns två primära fördelar när du använder **DirectQuery**:

* **DirectQuery** kan du skapa visualiseringar över stora datauppsättningar, där det annars skulle vara ohållbart att först importera alla data.
* Ändringar i underliggande data kan kräva en uppdatering av data och för vissa rapporter kan behovet av att visa aktuella data kräva stora dataöverföringar, vilket gör återinförande data inte lämpligt. Däremot **DirectQuery** rapporter använder alltid aktuella data.

## <a name="limitations-of-directquery"></a>Begränsningar hos DirectQuery

Det finns några begränsningar med att använda **DirectQuery**:

* Alla tabeller måste komma från en enskild databas.
* Om frågan är alltför komplex uppstår ett fel. Du kan åtgärda felet måste du omstrukturerar frågan så att det är mindre komplex. Om frågan måste vara komplexa, måste du importera data istället för att använda **DirectQuery**.
* Filtrering av relationen är begränsad till en enda riktning i stället för i båda riktningarna.
* Du kan inte ändra datatypen för en kolumn.
* Som standard begränsningar DAX-uttryck i åtgärder. Se [DirectQuery och mått](#measures).

<a name="measures"/>

## <a name="directquery-and-measures"></a>DirectQuery och mått
För att säkerställa att frågor som skickats till den underliggande datakällan har acceptabel prestanda, gäller begränsningar för mått. När du använder **Power BI Desktop**, avancerade användare kan välja att kringgå den här begränsningen genom att välja **fil > Alternativ och inställningar > alternativ**. I den **alternativ** dialogrutan Välj **DirectQuery**, och väljer alternativet **Tillåt obegränsade åtgärder i DirectQuery-läge**. När det alternativet väljs, kan du använda DAX-uttryck som är giltig för ett mått. Användarna måste vara medvetna; dock att vissa uttryck som fungerar bra när data importeras kan resultera i långsamt frågor till serverdelen datakällan när i **DirectQuery** läge. 

## <a name="see-also"></a>Se även

* [Kom igång med Microsoft Power BI-Arbetsytesamlingar](get-started.md)
* [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

Fler frågor? [Försök med Power BI Community](https://community.powerbi.com/)