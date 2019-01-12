---
title: Faktisk Slutanvändarmätning till Azure Traffic Manager med webbsidor | Microsoft Docs
description: Konfigurera dina webbsidor för att skicka faktisk Slutanvändarmätning till Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: c994190ed4ccbd8e5b90f17b70ec6b65477fb0d8
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232013"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Hur du skickar Real User Measurements till Azure Traffic Manager med webbsidor

Du kan konfigurera dina webbsidor för att skicka faktisk Slutanvändarmätning till Traffic Manager genom att skaffa en Real User Measurements (ROM)-nyckel och bädda in den genererade koden till webbsidan.

## <a name="obtain-a-real-user-measurements-key"></a>Få en Real User Measurements-nyckel

Mätningar du ta och skicka till Traffic Manager från klientprogrammet identifieras av tjänsten med hjälp av en unik sträng som kallas den **Real User Measurements (ROM) nyckel**. Du kan få en köra nyckel med hjälp av Azure portal, REST-API eller med hjälp av PowerShell eller Azure CLI.

Du kan hämta den ROM-nyckeln med hjälp av Azure portal:
1. Logga in på Azure portal från en webbläsare. Om du inte redan har ett konto kan registrera du dig för en kostnadsfri utvärderingsversion för en månad.
2. I portalens sökfältet söker du efter namnet på Traffic Manager-profilen som du vill ändra och klicka sedan på Traffic Manager-profilen i resultaten som visas.
3. I profilbladet Traffic Manager-klickar du på **Real User Measurements** under **inställningar**.
4. Klicka på **Generera nyckel** att skapa en ny ROM-nyckel.
 
  ![Generera Real User Measurements nyckel](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Bild 1: Real User Measurements nyckelgenerering**

5. Bladet visar nu ROM-nyckeln som genererades och ett JavaScript-kodfragment som krävs för att bädda in HTML-sidan.
 
    ![JavaScript-kod för Real User Measurements nyckel](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Bild 2: Real User Measurements nyckel och JavaScript-mått**
 
6.  Klicka på den **kopiera** för att kopiera JavaScript-koden. 

>[!IMPORTANT]
> Använda det genererade JavaScriptet för Real User Measurements funktionen ska fungera korrekt. Ändringar i det här skriptet eller skript som används av Real User Measurements kan leda till oväntade funktionssätt.

## <a name="embed-the-code-to-an-html-web-page"></a>Bädda in kod till en HTML-webbsida

När du har fått nyckeln köra, är nästa steg att bädda in den här kopierade JavaScript i en HTML-sida som användarna besöker. Redigera HTML kan göras på många sätt och med hjälp av olika verktyg och arbetsflöden. Det här exemplet visar hur du uppdaterar en HTML-sida för att lägga till det här skriptet. Du kan använda den här vägledningen för att anpassa det till HTML-källa management-arbetsflöde.

1.  Öppna HTML-sidan i en textredigerare
2.  Klistra in JavaScript-koden som du hade kopierat det tidigare steget i avsnittet BRÖDTEXT i HTML (den kopierade koden är på rad 8 och 9, se figur 3).
 
    ![Bädda in Javascript-kod i webbsida för Real User Measurements](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Bild 3: Enkel HTML-kod med inbäddade riktiga användare mätningar av JavaScript**

3.  Spara HTML-fil och värden som den på en webbserver ansluten till internet. 
4. Nästa gång den här sidan återges i en webbläsare, JavaScript som refererar till hämtas och skriptet körs mätning och rapportering av åtgärder.


## <a name="next-steps"></a>Nästa steg
- Läs mer om [Real User Measurements](traffic-manager-rum-overview.md)
- Lär dig [så här fungerar Traffic Manager](traffic-manager-overview.md)
- Läs mer om den [trafikroutningsmetoder](traffic-manager-routing-methods.md) stöds av Traffic Manager
- Lär dig hur du [skapa en Traffic Manager-profil](traffic-manager-create-profile.md)

