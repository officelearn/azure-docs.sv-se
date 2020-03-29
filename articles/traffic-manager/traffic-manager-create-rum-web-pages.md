---
title: Verkliga användarmätningar med webbsidor - Azure Traffic Manager
description: I den här artikeln får du lära dig hur du konfigurerar webbsidor för att skicka riktiga användarmätningar till Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 927d774ee30a291607a8a47fc2fd6878c1bc6fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938685"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Så här skickar du verkliga användarmätningar till Azure Traffic Manager med webbsidor

Du kan konfigurera dina webbsidor så att verkliga användarmått skickas till Traffic Manager genom att skaffa en RUM-nyckel (Real User Measurements) och bädda in den genererade koden till webbsidan.

## <a name="obtain-a-real-user-measurements-key"></a>Hämta nyckeln Real User Measurements

De mått du gör och skickar till Traffic Manager från klientprogrammet identifieras av tjänsten med hjälp av en unik sträng, kallad **RUM-nyckeln (Real User Measurements).** Du kan hämta en RUM-nyckel med Azure-portalen, ett REST API eller genom att använda PowerShell eller Azure CLI.

Så här hämtar du RUM-nyckeln med Azure-portalen:
1. Logga in på Azure Portal från en webbläsare. Om du inte redan har ett konto kan du registrera dig för en kostnadsfri utvärderingsmånad.
2. Leta efter namnet på Traffic Manager-profilen som du vill modifiera och klicka sedan på Traffic Manager-profilen i resultaten som visas.
3. Klicka på **Riktiga användarmått** under **Inställningar**i profilbladet Traffic Manager .
4. Klicka på **Generera nyckel** för att skapa en ny RUM-nyckel.
 
   ![Generera nyckeln Verkliga användarmätningar](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Bild 1: Nyckelgenerering för verkliga användarmätningar**

5. Bladet visar nu den RUM-nyckel som genererats och ett JavaScript-kodavsnitt som måste bäddas in på HTML-sidan.
 
    ![Javascript-kod för nyckeln Riktiga användarmätningar](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Bild 2: Real User Measurements Key och Mätning JavaScript**
 
6. Klicka på knappen **Kopiera** om du vill kopiera JavaScript-koden. 

>[!IMPORTANT]
> Använd den genererade JavaScript för riktiga användarmätningar funktionen för att fungera korrekt. Alla ändringar av skriptet eller skripten som används av riktiga användarmätningar kan leda till oförutsägbart beteende.

## <a name="embed-the-code-to-an-html-web-page"></a>Bädda in koden på en HTML-webbsida

När du har fått RUM-tangenten är nästa steg att bädda in det kopierade JavaScript-programmet på en HTML-sida som slutanvändarna besöker. Redigering av HTML kan göras på många sätt och med hjälp av olika verktyg och arbetsflöden. I det här exemplet visas hur du uppdaterar en HTML-sida för att lägga till skriptet. Du kan använda den här vägledningen för att anpassa den till html-källhanteringsarbetsflödet.

1.  Öppna HTML-sidan i en textredigerare
2.  Klistra in JavaScript-koden som du hade kopierat i det tidigare steget till BODY-avsnittet i HTML-koden (den kopierade koden finns på rad 8 & 9, se bild 3).
 
    ![Bädda in Javascript-kod på webbsidan för riktiga användarmätningar](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Bild 3: Enkel HTML med inbäddade Real User Measurements JavaScript**

3.  Spara HTML-filen och vara värd för den på en webbserver som är ansluten till Internet. 
4. Nästa gång den här sidan återges i en webbläsare hämtas JavaScript-referensen och skriptet kör mätnings- och rapporteringsåtgärderna.


## <a name="next-steps"></a>Nästa steg
- Läs mer om [riktiga användarmätningar](traffic-manager-rum-overview.md)
- Läs om [hur Traffic Manager fungerar](traffic-manager-overview.md)
- Läs mer om [de trafikroutningsmetoder](traffic-manager-routing-methods.md) som stöds av Traffic Manager
- Läs om hur du [skapar en Traffic Manager-profil](traffic-manager-create-profile.md)

