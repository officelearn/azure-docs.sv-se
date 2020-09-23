---
title: Snabb start – hämta ett telefonnummer från Azure Communication Services
description: Lär dig hur du köper ett telefonnummer till kommunikations tjänster med hjälp av Azure Portal.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/09/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: 2743dc0164f604c9c5e033aacc3e58fae42a1fd2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948385"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Snabb start: Hämta ett telefonnummer med hjälp av Azure Portal

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Kom igång med Azure Communication Services genom att använda Azure Portal för att köpa ett telefonnummer.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [En Active Communication Services-resurs.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Hämta ett telefonnummer

Om du vill börja etablerings numren går du till kommunikations tjänst resursen på [Azure Portal](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Skärm bild som visar huvud sidan för en kommunikations tjänst resurs.":::

### <a name="getting-new-phone-numbers"></a>Hämtar nya telefonnummer

Navigera till bladet med telefonnummer på resurs-menyn.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Skärm bild som visar telefon Sidan för kommunikations tjänst resursen.":::

Tryck på `Get` knappen för att starta guiden. Guiden på `Phone numbers` bladet vägleder dig genom en serie frågor som hjälper dig att välja det telefonnummer som passar bäst för ditt scenario. 

Du måste först välja den `Country/region` plats där du vill etablera telefonnumret. När du har valt land/region måste du välja den `phone plan` som bäst motsvarar dina behov. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Skärm bild som visar vyn Hämta telefonnummer.":::

### <a name="select-a-phone-plan"></a>Välj en telefon plan

Att välja telefon plan delas upp i två steg: 

1. Valet av [siffer typ](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. Valet av [plan](../../concepts/telephony-sms/plan-solution.md#plans)

Vi erbjuder två nummer typer för närvarande: `Geographic` , och `Toll-free` . När du har valt en siffer typ kommer du att erbjudas flera planer som du kan välja mellan.

> [!NOTE]
> Vi stöder för närvarande bara att välja telefonnummer med inkommande eller utgående samtal. Du kan dock köpa ett telefonnummer med inkommande samtal aktiverat och sedan konfigurera det utgående anrops-ID: t så att det matchar numret för det inkommande-anropande-aktiverade telefonnumret (vad användarna ser när de anropar dem från kommunikations tjänst programmet).
> Detta gäller endast för tvåvägs anrop. Dubbelriktat SMS stöds internt.

I vårt exempel har vi valt en `Toll-free` siffer typ med `Outbound calling` planen.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Skärm bild som visar vyn Välj planer.":::

### <a name="declare-purpose"></a>Deklarera syfte

Sedan ber guiden dig att använda talet. Vi samlar in den här informationen för att tillämpa rätt bestämmelser om skatte-och kris samtal.

:::image type="content" source="../media/quickstart-search-and-acquire-bot-or-human.png" alt-text="Skärm bild som visar hur du väljer en robot eller mänsklig i förvärvs processen för telefonnummer.":::

Härifrån klickar du på `Next: Numbers` knappen längst ned på sidan för att anpassa de telefonnummer som du vill etablera.

### <a name="customizing-phone-numbers"></a>Anpassa telefonnummer

På `Numbers` sidan kommer du att anpassa de telefonnummer som du vill etablera.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Skärm bild som visar sidan med siffer val.":::

> [!NOTE]
> Den här snabb starten visar `Toll-free` siffer typs anpassnings flödet. Upplevelsen kan skilja sig något från om du har valt `Geographic` nummer typen, men slut resultatet blir detsamma.

Välj i `Area code` listan över tillgängliga rikt nummer och ange den kvantitet som du vill etablera och klicka sedan `Search` för att hitta siffror som uppfyller de valda kraven. De telefonnummer som motsvarar dina behov visas tillsammans med deras månads kostnad.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Skärm bild som visar sidan för sidnumrering med reserverade nummer.":::

> [!NOTE]
> Tillgängligheten beror på den siffer typ, plats och den plan som du har valt.
> Siffror är reserverade under en kort tid innan transaktionen upphör att gälla. Om transaktionen upphör att gälla måste du markera talen igen.

Klicka på `Next: Summary` knappen längst ned på sidan om du vill visa inköps sammanfattningen och placera din beställning.

### <a name="place-order"></a>Placera order

På sidan Sammanfattning visas siffer typ, funktioner, telefonnummer och total månads kostnad för att etablera telefonnumret.

> [!NOTE]
> Priserna som visas är de **månatliga återkommande avgifterna** som omfattar kostnaden för att leasa det valda telefonnumret till dig. Som inte ingår i den här vyn är **kostnaderna för betala per** användning som uppstår när du gör eller tar emot samtal. Pris listorna finns [här](../../concepts/pricing.md). Dessa kostnader är beroende av siffer typ och destinationer som kallas. Till exempel kan pris per minut för ett samtal från ett regionalt Seattle-nummer till ett regionalt nummer i New York och ett samtal från samma nummer till ett BRITTISKt mobil nummer vara olika.

Klicka slutligen längst `Place order` ned på sidan för att bekräfta.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Skärm bild som visar sammanfattnings sidan med nummer typ, funktioner, telefonnummer och den totala månads kostnaden som visas.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Hitta dina telefonnummer på Azure Portal

Gå till Azure Communication-resursen på [Azure Portal](https://portal.azure.com):

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Skärm bild som visar huvud sidan för en kommunikations tjänst resurs.":::

Välj fliken telefonnummer på menyn för att hantera dina telefonnummer.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Skärm bild som visar sidan telefonnummer till kommunikations tjänst resursen.":::

> [!NOTE]
> Det kan ta några minuter innan de etablerade talen visas på den här sidan.

## <a name="troubleshooting"></a>Felsökning

Vanliga frågor och problem:

- Endast USA och Kanada stöder köp av telefonnummer för tillfället. Detta baseras på fakturerings adressen för den prenumeration som resursen är kopplad till. För närvarande kan du inte flytta resursen till en annan prenumeration.

- När ett telefonnummer tas bort frigörs inte telefonnumret eller kan köpas in igen förrän fakturerings perioden är slut.

- När en kommunikations tjänst resurs tas bort, släpps de telefonnummer som är associerade med resursen automatiskt samtidigt.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig att:

> [!div class="checklist"]
> * Köp ett telefonnummer
> * Hantera ditt telefonnummer
> * Frisläpp ett telefonnummer

> [!div class="nextstepaction"]
> [Skicka ett SMS](../telephony-sms/send.md) 
>  [Kom igång med att anropa](../voice-video-calling/getting-started-with-calling.md)
