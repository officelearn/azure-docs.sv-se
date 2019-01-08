---
title: Icke-tekniska krav för att skapa ett erbjudande för Azure Marketplace | Microsoft Docs
description: Förstå kraven för att skapa och distribuera ett erbjudande på Azure Marketplace för andra att köpa.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 3dae463b-8f48-4f52-8fa8-4e3975f09f43
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/18/2016
ms.author: hascipio
ROBOTS: NOINDEX
ms.openlocfilehash: 4b925522186d2d9ae537431c1d96d39b107ad967
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073177"
---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Allmänna krav för att skapa ett erbjudande för Azure Marketplace
Förstå de krav för Allmänt, process-företagsanpassat som behövs för att gå vidare med erbjudandet skapandeprocessen.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Se till att du är registrerad som en försäljning med Microsoft
Detaljerade anvisningar om hur du registrerar en försäljarkonto med Microsoft finns i [skapande och registrering](marketplace-publishing-accounts-creation-registration.md).

* Om ditt företag har redan registrerats som en försäljning i Dev Center och du vill skapa ett nytt erbjudande, logga sedan in på publiceringen med samma ID för e-post med vilken Dev Center registreringen är klar. Det här steget krävs för att portalen Dev Center och publicering är kopplad till varandra.
* Om ditt företag har redan registrerats som en försäljning i Dev Center och du vill redigera ett befintligt erbjudande och sedan logga antingen in publiceringen portal med administratörskontot eller med ett konto som har lagts till som en coadmin i publiceringen portal. Steg för att lägga till ett coadmin konto anges nedan.

## <a name="steps-to-add-a-coadmin-in-the-publishing-portal"></a>Hur du lägger till en coadmin i Publiceringsportalen
Administratörer för publicering portal kan lägga till andra medlemmar i företaget som arbetar på programmet som en coadmin i publiceringen portal. **Om vi antar att du är administratör,** nedan följer stegen för att lägga till en coadmin.

> [!NOTE]
> För nya användare innan du lägger till en medadministratör i publiceringen portal, se till att du har skapat minst ett program i publiceringen portal. Detta är obligatoriskt som den **UTGIVARE** fliken visas bara när du har skapat minst ett program i publiceringen portal.
> 
> 

1. Kontrollera att coadmin e-post-ID: T är en Microsoft-account(MSA). Om den inte registrera den som en MSA som använder det här [länk](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Kontrollera att det finns minst ett program under administratörskontot innan du försöker lägga till en coadmin.
3. När stegen ovan är klar loggar du in att publiceringen portal med coadmin e-ID och sedan logga ut.
4. Nu logga in på att publiceringen med admin e-post-ID.
5. Gå till utgivare -> Välj ditt konto -> administratörer -> Lägg till coadmin (skärmbilden nedan)
   
    ![Rita](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. Se till att e-post-ID: n som anges på de olika stegen i publiceringsprocessen (till exempel Dev Center, Publiceringsportal) övervakas för all kommunikation från Microsoft.
7. Undvik att använda ett konto som är associerade med en enskild person för registrering av Dev Center. Den här förslag tar bort beroendet av enskilda användare.
8. Om du får problem med registrering av Dev Center du öka en biljett som använder det här [länk](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-coadmin-in-the-publishing-portal"></a>Steg för att ta bort en coadmin i publiceringen portal
**Om vi antar att du är administratör,** nedan följer stegen för att ta bort en coadmin.

1. Logga in på att publiceringen med admin e-post-ID.
2. Gå till **utgivare** -> Välj ditt konto -> **administratörer** -> **Medadministratörer**.
3. Klicka på den **X** knappen bredvid coadmin som du vill ta bort summa (skärmbilden nedan).
   
    ![Rita](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> Du behöver inte utföra skatte- och bank företagsinformation om du planerar att publicera endast kostnadsfria erbjudanden (eller Använd din egen licens).
> 
> Företagets registreringen måste utföras för att komma igång. Men även om ditt företag fungerar på skatte- och bank informationen i Microsoft Developer-konto, utvecklare kan börja arbeta om hur du skapar avbildningen av virtuella datorn i den [Publiceringsportalen](https://publish.windowsazure.com), få den certifierad och testning den i mellanlagringsmiljön i Azure. Du behöver fullständig försäljning konto godkännandet endast för det sista steget för att publicera ditt erbjudande på Azure Marketplace.
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Skaffa en Azure ”betala per användning”-prenumeration
Det här värdet är den prenumeration som du använder för att skapa dina VM-avbildningar och överlämna avbildningar till den [Azure Marketplace](https://azure.microsoft.com/marketplace/). Om du inte har en befintlig prenumeration logga sedan på https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>”” Försäljningsländer
> [!WARNING]
> Kontrollera för att kunna sälja dina tjänster på Azure Marketplace, att registrerade entiteten är från en av de godkända ”” försäljningsländer. Den här begränsningen är payout och skatt skäl. Vi letar aktivt så Håll ögonen öppna om du vill expandera den här listan över länder inom en snar framtid. En fullständig lista finns i avsnittet 1b av den [deltagandepolicyer för Azure Marketplace](https://go.microsoft.com/fwlink/?LinkID=526833).
> 
> 

## <a name="next-steps"></a>Nästa steg
När icke-tekniska krav är uppfyllda, är nästa erbjudandet specifika tekniska krav. Klicka på länken till artikeln för den typ av respektive erbjudande som du vill skapa för Azure Marketplace.

* [Tekniska krav för virtuell dator](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Lösningen mall tekniska krav](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Se också
* [Komma igång: Hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)

