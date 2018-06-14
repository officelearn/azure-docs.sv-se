---
title: Icke-tekniska krav för att skapa ett erbjudande för Azure Marketplace | Microsoft Docs
description: Förstå kraven för att skapa och distribuera ett erbjudande på Azure Marketplace för andra att köpa.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 3dae463b-8f48-4f52-8fa8-4e3975f09f43
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/18/2016
ms.author: mbaldwin
ms.openlocfilehash: 5c30e62bf345843fe83b3f17b728e1a937d19ce3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
ms.locfileid: "29940049"
---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Allmänna krav för att skapa ett erbjudande för Azure Marketplace
Förstå de krav för Allmänt, business-process-elementbaserade som behövs för att gå vidare med processen erbjudandet.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Se till att du är registrerad som en säljare med Microsoft
Detaljerade anvisningar om hur du registrerar ett säljare-konto med Microsoft, gå till [skapande av konton och registrering](marketplace-publishing-accounts-creation-registration.md).

* **Om ditt företag har redan registrerats som en säljare i Dev Center och du vill skapa ett nytt erbjudande** och sedan logga in på publicering portalen med samma id för e-post med vilken Dev Center har registrerats. Det här steget krävs för att portalen Dev Center och publicering är kopplade till varandra.
* **Om ditt företag har redan registrerats som en säljare i Dev Center och du vill redigera en befintlig erbjudande** sedan antingen inloggning för publicering portalen administratörskontot eller med ett konto som har lagts till som medadministratör i publicering portal. Steg för att lägga till en medadministratör konto anges nedan.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>Steg för att lägga till en medadministratör i Publishing Portal
Administratörer för publicering portal kan lägga till andra medlemmar i företaget, som arbetar på programmet som en medadministratör i publicering portal. **Förutsatt att du är administratör,** nedan följer stegen för att lägga till en ko-administratör.

> [!NOTE]
> För nya användare innan du lägger till en medadministratör i publicering portal, se till att du har skapat minst ett program i publicering portal. Detta är den **UTGIVARE** fliken visas endast när du har skapat minst ett program i publicering portal.
> 
> 

1. Se till att medadministratör e-post-id är ett Microsoft-account(MSA). Om inte, registrera den som en MSA med detta [länk](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Kontrollera att det finns minst ett program under administratörskontot innan du försöker lägga till en ko-administratör.
3. När stegen ovan är klar, logga in på publicering Företagsportalen med medadministratör e-post-id och sedan loggar ut.
4. Nu logga in på publicering Företagsportalen med e-post-ID: t för administratören.
5. Navigera till utgivare -> ditt konto -> Välj administratörer -> Lägg till medadministratör (skärmbilden nedan)
   
    ![Rita](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. Se till att e-post-ID: n som anges i de olika stegen i publiceringsprocessen (t.ex. Dev Center, publicera portal) övervakas för all kommunikation från Microsoft.
7. För registrering av Dev Center Undvik att använda ett konto som är associerade med en enskild person. Detta rekommenderas för beroendet från en person.
8. Om du står inför eventuella problem med registreringen Dev Center sedan Utlös en biljett med den här [länk](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>Steg för att ta bort en medadministratör i publicering portal
**Förutsatt att du är administratör,** nedan följer stegen för att ta bort en ko-administratör.

1. Logga in på publicering Företagsportalen med e-post-ID: t för administratören.
2. Gå till **utgivare** -> Välj ditt konto -> **administratörer** -> **Medadministratörer**.
3. Klicka på den **X** knappen bredvid medadministratör du vill ta bort totalvärde (skärmbilden nedan).
   
    ![Rita](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> Du behöver inte slutföra skatt och bank företagsinformation om du planerar att publicera endast kostnadsfria erbjudanden (eller använda din egen licens).
> 
> Företagets registreringen måste utföras för att komma igång. Men när ditt företag fungerar på skatt och bank informationen i Microsoft Developer-konto, utvecklare kan börja arbeta om hur du skapar avbildningen av virtuella datorn i den [Publiceringsportal](https://publish.windowsazure.com), tas den certifierade och testa dem i Azure mellanlagringsmiljön. Fullständig säljare konto godkännande behöver du bara för det sista steget i att publicera ditt erbjudande på Azure Marketplace.
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Skaffa en Azure-prenumeration ”betalning per användning”
Det här är den prenumeration som du använder för att skapa VM-avbildningar och bilder att överlämna den [Azure Marketplace](https://azure.microsoft.com/marketplace/). Om du inte har en befintlig prenumeration sedan du logga in på https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>”Säljer-från-länder
> [!WARNING]
> För att sälja dina tjänster på Azure Marketplace, måste du se till att registrerade entiteten är från en av de godkända ”säljer-från-länderna. Den här begränsningen är beloppet och skatt skäl. Aktivt ser vi att expandera den här listan över länder i den nära framtiden, så Håll ögonen öppna. Den fullständiga listan finns i avsnittet 1b av den [Azure Marketplace deltagande principer](http://go.microsoft.com/fwlink/?LinkID=526833).
> 
> 

## <a name="next-steps"></a>Nästa steg
När icke-tekniska kraven är uppfyllda, nästa är erbjudandet specifika tekniska krav. Klicka på länken till artikeln för den typ av respektive erbjudande som du vill skapa för Azure Marketplace.

* [Tekniska krav för VM](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Lösning mallen tekniska krav](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Se också
* [Komma igång: hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)

