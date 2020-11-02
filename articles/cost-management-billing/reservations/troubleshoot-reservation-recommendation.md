---
title: Felsöka Azure-reservationsrekommendationer
description: Den här artikeln hjälper dig att förstå och felsöka Azure-reservationsrekommendationer som visas i Azure-portalen.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: a3137d779908bf2791ca396068a8c9edf5d56739
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92492256"
---
# <a name="troubleshoot-azure-reservation-recommendations"></a>Felsöka Azure-reservationsrekommendationer

Den här artikeln hjälper dig att förstå och felsöka Azure-reservationsrekommendationer som visas i Azure-portalen. Du kanske inte ser några rekommendationer eller så ser du rekommendationer som inte stämmer överens med dina förväntningar. Du kanske till exempel redan har en reserverad instans för en angiven VM-konfiguration. Du kanske förväntar dig att se en rekommendation för en liknande VM-konfiguration.

## <a name="symptoms"></a>Symtom

1. Logga in på [Azure-portalen](https://portal.azure.com/) och gå till **Reservationer** .
2. Välj **+ Lägg till** och välj sedan en produkt.
3. På fliken **Rekommenderat** kanske du inte ser några rekommendationer eller så ser du rekommendationer som inte stämmer överens med dina förväntningar.

## <a name="cause"></a>Orsak

Listan med rekommenderade produkter genereras enligt den mängd användning som du har för ditt omfång (delat eller enskilt) jämfört med kostnaden för en reservation för produkten. Om en rekommendationsmotor identifierar besparingar rekommenderar att du köper en produkt. Men när motorn inte identifierar några besparingar rekommenderar den inte någon produkt.

När du kör en resurs med en specifik konfiguration finns det ingen garanti för att du sparar pengar genom att köpa en reservation för den konfigurationen. Du kan t. ex. ha sporadisk användning. I så fall kanske den totala kostnaden för reservationen inte sparar pengar under reservationsperiodens livstid.

Det är också viktigt att förstå hur valet av omfång påverkar rekommendationer. När omfånget är inställt på **Delad** visar rekommendationer i listan reserverade instanser där Azure hittar besparingar för hela registreringen som är associerad med faktureringsprenumerationen. När omfånget är inställt på **Enskilt** gäller rekommendationerna i listan endast för resurser som körs i prenumerationen. Det är möjligt att vissa VM-storlekar rekommenderas för ett område men inte för ett annat. Du kan till exempel ha samlat användning av **Standard_B1ls** i din registrering som är tillräckligt hög för att motivera kostnaden för att köpa en reservation i registreringsomfånget. Men en enda prenumeration i registreringen kanske inte har tillräckligt med användning för att motivera kostnaden för att köpa en reservation i omfånget. Om du ändrar omfattningen från **Delad** och **Enskild** kan olika rekommendationer skapas.

Azure kan rekommendera att du köper en reservation för vissa perioder och inte för andra, utifrån de kostnadsbesparingar som identifierats. Mer specifikt har treåriga perioder större rabatter än ettåriga perioder. Det är mer troligt att Azure kommer att hitta besparingar för en treårsperiod än för en ettårsperiod.

Om du vill veta varför Azure rekommenderar en speciell resursstorlek och kvantitet väljer du **&lt;Kvantitet&gt; Visa information** för en djupgående visualisering som visar potentiella besparingar över tid.

:::image type="content" source="./media/troubleshoot-reservation-recommendation/see-details-link.png" alt-text="Exempel som visar reservationsrekommendationen ser du när du väljer länken Visa information" lightbox="./media/troubleshoot-reservation-recommendation/see-details-link.png" :::

## <a name="solution"></a>Lösning

Du kan köpa valfri reservationskvantitet för en period som du önskar. Att köpa en reservation med en kvantitet och en period som skiljer sig från rekommendationen kommer troligen att leda till mindre än optimala besparingar och optimal användning.

## <a name="next-steps"></a>Nästa steg

- Mer information om reservationsrekommendationer finns i [Rekommendationer för reservationsköp](determine-reservation-purchase.md).
