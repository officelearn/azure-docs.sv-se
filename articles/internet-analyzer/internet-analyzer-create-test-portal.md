---
title: Skapa ett Internet Analyzer-test med Portal | Microsoft-dokument
description: I den här artikeln kan du läsa om hur du skapar ditt första Internet Analyzer-test.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73501773"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Skapa ett Internet Analyzer-test med Portal (Förhandsversion)

Det finns två sätt att skapa en Internet Analyzer-resurs med hjälp av Azure-portalen eller med [CLI](internet-analyzer-cli.md). Det här avsnittet hjälper dig att skapa en ny Azure Internet Analyzer-resurs med hjälp av vår portalupplevelse.

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

Den offentliga förhandsversionen är tillgänglig för användning globalt. Datalagring är dock begränsad till *US West 2* under förhandsversionen.

## <a name="basics"></a>Grundläggande inställningar

1. Få åtkomst till förhandsversionen av Internet [Analyzer](internet-analyzer-faq.md)genom att följa **instruktionerna för hur deltar jag i förhandsversionen?**
2. På startsidan i [Azure-portalen](https://preview.portal.azure.com)klickar du på **+ Skapa en resurs**. Internet Analyzer är för närvarande endast tillgängligt från förhandsversionen av Azure-portalen.
3. Sök efter "Internet Analyzer" i fältet Sök på *Marketplace* på sidan **Ny.**
4. Klicka på **Internet Analyzer (förhandsgranskning)**. Kontrollera att utgivaren är *Microsoft* och att kategorin är *Nätverk.*
5. På sidan **Internet Analyzer (förhandsgranskning)** klickar du på **Skapa** för att öppna sidan **Skapa en Internet Analyzer.**
6. Ange följande konfigurationsinställningar för Internet Analyzer-resursen:

    * **Prenumeration:** Azure-prenumerationen som värd för den nya Internet Analyzer-resursen. ***Använd samma prenumerations-ID som används för att begära förhandsversion.***
    * **Resursgrupp:** Den Azure-resursgrupp som den nya Internet Analyzer-resursen skapas i. Om du inte har en befintlig resursgrupp kan du skapa en ny.
    * **Namn:** Namnet på den nya resursprofilen För Internet Analyzer.
    * **Region:** Den offentliga Azure-regionen som resursen kommer att skapas i. Under förhandsversionen är endast *US West 2* tillgängligt.

7. När du har angett dina profilinställningar klickar du på **Granska + skapa**.

## <a name="configuration"></a>Konfiguration

Att slutföra de grundläggande stegen är en förutsättning för att konfigurera ett test och bädda in JavaScript-klienten. När du har skapat en profil går du till **Inställningar > konfiguration** för att konfigurera ditt första test.

1. Ge ditt test ett namn i rutan **Testnamn.**
2. Lägg till en beskrivning för testet i fältet **Beskrivning.**
3. Klicka på **Konfigurera slutpunkt** - en flik visas från höger sida. Välj den typ av slutpunkt som du vill konfigurera- en enda Azure-region, flera Azure-regioner eller en anpassad slutpunkt.

    >
    ***Förkonfigurerade slutpunkter: kombinationer av enstaka och flera Azure-regioner***
    * Välj en region eller uppsättning regioner från en [förkonfigurerad lista över Azure-slutpunkter](internet-analyzer-faq.md).
    * Välj sedan den typ av program- eller innehållsleveransarkitektur som du vill utvärdera.
        * Single Azure-region: Site acceleration ([Azure Front Door](https://azure.microsoft.com/services/frontdoor/)), statisk innehållscache ( Azure[CDN för Microsoft](https://azure.microsoft.com/services/cdn/)), eller ingen.
        * Flera Azure-regioner: Platsacceleration ([Azure Front Door](https://azure.microsoft.com/services/frontdoor/)), DNS-styrning ( Azure Traffic[Manager](https://azure.microsoft.com/services/traffic-manager/))  

    ***Anpassade slutpunkter***
    * Följ instruktionerna på sidan [Skapa anpassad slutpunkt.](internet-analyzer-custom-endpoint.md)
    * Klistra in HTTPS-URL-platsen för enpixelbilden i portalen.
    >

4. Klicka på **Lägg till** om du vill lägga till slutpunkten i testet.
5. Upprepa steg 1-4 för att konfigurera din andra slutpunkt. Slutpunkt B mäts alltid i förhållande till slutpunkt A - när du konfigurerar slutpunkter bör du tänka på vilken slutpunkt som ska vara din testkontroll.
6. Klicka på knappen **Spara** för att spara testet. När du har sparat ett test kan du inte längre redigera slutpunkterna för ett visst test.
7. Markera de tester som du vill starta och klicka på **Starta test**. Detta kommer att ändra ***tillståndet*** för dina test (er) till ***Kör***. Du kan starta tester när som helst, men JavaScript-klienten måste bäddas in för att testet ska kunna börja samla in mätningar.
8. Lägg till fler tester när som helst. Observera att den unika JavaScript-klienten inte genereras förrän ett test har skapats.

## <a name="embed-client"></a>Bädda in klient

För att kunna starta ett test måste JavaScript-klienten bäddas in i webbprogrammet. När du har konfigurerat minst ett test klickar du på **Granska + skapa**, går till Inställningar > **konfiguration**och kopierar JavaScript-klienten. Specifika instruktioner finns på sidan [Embed Internet Analyzer Client.](internet-analyzer-embed-client.md)  

## <a name="next-steps"></a>Nästa steg

* Läs [vanliga frågor om Internet Analyzer](internet-analyzer-faq.md)
* Läs mer om hur du bäddar in [Internet Analyzer-klienten](internet-analyzer-embed-client.md) och skapar en [anpassad slutpunkt](internet-analyzer-custom-endpoint.md).
