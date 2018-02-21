---
title: "Verklig användare mått till Azure Traffic Manager med webbsidor | Microsoft Docs"
description: "Konfigurera webbsidor som skickar verkliga användaren mått i Traffic Manager"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: c8b345cd973b8cd939ad111132f1afdadae9f2b7
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Hur du skickar verkliga användaren mått till Azure Traffic Manager med hjälp av webbsidor

>[!NOTE]
>Funktionen verkliga användaren mått i Traffic Manager finns i Public Preview och kanske inte har samma nivå av tillgänglighet och tillförlitlighet som viktiga funktioner som är i allmänhet tillgänglighet. Funktionen stöds inte, kan ha begränsad kapacitet och kanske inte tillgänglig på alla platser i Azure. Den senaste meddelanden på tillgänglighet och status för den här funktionen, kontrollera den [Azure Traffic Manager uppdaterar](https://azure.microsoft.com/updates/?product=traffic-manager) sidan.

Du kan konfigurera webbsidorna för att skicka verkliga användaren mått i Traffic Manager genom att skaffa en verklig användare mått (ROM) nyckel och bädda in den genererade koden till webbsidan.

## <a name="obtain-a-real-user-measurements-key"></a>Skaffa en verklig användare mätningar nyckel

Mått du vidta och skicka i Traffic Manager från ditt klientprogram identifieras av tjänsten med hjälp av en unik sträng som kallas den **verkliga användaren mått (ROM) nyckeln**. Du kan få en ROM nyckeln med hjälp av Azure-portalen REST-API eller med hjälp av PowerShell eller Azure CLI.

Du kan hämta ROM nyckeln med hjälp av Azure portal:
1. Logga in på Azure-portalen från en webbläsare. Om du inte redan har ett konto kan registrera du dig för en kostnadsfri utvärderingsversion för en månad.
2. I portalens sökfältet, söker du efter Traffic Manager-profilnamn som du vill ändra och klicka sedan på Traffic Manager-profilen i resultat som visas.
3. I profilbladet Traffic Manager-klickar du på **verkliga användaren mätningar** under **inställningar**.
4. Klicka på **Generera nyckel** att skapa en ny ROM-nyckel.
 
  ![Skapa verkliga användaren mätningar nyckel](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Bild 1: Användare mätningar nyckelgenerering**

5. Bladet visar nu ROM-nyckeln genereras och ett JavaScript-kodfragment som krävs för att bädda in i HTML-sidan.
 
    ![JavaScript-kod för verklig användare mätningar nyckeln](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Bild 2: Verkliga mätningar nyckeln och mätning JavaScript**
 
6.  Klicka på den **kopiera** för att kopiera JavaScript-kod. 

>[!IMPORTANT]
> Använd genererade JavaScript för den verkliga användaren mått funktionen ska fungera korrekt. Ändringar i det här skriptet eller skript som används av den verkliga användaren mått kan leda till oväntade funktionssätt.

## <a name="embed-the-code-to-an-html-web-page"></a>Bädda in kod till en HTML-webbsida

När du har fått nyckeln ROM, är nästa steg att bädda in den här kopierade JavaScript i en HTML-sida som användarna besöker. Redigera HTML kan göras på många olika sätt och använda olika verktyg och arbetsflöden. Det här exemplet visar hur du uppdaterar en HTML-sida för att lägga till det här skriptet. Du kan använda den här vägledningen för att anpassa det till HTML-källa management-arbetsflöde.

1.  Öppna HTML-sidan i en textredigerare
2.  Klistra in JavaScript-kod som du hade kopierat i tidigare steg i avsnittet BRÖDTEXT i HTML (kopierade koden är på rad 8 & 9, se figur 3).
 
    ![Bädda in Javascript-kod i webbsida för verklig användare mått](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Bild 3: Enkel HTML med inbäddade verkliga användaren mätningar JavaScript**

3.  Spara HTML-fil och värden som den på en webbserver ansluten till internet. 
4. Nästa gång den här sidan renderas i en webbläsare, JavaScript refererar till hämtas och skriptet körs mätning och rapportering av åtgärder.


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [verkliga användaren mått](traffic-manager-rum-overview.md)
- Läs [hur Traffic Manager fungerar](traffic-manager-overview.md)
- Lär dig mer om den [routning av nätverkstrafik metoder](traffic-manager-routing-methods.md) stöds av Traffic Manager
- Lär dig hur du [skapa en Traffic Manager-profil](traffic-manager-create-profile.md)

