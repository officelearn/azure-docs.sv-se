---
title: Data Lake Store-utdata för Azure Stream Analytics
description: Konfigurationen av autentisering och auktorisering av ett Azure Data Lake Store i ett Stream Analytics-jobb
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: a0586b32fd12744c8bfce782583cdc4078979ef1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="stream-analytics-data-lake-store-output"></a>Stream Analytics Data Lake Store-utdata
Stream Analytics-jobb stöder flera metoder för utdata, en som en [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store är en företagsomfattande storskalig lagringsplats för analytiska arbetsbelastningar för stordata. Data Lake Store kan du lagra data med en storlek, typ och införandet hastighet för drifts- och undersökande analyser.

## <a name="authorize-a-data-lake-store-account"></a>Godkänna ett Data Lake Store-konto
1. När Data Lake Store är markerad som utdata i Azure-portalen, uppmanas du att tillåta användning av din befintliga Data Lake Store eller begära åtkomst till Data Lake Store.
   
   ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  
   
2. Om du redan har åtkomst till Data Lake Store, klicka på ”Verifiera nu” och under en kort tid en sida visas som anger ”omdirigera tillstånd”. Sidan stängs automatiskt och visas med sidan som du kan konfigurera Data Lake Store-utdata.

Om du inte har registrerat dig för Data Lake Store kan du följa länken ”logga nu” för att initiera begäran eller följa den [igång instruktioner](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="configure-the-data-lake-store-output-properties"></a>Konfigurera egenskaper för Data Lake Store-utdata
När du har Data Lake Store-kontot autentiseras kan du konfigurera egenskaper för Data Lake Store-utdata. Tabellen nedan är listan över egenskapsnamn och deras beskrivning för att konfigurera din Data Lake Store-utdata.

<table>
<tbody>
<tr>
<td><B>EGENSKAPSNAMN</B></td>
<td><B>BESKRIVNING</B></td>
</tr>
<tr>
<td>Kolumnalias</td>
<td>Detta är ett eget namn som används i frågor för att dirigera utdata till denna Data Lake Store.</td>
</tr>
<tr>
<td>Data Lake Store-konto</td>
<td>Namnet på det lagringskonto där du skickar din utdata. Visas en lista med Data Lake Store-konton som den inloggade användaren har åtkomst till.</td>
</tr>
<tr>
<td>Prefixet sökvägar [<I>valfria</I>]</td>
<td>Den filsökväg som används för att skriva filer i den angivna Data Lake Store-konto. <BR>{date} {time}<BR>Exempel 1: mapp1/logs / {date} / {time}<BR>Exempel 2: mapp1/logs / {date}</td>
</tr>
<tr>
<td>Datumformat [<I>valfria</I>]</td>
<td>Du kan välja datumformat där filerna ordnas om datumtoken används i sökvägen till prefix. Exempel: ÅÅÅÅ/MM/DD</td>
</tr>
<tr>
<td>Tidsformat [<I>valfria</I>]</td>
<td>Ange tidsformat där filerna ordnas om tid token används i sökvägen till prefix. Det enda värdet som stöds är för närvarande HH.</td>
</tr>
<tr>
<td>Händelsen serialiseringsformat</td>
<td>Serialiseringsformat för utdata. JSON-, CSV- och Avro stöds.</td>
</tr>
<tr>
<td>Encoding</td>
<td>Om CSV- eller JSON-format, måste kodning anges. UTF-8 är det enda kodformat som stöds för närvarande.</td>
</tr>
<tr>
<td>Avgränsare</td>
<td>Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering CSV. Värden som stöds är kommatecken, semikolon, blanksteg, TABB och vertikalstreck.</td>
</tr>
<tr>
<td>Format</td>
<td>Gäller endast för JSON-serialisering. Radseparering innebär att utdata formateras genom att varje JSON-objekt avgränsas med en ny rad. Matrisen anger att utdata formateras som en matris av JSON-objekt.</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Förnya auktorisering för Data Lake Store
För närvarande finns en begränsning där autentiseringstoken måste uppdateras manuellt efter 90 dagar för alla jobb med Data Lake Store-utdata. Du måste också återautentisera ditt Data Lake Store-konto om du har ändrat ditt lösenord eftersom utskriftsjobbet skapades eller senast autentiserad. Ett symtom på det här problemet är inga jobbutdata och ett fel i loggarna åtgärden som anger behovet av återauktorisering.

Lös problemet genom att stoppa körs jobbet och gå till din Data Lake Store-utdata. Klicka på länken ”förnya auktorisering” och under en kort tid en sida visas som anger ”omdirigera tillstånd..”. Sidan stängs automatiskt och om detta lyckas visar ”tillstånd har förnyats”. Du sedan behöver klicka på ”Spara” längst ned på sidan och kan fortsätta genom att starta om jobbet från stoppats senast att undvika dataförlust.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)

