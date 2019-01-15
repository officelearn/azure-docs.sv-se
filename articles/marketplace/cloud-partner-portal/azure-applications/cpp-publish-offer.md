---
title: Publicera erbjudande för Azure-program – Azure Marketplace | Microsoft Docs
description: Beskriver processen och stegen för att publicera ett erbjudande för Azure-program på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: pbutlerm
ms.openlocfilehash: 0a18bcf9f9e1217b674bda5f5ae698908f1911a8
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262922"
---
# <a name="publish-azure-application-offer"></a>Publicera erbjudande för Azure-program

När du har skapat ett erbjudande genom att tillhandahålla informationen om den **nytt erbjudande** kan du publicera erbjudandet. Välj **publicera** att starta publiceringsprocessen.

I följande diagram visas de viktigaste stegen i publiceringsprocessen i ett erbjudande ”gå live”.

![Publicera erbjudande-steg](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Detaljerad beskrivning av publicering av steg

I följande tabell listar och beskriver hur publicering och ger en Uppskattad tidsåtgång för att slutföra varje steg.  Tider beräkningar i ”dagar” definieras som arbetsdagar utesluta helger och semestrar.

|  **Publiceringen**           | **Time**    | **Beskrivning**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Verifiera förutsättningar         | < 15 min    | Ger information och erbjuder inställningarna verifieras.                        |
| Verifiera inställningar som har påverkat intäkter | < 15 min  |      |
| Certifiering                  | < 1 dag     | Erbjudandet analyseras av Azure-certifiering-teamet. Erbjudandet genomsöks efter virus, skadlig kod, säkerhet och säkerhetsproblem. Erbjudandet kontrolleras för att se att den uppfyller alla urvalskriterier. Mer information finns i [krav](./cpp-prerequisites.md). Feedback tillhandahålls om ett problem hittas. |
| Test Drive-verifiering          | < 2 timmar   | (Valfritt) Om en Test Drive finns verifierar Microsoft att den kan distribueras och replikeras.  |
| Paketering och registrering för lead-generering | < 1 timme  | Erbjudandets tekniska resurser som är packade för kundens användning och lead-system konfigureras och distribueras. |
|  Utgivaren signering             |  Manuell    | Sista publisher granska och bekräfta innan erbjudandet lanseras. Erbjudandet är nu tillgänglig för förhandsgranskning.  Du kan distribuera ditt erbjudande i de valda prenumerationerna (i erbjudandet information steg) för att kontrollera att den uppfyller dina krav.  När du har kontrollerat erbjudandet, Välj **Go Live** så att ditt erbjudande kan flytta till nästa steg. |
| Microsoft-granskning                | 7 – 14 dagar * | Microsoft granskar dina Azure-program holistiskt och e-postmeddelande om problem upptäcks.  * Sällan, kan det här steget ta längre tid än 14 dagar, beroende på komplexiteten i programmet och problem som upptäckts.  |
| Live                           | < 1 dag | Erbjudandet är publicerat, replikeras till de angivna regionerna och göras tillgängliga för allmänheten. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |
 
Du kan övervaka publiceringsprocessen i den **Status** fliken för ditt erbjudande i partnerportalen i molnet.

![Fliken status för ett erbjudande för Azure-app](./media/offer-status-tab.png)

När du har slutfört processen för att publicera ditt erbjudande visas i den [Microsoft Azure Marketplace-programkategori](https://azuremarketplace.microsoft.com/marketplace/apps/).


## <a name="errors-and-review-feedback"></a>Fel och granska feedback

Förutom att visa publiceringsstatus för ditt erbjudande i **Status** fliken visas även felmeddelanden och feedback från den **Microsoft granska** steg.  Granska problem är normalt refereras till som pull-begäran (PR).  Varje pull-begäran är länkad till en online Visual Studio Team Services (VSTS, bytt namn till [Azure DevOps](https://azure.microsoft.com/services/devops/)) objekt som innehåller information om problemet.  Följande bild visar ett exempel på en granska PR-referens.  För mer komplexa situationer, granskning och support team kan via e-post. 

![Status för fliken visas granska feedback](./media/status-tab-ms-review.png)

Du måste åtgärda varje rapporterad fråga innan erbjudandet fortsätter via publiceringsprocessen.  Följande diagram illustrerar hur den här feedbackprocessen relaterar till publiceringsprocessen.

![Publicera steg med VSTS-feedback](./media/pub-flow-vsts-access.png)


### <a name="vsts-access"></a>VSTS-åtkomst

Utgivare måste beviljas rätt behörighet för om du vill visa VSTS-objekt som refereras i Granska feedback.  Annars kan nya utgivare ta emot en `401 - Not Authorized` svar sidan.  Utför följande steg för att begära åtkomst till erbjudandet granska VSTS systemet:

1. Samla in följande information:
    - Ditt namn på utgivare och -ID
    - Erbjudandetyp (Azure app), erbjuder namnet och SKU-ID
    - Pull-begäran länka till exempel: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`  Den här URL: en kan hämtas från meddelandet eller adressen till sidan 401-svar.
    - E-postadresserna för personerna från organisationen publishing som du vill bevilja åtkomst till.  Dessa bör inkludera ägare-adresser som du angav när du registrerar en utgivare på partnerportalen i molnet.
2. Skapa ett supportärende.  I namnlisten i Cloud Partner Portal, väljer du den **hjälpa** knappen och välj sedan **Support** på menyn.  Du default web browser bör starta och gå till Microsoft support incident sidan Ny.  (Du kan behöva logga in först.)
3. Ange den **problemtyp** som **marketplace onboarding** och **kategori** som **problem med anslutningen**och välj sedan **Start begäran**.

    ![Stöd för biljett kategori](./media/support-incident1.png)

4. I **steg 1 av 2** , ange din kontaktinformation och välj **Fortsätt**.
5. I **steg 2 av 2** anger du ett incidentrubrik (till exempel `Request VSTS access`) och ange den information du samlade in i det första steget (ovan).  Läsa och godkänna avtalet och välj sedan **skicka**.

Om incidentskapande, visas en bekräftelsesida.  Bekräftelseinformationen för din referens sparas.  Microsoft-supporten ska svara på din förfrågan inom ett par arbetsdagar.


## <a name="next-steps"></a>Nästa steg

När en Azure-app har publicerats kan du [uppdatera befintligt erbjudande](./cpp-update-existing-offer.md) så att företag eller tekniska behoven. 
